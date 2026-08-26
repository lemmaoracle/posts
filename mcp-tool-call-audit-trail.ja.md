---
slug: "mcp-tool-call-audit-trail"
date: "2026.08.26"
category: "Technical"
audience: technical
section: "Essays"
title: "MCPツール呼び出しの監査証跡を、あとから検証できる形で残す"
abstract: "2026年8月、Splunk MCP Server に CVSS 9.1 の逆シリアル化RCE（CVE-2026-76404）が公表されました。前提は「Splunk admin ロールを持つユーザー」です。つまり認証もロール制御も通ったあとに任意コマンドが走ります。OAuth と RBAC は「呼ばせない」ための仕組みであって、「何が呼ばれたか」の記録が本物であることは保証しません。MCPツール呼び出しを1件ずつコミットメント付きで登録し、第三者が API キーなしで照合できるようにするまでを、実際に動かしたコードで示します。"
tags:
  - mcp
  - audit-trail
  - provenance
  - zk-proof
  - verifiable-ai
relatedLinks:
  - label: "Lemma Dashboard — 5分クイックスタート"
    href: "/ja/blog/dashboard-quickstart/"
  - label: "@lemmaoracle/sdk (npm)"
    href: "https://www.npmjs.com/package/@lemmaoracle/sdk"
  - label: "@lemmaoracle/mcp (npm)"
    href: "https://www.npmjs.com/package/@lemmaoracle/mcp"
  - label: "用語集"
    href: "/ja/glossary/"
---

MCPサーバーを本番で動かしている方に向けて書いています。この記事のゴールは、MCPツール呼び出しを1件ずつコミットメント付きで登録し、第三者があとから API キーなしで照合できる状態を作ることです。載せている出力は実際に手元で実行した結果です（ハンドラに差し込む「5.」だけは、お使いのMCPサーバー実装に合わせて書き換える前提の骨組みです）。

## いま起きていること

まず事実を3つ、出典つきで並べます。

