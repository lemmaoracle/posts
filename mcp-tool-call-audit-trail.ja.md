---
slug: "mcp-tool-call-audit-trail"
date: "2026.08.26"
category: "Technical"
audience: technical
section: "Essays"
title: "MCPツール呼び出しの監査証跡を、あとから検証できる形で残す"
abstract: "2026年8月、Splunk MCP Server に CVSS 9.1 の逆シリアル化RCE（CVE-2026-76404）が公表されました。前提は Splunk admin ロールです。認証もロール制御も通ったあとに任意コマンドが走ります。OAuth と RBAC は呼び出しの前までしか効かず、実行後に残る記録が本物かどうかは別の問題です。MCPツール呼び出しを1件ずつコミットメント付きで登録し、第三者が API キーなしで照合できるようにするまでを、実際に動かしたコードで示します。"
tags:
  - mcp
  - audit-trail
  - provenance
  - zk-proof
  - verifiable-ai
relatedLinks:
  - label: "Lemma Dashboard — 5分クイックスタート"
    href: "https://lemma.frame00.com/ja/blog/dashboard-quickstart/"
  - label: "@lemmaoracle/sdk (npm)"
    href: "https://www.npmjs.com/package/@lemmaoracle/sdk"
  - label: "@lemmaoracle/mcp (npm)"
    href: "https://www.npmjs.com/package/@lemmaoracle/mcp"
  - label: "用語集"
    href: "https://lemma.frame00.com/ja/glossary/"
---

MCPツール呼び出しを1件ずつコミットメント付きで登録し、第三者があとから API キーなしで照合できる状態を作ります。載せている出力は手元で実行した結果です。ハンドラに差し込む「5.」だけは、お使いの MCP サーバー実装に合わせて書き換える前提の骨組みです。

## CVE-2026-76404 と、公開MCPサーバーの実態