**1. Splunk MCP Server の逆シリアル化RCE。** 2026年8月19日、Splunk が [SVD-2026-0808](https://advisory.splunk.com/advisories/SVD-2026-0808) を公開し、[CVE-2026-76404](https://nvd.nist.gov/vuln/detail/CVE-2026-76404) が採番されました。CVSS 3.1 のベーススコアは 9.1（Critical）、ベクタは `AV:N/AC:L/PR:H/UI:N/S:C/C:H/I:H/A:H`、CWE-502（信頼できないデータの逆シリアル化）です。認証情報を扱うコンポーネントが、保存済みデータを期待する型かどうか確認せずに逆シリアル化していました。影響を受けるのは 1.2.1 未満、修正版は 1.2.1。Splunk 側の緩和策は「Splunk MCP Server app を無効化するか削除する」です。

ここで目を止めたいのはベクタの `PR:H` です。**このRCEには Splunk の admin ロールが要ります**。認証は通っています。ロール制御も通っています。そのうえでOSコマンドが走ります。

**2. 公開MCPサーバーの実態調査。** 2026年7月31日に arXiv に投稿された [Exposed by Design: A Dynamic Security Assessment of Internet-Facing MCP Servers at Scale](https://arxiv.org/abs/2608.00150)（Nicolás Padilla）は、公開インターネット上で 21,000 を超える MCP サーバーインスタンスが観測できると報告しています。うち本番稼働と確認できたのが 640 台、そのうち **414 台**を動的に監査した結果、**91.8% が OAuth 認証を持っていませんでした**。また確認済みサーバー群の中に、アクセス制御なしでシェル実行を提供する**ツールインスタンスが 687 個**ありました。

数字を引くときは母数に注意してください。91.8% は「動的監査した 414 台」に対する割合で、640 台に対する割合ではありません。687 も「サーバー台数」ではなく「ツールインスタンス数」です。

**3. トランスポート設計そのものへの議論。** 2026年8月13〜14日にソウルで [MCP Dev Summit Seoul](https://events.linuxfoundation.org/mcp-dev-summit-seoul/)（Agentic AI Foundation 主催、Linux Foundation のイベント基盤）が開かれ、STDIO トランスポートの設計が主要な論点になりました。開催直前の[解説記事](https://forkast.news/the-model-context-protocol-reaches-a-security-inflection-point/)は、プロトコル自体を作り直すのか、開発者側の回避策で凌ぐのか、という対立軸で状況を整理しています。

## OAuth と RBAC が守っている範囲

3つのシグナルは同じ方向を指しています。ただし、そこから引き出される結論は「認証を強くしよう」だけではありません。

OAuth も RBAC も、**呼び出しが起きる前**に効く仕組みです。誰に呼ばせるか、どのツールを見せるか、どのスコープを許すか。ここを固めるのは当然として、固めきったあとに残る問題があります。

呼び出しが起きたあと、「誰が・いつ・どのツールを・どんな引数で呼んだか」を確かめる手段は何でしょうか。ログです。ではそのログはどこにありますか。多くの場合、MCPサーバーが動いているホスト上のファイルです。

CVE-2026-76404 が示したのは、そのホスト上で任意コマンドが実行されうる、という状態でした。**任意コマンドが走ったホストのログファイルは、そのインシデントの証拠としては使えません**。追記も削除も書き換えも、同じ権限でできてしまうからです。ログ転送を挟んでも、転送前に書き換えられていないことは転送先には分かりません。

つまりアクセス制御と証跡の真正性は、別々に手当てする必要がある別の問題です。前者は「起こさせない」、後者は「起きたことを、あとから誰でも同じ結論に到達できる形で残す」。この記事が扱うのは後者です。

## 設計：ツール呼び出し1件を1ドキュメントにする

やることは単純です。MCPサーバーのツールハンドラを薄いラッパーで包み、呼び出しが1件終わるたびに次の2つに分けます。

- **手元に残すもの** — 呼び出しの中身そのもの（引数、結果、実行者）。暗号化して自分の管理下に置きます。
- **Lemma に登録するもの** — その中身から計算したハッシュとコミットメントだけ。平文は送りません。

登録されるとドキュメントは `docHash` で引けるようになります。この参照は公開エンドポイントなので、監査する側は API キーを持っていなくても、手元の記録から同じ `docHash` が再計算できるかを確かめられます。**一致しなければ、記録は登録後に変わっています。**

ここは正確に書きます。コミットメントは改ざんを**防ぎません**。防ぐのはアクセス制御の仕事です。コミットメントがするのは、改ざんを**検知できるようにする**ことだけです。そしてもうひとつ、登録された記録が事実である保証もしません。証明できるのは「登録した時点の値から変わっていない」という一点です。この線引きを曖昧にした監査証跡は、あとで使いものになりません。

## 実装

`@lemmaoracle/sdk` を使います。関数型のスタイルで、クライアントは各関数の第1引数として渡します。

```bash
npm install @lemmaoracle/sdk @lemmaoracle/spec
```

### 1. クライアントと、記録する1件

```ts
import {
  create,
  encrypt,
  commit,
  derivePublicKey,
  documents,
} from "@lemmaoracle/sdk";

const client = create({
  apiBase: "https://workers.lemma.workers.dev",
  apiKey: process.env.LEMMA_API_KEY,
});

// MCPツール呼び出し1件を、そのまま記録用のオブジェクトにする
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

`encrypt` は ECIES（secp256k1 + HKDF-SHA256 + AES-256-GCM）でペイロードを暗号化し、暗号文・`docHash`・`cid` を返します。**暗号文は戻り値として手元に返るだけで、送信はされません**。保存先はご自身で決めてください。

```ts
// holderKey は復号できる人の secp256k1 圧縮公開鍵
const holderKey = derivePublicKey(process.env.HOLDER_PRIVATE_KEY!);

const enc = await encrypt(client, { payload: call, holderKey });
// enc.docHash, enc.cid, enc.ciphertext, enc.algorithm
```

手元で実行した結果です。

```text
holderKey: 034f355bdcb7cc0af728ef3cceb9615d90684bb5b2ca5f859ab0f0b704075871aa
docHash:   0x72f12dc1029acd9a635f221ba16ead84532e8c397a65059856a2e069e696ba40
cid:       bafkreieyjosbpy4l5tyfox4weg2lxhcpt2t6uspnfhbq6zfxnyopssnx7y
algorithm: aes-256-gcm
```

`docHash` は暗号文の SHA3-256、`cid` は CIDv1-raw です。

### 3. フィールド単位のコミットメントを作る

`commit` はネットワークI/Oのないローカル処理です。オブジェクトをフィールドごとの葉に分解し、Poseidon の Merkle ツリーを組みます。

```ts
const c = await commit(call);
// c.root, c.leaves, c.randomness, c.depth,
// c.inclusionProofs, c.leafPreimages
```

同じく実行結果です。6フィールドなので葉が6枚、深さ3のツリーになります。

```text
root:   0x3f8914c900818eecd291152af8561ee466dab0936d5df8e428264087ded1175
depth:  3
leaves: 6
leafPreimages[0]: {
  "name": "actor",
  "value": "agent:ops-bot",
  "nameHash":  "0x2065763bd2d9baf6b31d533e95776063c79eb49a6bb1062202199cacd225a1b6",
  "valueHash": "0x5cf6e27547601b5bed3960480b060d10d64f2f70cb163fd2bdcf0837f70f171",
  "blindingHash": "0xa99551d706a1bc1ed283adf4d44bd6abffe85e64fe419de5a0f8580705be5929"
}
```

葉が1枚ずつ独立していることには意味があります。あとで「`actor` だけを開示する」「`outcome` が `ok` であることだけを示す」といった部分開示に進めます。ドキュメント全体を1つのハッシュで固めてしまうと、この道が閉じます。

`randomness` はブラインディング係数です。`commit` が呼ばれるたびに新しく生成されるので、**同じ内容の呼び出しを2回記録しても root は一致しません**。呼び出しの内容を root から逆算されない性質はここから来ます。逆に言えば、`randomness` を失うと検証側で root を再計算できなくなるので、暗号文と一緒に保管してください。

### 4. 登録する

```ts
await documents.register(client, {
  schema: "mcp-tool-call-v1",
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
// → { status: "registered", docHash: "0x72f1…" }
```

`commit` の戻り値には `scheme` が入っていないので、`commitments` を組むときに `"poseidon"` を足します。`revocation` は必須フィールドです。ツール呼び出しの記録を失効させる運用がないなら、上のようにゼロ値を入れておきます。

`schema` は文字列で、この API は登録済みかどうかを検査しません。本番では `schemas.register` でスキーマを登録し、その ID を使ってください。

### 5. MCPサーバーに差し込む

あとはツールハンドラを包むだけです。**証跡の記録に失敗しても呼び出し自体は返す**設計にしています。監査のための仕組みが可用性を下げると、真っ先に外されるからです。外れた監査証跡は無いのと同じです。

```ts
const withAuditTrail =
  (name: string, handler: Handler): Handler =>
  async (args) => {
    const startedAt = new Date().toISOString();
    const result = await handler(args);

    void recordCall({
      ts: startedAt,
      server: "splunk-mcp",
      tool: name,
      actor: currentActorId(),
      argsHash: await sha256Hex(JSON.stringify(args)),
      outcome: result.isError ? "error" : "ok",
    }).catch((e) => console.error("[audit] record failed", e));

    return result;
  };
```

`recordCall` は 1〜4 をまとめた関数です。`void` と `.catch` で握っているのは、記録の失敗をツール応答に波及させないためです。ただし握りっぱなしにはせず、失敗はローカルのキューに積んで再送する設計にしてください。落ちた記録の数を数えられない監査証跡は、やはり信用できません。

### 6. 検証する側

`GET /v1/documents/:docHash` は認証不要です。監査する側に API キーを配る必要がありません。

```bash
curl https://workers.lemma.workers.dev/v1/documents/0x72f1…
```

```json
{
  "docHash": "0x72f1…",
  "schemaId": "mcp-tool-call-v1",
  "issuerId": "mcp-gateway",
  "subjectId": "agent:ops-bot",
  "commitmentRoot": "0x3f89…",
  "status": "registered",
  "chainId": null,
  "onchainTxHash": null,
  "registeredAt": "2026-08-26T04:12:08Z"
}
```

存在しない `docHash` は 404（`{"error":"Document not found"}`）を返します。登録（`POST /v1/documents`）のほうは API キーが要るので、キーなしで叩くと 401 です。読み取りだけを開くこの非対称が、この設計の要点です。

`status` は3値です。`registered` は Lemma に記録済み（オフチェーン）、`anchored` は確定したオンチェーントランザクションがある状態、`pending` はオンチェーン書き込みを意図したがトランザクションが未確定の状態です。**確認できるトランザクションがないものを `anchored` とは呼びません。**

照合の手順はこうなります。監査する側は、手元にある呼び出し記録と `randomness` から `commit` を再実行し、得た root が API の `commitmentRoot` と一致するかを見ます。一致すれば、その記録は登録時点から変わっていません。一致しなければ、変わっています。判断はここで終わりで、Lemma に問い合わせる必要も、記録の中身を Lemma に見せる必要もありません。

## いまできないこと

誇張しても仕方がないので、現時点の境界を書いておきます。

**`@lemmaoracle/mcp` は読み取り専用です**。このパッケージが公開している MCP ツールは 5 つで、いずれも参照系です（`lemma_query_verified_attributes` / `lemma_get_schema` / `lemma_get_circuit` / `lemma_get_generator` / `lemma_get_proof_status`）。書き込み側の `lemma_register_document` と `lemma_submit_proof` は README で Phase 2 と明記されていて、リポジトリの `packages/mcp/src/tools/` にもファイルはありますが中身は TODO コメントだけです。したがって、**この記事のラッパーは `@lemmaoracle/mcp` ではなく `@lemmaoracle/sdk` を直接使っています**。AIエージェント側から登録済みの証跡を引く用途では、`@lemmaoracle/mcp` をそのまま使えます。

**ZK述語証明はこの記事の範囲外です。**「`outcome` が `ok` である」「呼び出し時刻が営業時間内である」といった条件を、中身を出さずに証明するところまでは `prover.prove` と `proofs.submit` で到達できますが、そのためには回路の登録が要ります。今回は登録と照合まででいったん止めています。

**選択的開示も別立てです**。BBS+ による属性単位の開示は `disclose` 名前空間にありますが、上のフローには組み込んでいません。`commit` がフィールド単位の葉を作っているので、そこへ進む道は開いています。

**コミットメントは記録の正しさを保証しません**。冒頭に書いたとおり、証明できるのは「登録時点から変わっていない」ことだけです。呼び出しラッパーが最初から嘘の値を書けば、その嘘がそのまま固定されます。誰が記録したかを担保したいなら `issuerId` に加えて `signature`（発行者署名）を付けてください。`RegisterDocumentRequest` は任意フィールドとして受け取ります。

## 試す

[dashboard.lemma.workers.dev/signin](https://dashboard.lemma.workers.dev/signin) で **Continue with GitHub** を押すと、スコープと最初の API キーがその場で発行されます。クレジットカードの登録は要りません。キーのシークレットは発行直後に1度だけ表示されるので、その場でコピーしてください。

サインイン後の **Reference** タブに、`claude_desktop_config.json` へそのまま貼れる `@lemmaoracle/mcp` の設定スニペットがあります。`YOUR_API_KEY` を差し替えれば、お使いの MCP クライアントから登録済みの証跡を引けるようになります。API が受け付けるアルゴリズム文字列とチェーン ID の一覧も同じタブにあります。

ダッシュボードの歩き方は [Lemma Dashboard — 5分クイックスタート](/ja/blog/dashboard-quickstart/) にまとめてあります。関数名とペイロード形状の正典は [`@lemmaoracle/sdk` の README](https://www.npmjs.com/package/@lemmaoracle/sdk) です。

MCPサーバーを1つ選んで、ツールを1つだけ包んでみてください。呼び出しが1件登録され、その `docHash` を API キーなしで引けたところで、この記事の主張は確かめられます。