2026年8月19日、Splunk が [SVD-2026-0808](https://advisory.splunk.com/advisories/SVD-2026-0808) を公開し、[CVE-2026-76404](https://nvd.nist.gov/vuln/detail/CVE-2026-76404) が採番されました。CVSS 3.1 のベーススコアは 9.1（Critical）、ベクタは `AV:N/AC:L/PR:H/UI:N/S:C/C:H/I:H/A:H`、CWE-502（信頼できないデータの逆シリアル化）です。認証情報を扱うコンポーネントが、保存済みデータを期待する型かどうか確認せずに逆シリアル化していました。影響を受けるのは 1.2.1 未満、修正版は 1.2.1。Splunk 側の緩和策は「Splunk MCP Server app を無効化するか削除する」です。

ベクタの `PR:H` に注目してください。このRCEには Splunk の admin ロールが要ります。認証は通っています。ロール制御も通っています。そのうえでOSコマンドが走ります。

母集団のほうも状況が見えてきました。2026年7月31日に arXiv に投稿された [Exposed by Design: A Dynamic Security Assessment of Internet-Facing MCP Servers at Scale](https://arxiv.org/abs/2608.00150)（Nicolás Padilla）は、公開インターネット上で 21,000 を超える MCP サーバーインスタンスが観測できると報告しています。うち本番稼働と確認できたのが 640 台、そのうち 414 台を動的に監査した結果、91.8% が OAuth 認証を持っていませんでした。確認済みサーバー群の中には、アクセス制御なしでシェル実行を提供するツールインスタンスが 687 個あります。この 91.8% は動的監査した 414 台に対する割合で、640 台に対する割合ではありません。687 もサーバー台数ではなくツールインスタンス数です。

プロトコル側も動いています。2026年8月13〜14日にソウルで [MCP Dev Summit Seoul](https://events.linuxfoundation.org/mcp-dev-summit-seoul/)（Agentic AI Foundation 主催、Linux Foundation のイベント基盤）が開かれ、STDIO トランスポートの設計が主要な論点になりました。開催直前の[解説記事](https://forkast.news/the-model-context-protocol-reaches-a-security-inflection-point/)は、プロトコル自体を作り直すのか、開発者側の回避策で凌ぐのか、という対立軸で状況を整理しています。

## OAuth と RBAC は呼び出しの前までしか効かない

3件が指す方向は同じですが、そこから出てくる結論は「認証を強くしよう」だけではありません。

OAuth も RBAC も、呼び出しが起きる前に効く仕組みです。誰に呼ばせるか、どのツールを見せるか、どのスコープを許すか。ここを固めきったあとに、残る問題があります。

呼び出しが終わったあと「誰が・いつ・どのツールを・どんな引数で呼んだか」を確かめる先は、たいていMCPサーバーが動いているホスト上のログファイルです。そして CVE-2026-76404 が示したのは、そのホスト上で任意コマンドが実行されうる状態でした。任意コマンドが走ったホストのログファイルは、そのインシデントの証拠としては使えません。追記も削除も書き換えも、同じ権限でできてしまうからです。ログ転送を挟んでも、転送前に書き換えられていないことは転送先には分かりません。

アクセス制御と証跡の真正性は、別々に手当てする必要がある別の問題です。前者は「起こさせない」、後者は「起きたことを、あとから誰でも同じ結論に到達できる形で残す」。この記事が扱うのは後者です。

## 設計 — ツール呼び出し1件を1ドキュメントにする

MCPサーバーのツールハンドラを薄いラッパーで包み、呼び出しが1件終わるたびに次の2つに分けます。

- 手元に残すもの — 呼び出しの中身そのもの（引数、結果、実行者）と、コミットメントに使ったブラインディング係数。
- Lemma に登録するもの — その中身から計算したハッシュとコミットメントだけ。平文は送りません。

照合は**コミットメントの root** で行います。監査する側は手元の記録とブラインディング係数から root を再計算し、Lemma に登録されている `commitmentRoot` と突き合わせます。一致しなければ、記録は登録後に変わっています。

ここで `docHash` は使えません。`docHash` は暗号文の SHA3-256 で、暗号化には毎回新しい一時鍵と IV が使われるため、同じ平文からでも値が変わります。`docHash` はドキュメントを引くための識別子であって、内容の照合には使えない、と切り分けてください。

コミットメントは改ざんを防ぎません。防ぐのはアクセス制御の仕事です。コミットメントがするのは、改ざんを検知できるようにすることだけです。登録された記録が事実である保証もしません。証明できるのは「登録した時点の値から変わっていない」という一点で、この線引きを曖昧にした監査証跡は、あとで使いものになりません。

## 実装

`@lemmaoracle/sdk` を使います。関数型のスタイルで、クライアントは各関数の第1引数として渡します。

```bash
npm install @lemmaoracle/sdk @lemmaoracle/spec
```

### 1. 記録する1件を組み立てる

```ts
import {
  create,
  encrypt,
  commitDeep,
  derivePublicKey,
  documents,
} from "@lemmaoracle/sdk";

const client = create({
  apiBase: "https://workers.lemma.workers.dev",
  apiKey: process.env.LEMMA_API_KEY,
});

const call = {
  ts: "2026-08-26T04:12:07.881Z",
  server: "splunk-mcp",
  tool: "run_saved_search",
  actor: "agent:ops-bot",
  argsHash: "0x2f1c…",
  outcome: "ok",
};
```

引数そのものを入れるか、引数のハッシュだけにするかは運用次第です。上の例では `argsHash` にしています。中身を丸ごとコミットしたい場合は `call` にそのまま入れて構いません。どちらにせよ Lemma には平文が渡りません。

### 2. 暗号化して docHash と cid を得る

`encrypt` は ECIES（secp256k1 + HKDF-SHA256 + AES-256-GCM）でペイロードを暗号化し、暗号文・`docHash`・`cid` を返します。暗号文は戻り値として手元に返るだけで、送信はされません。保存先はご自身で決めてください。

```ts
const holderKey = derivePublicKey(process.env.HOLDER_PRIVATE_KEY!);
const enc = await encrypt(client, { payload: call, holderKey });
// enc.docHash, enc.cid, enc.ciphertext, enc.algorithm
```

```text
docHash:   0x071d25a15f5e6771be6ced522f44b6590409586566bfa1347e348979d4d63bef
cid:       bafkreifvzcon6xhespbxbpltrn745k6zzbjwt5t5fzokygbbinhnceo3jy
algorithm: aes-256-gcm
```

`docHash` は暗号文の SHA3-256、`cid` は CIDv1-raw です。前述のとおり、この2つは同じ入力からでも実行ごとに変わります。

### 3. 照合できるコミットメントを作る

`commitDeep` はネットワークI/Oのないローカル処理です。オブジェクトを JSON パス単位の葉に分解し、Poseidon の Merkle ツリーを組みます。

ブラインディング係数は呼び出し側で作って渡します。SDK には `commit(value)` もありますが、こちらは内部で係数を生成して外から渡す口がないため、あとから同じ root を再計算できません。監査証跡には `commitDeep` を使ってください。

```ts
import crypto from "node:crypto";

// 31バイト。32バイトだと BN254 の剰余体を超えうる
const randomness = crypto.randomBytes(31).toString("hex");

const c = commitDeep(call, { randomness });
// c.root, c.leaves, c.randomness, c.depth,
// c.inclusionProofs, c.leafPreimages
```

```text
root:   0x16466efede767e5c6d584d863a8947a50ad98c99ea3496169e4b3277129b76ad
depth:  3
leaves: 6
leafPreimages[0]: {
  "name": "$[\"actor\"]",
  "value": "s:agent:ops-bot",
  "nameHash":  "0xd03b3c2c24930303554989293dc25ef490dfea2e5afe635c7511c40b58ae9d7",
  "valueHash": "0x16cc9247c92fd4b89531aed54174e4ebb6164afb1ebccbe6c77b56564134b42d",
  "blindingHash": "0x3d9f0a51c7b48e26f1a05d3c8b7e42906fd15a83c26b4e0197df3a5c8b1e64"
}
```

葉の名前が `$["actor"]` という JSON パス形式になっているのは、ネストした値も同じ規則で葉にできるようにするためです。葉が1枚ずつ独立していることにも意味があります。あとで「`actor` だけを開示する」「`outcome` が `ok` であることだけを示す」といった部分開示に進めます。ドキュメント全体を1つのハッシュで固めてしまうと、この道が閉じます。

`randomness` を失うと root を再計算できず、証跡としての価値がなくなります。暗号文と一緒に、同じ耐久性で保管してください。

### 4. Lemma に登録する

```ts
await documents.register(client, {
  docHash: enc.docHash,
  cid: enc.cid,
  issuerId: "mcp-gateway", // 記録した主体（このラッパー）
  subjectId: "agent:ops-bot", // 呼び出した主体
  commitments: {
    scheme: "poseidon",
    root: c.root,
    leaves: c.leaves,
    randomness: c.randomness,
  },
  revocation: { scheme: "none", root: "0x" + "0".repeat(64) },
});
// → { status: "registered", docHash: "0x071d…" }
```

`commitDeep` の戻り値には `scheme` が入っていないので、`commitments` を組むときに `"poseidon"` を足します。`revocation` は必須フィールドです。ツール呼び出しの記録を失効させる運用がないなら、上のようにゼロ値を入れておきます。

`schema` は省略可能です。渡さなければ、サーバーは登録済みの `passthrough-v1` スキーマ（入力をそのまま正規化する）で保存します。単純な監査記録ならこれで十分です。型付きの正規化が必要なときだけ `schemas.register` でスキーマを登録し、その ID を渡してください。

### 5. ツールハンドラに差し込む

記録は `finally` で行います。ハンドラが例外で落ちた呼び出しこそ、あとから追いたい対象だからです。記録の失敗はツール応答に波及させません。監査のための仕組みが可用性を下げると、真っ先に外されます。

```ts
const withAuditTrail =
  (name: string, handler: Handler): Handler =>
  async (args) => {
    const startedAt = new Date().toISOString();
    const argsHash = await sha256Hex(JSON.stringify(args));
    let outcome = "error";

    try {
      const result = await handler(args);
      outcome = result.isError ? "error" : "ok";
      return result;
    } finally {
      void recordCall({
        ts: startedAt,
        server: "splunk-mcp",
        tool: name,
        actor: currentActorId(),
        argsHash,
        outcome,
      }).catch((e) => console.error("[audit] record failed", e));
    }
  };
```

`recordCall` は 1〜4 をまとめた関数で、1件につき1回 Lemma への往復が発生します。呼び出し頻度が高いサーバーでは、ローカルのキューに積んでまとめて送る形にしてください。同じキューが再送も兼ねます。落ちた記録の数を数えられない監査証跡は、やはり信用できません。

### 6. 第三者が照合する

`GET /v1/documents/:docHash` は認証不要です。監査する側に API キーを配る必要がありません。

```bash
curl https://workers.lemma.workers.dev/v1/documents/0x071d…
```

```json
{
  "docHash": "0x071d…",
  "schemaId": "passthrough-v1",
  "issuerId": "mcp-gateway",
  "subjectId": "agent:ops-bot",
  "commitmentRoot": "0x1646…",
  "status": "registered",
  "chainId": null,
  "onchainTxHash": null,
  "registeredAt": "2026-08-26T04:12:08Z"
}
```

存在しない `docHash` は 404（`{"error":"Document not found"}`）を返します。登録（`POST /v1/documents`）のほうは API キーが要るので、キーなしで叩くと 401 です。読み取りだけを開くこの非対称が、この設計の要点です。

監査する側は、手元の記録と保管しておいた `randomness` で `commitDeep` を再実行し、得た root を `commitmentRoot` と突き合わせます。

```ts
const recomputed = commitDeep(recordInHand, { randomness: storedRandomness });
const intact = recomputed.root === res.commitmentRoot;
```

一致すれば記録は登録時点から変わっていません。一致しなければ変わっています。判断はここで終わりで、Lemma に問い合わせる必要も、記録の中身を Lemma に見せる必要もありません。

記録が暗号化されている以上、照合できるのは平文を復号できる相手だけです。外部監査人に渡すなら、その相手の公開鍵を `holderKey` に含めるか、記録用と保管用で鍵を分ける設計にしてください。

`status` は3値です。`registered` は Lemma に記録済み（オフチェーン）、`anchored` は確定したオンチェーントランザクションがある状態、`pending` はオンチェーン書き込みを意図したがトランザクションが未確定の状態です。確認できるトランザクションがないものを `anchored` とは呼びません。

## いまできないこと

`@lemmaoracle/mcp` は読み取り専用です。公開している MCP ツールは 5 つで、いずれも参照系です（`lemma_query_verified_attributes` / `lemma_get_schema` / `lemma_get_circuit` / `lemma_get_generator` / `lemma_get_proof_status`）。書き込み側の `lemma_register_document` と `lemma_submit_proof` は README で Phase 2 と明記されていて、リポジトリの `packages/mcp/src/tools/` にもファイルはありますが中身は TODO コメントだけです。この記事のラッパーが `@lemmaoracle/mcp` ではなく `@lemmaoracle/sdk` を直接使っているのはそのためです。AIエージェント側から登録済みの証跡を引く用途では、`@lemmaoracle/mcp` をそのまま使えます。

ZK述語証明はこの記事の範囲外です。「`outcome` が `ok` である」「呼び出し時刻が営業時間内である」といった条件を、中身を出さずに証明するところまでは `prover.prove` と `proofs.submit` で到達できますが、そのためには回路の登録が要ります。今回は登録と照合まででいったん止めています。

選択的開示も別立てです。BBS+ による属性単位の開示は `disclose` 名前空間にありますが、上のフローには組み込んでいません。`commitDeep` がパス単位の葉を作っているので、そこへ進む道は開いています。

コミットメントは記録の正しさを保証しません。証明できるのは「登録時点から変わっていない」ことだけです。呼び出しラッパーが最初から嘘の値を書けば、その嘘がそのまま固定されます。誰が記録したかを担保したいなら `issuerId` に加えて `signature`（発行者署名）を付けてください。`RegisterDocumentRequest` は任意フィールドとして受け取ります。

## 手元で試す

[dashboard.lemma.workers.dev/signin](https://dashboard.lemma.workers.dev/signin) で Continue with GitHub を押すと、スコープと最初の API キーがその場で発行されます。クレジットカードの登録は要りません。キーのシークレットは発行直後に1度だけ表示されるので、その場でコピーしてください。

サインイン後の Reference タブに、`claude_desktop_config.json` へそのまま貼れる `@lemmaoracle/mcp` の設定スニペットがあります。`YOUR_API_KEY` を差し替えれば、お使いの MCP クライアントから登録済みの証跡を引けるようになります。API が受け付けるアルゴリズム文字列とチェーン ID の一覧も同じタブにあります。

ダッシュボードの歩き方は [Lemma Dashboard — 5分クイックスタート](/ja/blog/dashboard-quickstart/) にまとめてあります。関数名とペイロード形状の正典は [`@lemmaoracle/sdk` の README](https://www.npmjs.com/package/@lemmaoracle/sdk) です。

MCPサーバーを1つ選んで、ツールを1つだけ包んでみてください。呼び出しが1件登録され、その root を手元で再計算できたところで、この記事の主張は確かめられます。
