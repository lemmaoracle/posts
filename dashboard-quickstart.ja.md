---
slug: dashboard-quickstart
title: "Lemma Dashboard — 5分クイックスタート"
date: "2026.05.21"
category: ガイド
section: Essays
abstract: "dashboard.lemma.workers.dev の Dashboard をひと通り触ります。サインイン・APIキー発行・各タブの役割確認・AIエージェント接続まで。x402とゼロ知識証明をすでに知っている開発者向けです。"
tags:
  - zk-proof
  - verifiable-ai
  - x402
  - provenance
relatedLinks:
  - label: "Dashboard"
    href: "https://dashboard.lemma.workers.dev/dashboard"
  - label: "@lemmaoracle/sdk (npm)"
    href: "https://www.npmjs.com/package/@lemmaoracle/sdk"
  - label: "用語集"
    href: "/ja/glossary/"
---

Lemma Dashboard（[dashboard.lemma.workers.dev](https://dashboard.lemma.workers.dev/dashboard)）は、ご自身のスコープ配下にあるAPIキー・公開リスティング・登録済みリソースを操作する場所です。このガイドでは各タブが何をするかをひと通り押さえ、5分で全体像をつかむことを目的としています。ZK証明・HTTP 402決済フロー・BBS+選択的開示の概念は、すでにご存知である前提で書いています。初めて触れる用語があれば、先に[用語集](/ja/glossary/)に目を通してください。

## 5分UIツアー

### 1. サインイン（初回はGitHub OAuth）

[dashboard.lemma.workers.dev](https://dashboard.lemma.workers.dev/signin) を開き、**Continue with GitHub** をクリックします。OAuthフローが新しい **scope**（テナント境界）と、それに紐づく最初のAPIキーを発行します。キーのシークレットは次画面で1度だけ表示されます。コピーし忘れた場合は、revokeして作り直すしかありません。

2回目以降は **Seal Proof** が使えます。これはAPIキーを所有していることをGroth16 ZK証明でクライアント側だけで示す方式で、シークレットそのものはサーバに送りません。Sealはキーが既に存在している必要があるため、新規アカウントを作る経路としては使えません。新規はGitHub経由で行ってください。Sealが `503` を返す場合はサーバ側のverification keyがまだ登録されていない状態なので、開発中はGitHub OAuthをフォールバックとして使ってください。

### 2. API keys — 認証情報

**API keys** タブを開きます。サインイン時に発行されたキーがすでに並んでいます。把握すべきは2点です。

- **シークレットは1度しか表示されません。** *Create new key* を押してシークレットをコピーし、ページを離れてください。以降の一覧画面では、キー名と作成日時しか表示されません。シークレットの再表示はありません。
- **Revokeは即時破壊的です。** revokeした瞬間に、そのキーは全環境で無効になります。ローテーションは「新キー作成 → デプロイ → 旧キーrevoke」の順で計画してください。

キーはSDKや任意のHTTPクライアントからLemma APIを叩く際のbearer tokenとして使います。Gitに入れないようにしてください。

### 3. Reference — APIが受け付ける形式

**Reference** タブを開きます。統合作業中はずっと開いておくページです。すべて読み取り専用で、以下が列挙されています。

- **Supported chains** — オンチェーン検証が可能なネットワークごとのchain IDとexplorer URL。
- **Cryptographic algorithms** — APIが受け付ける厳密な文字列です。証明は `groth16-bn254-snarkjs`、選択的開示は BBS+ on BLS12-381、発行者署名は ECDSA on secp256k1、ペイロード暗号化は ECIES（secp256k1 + HKDF-SHA256 + AES-256-GCM）、ハッシュは SHA-256 です。
- **Official libraries** — [`@lemmaoracle/sdk`](https://www.npmjs.com/package/@lemmaoracle/sdk)・[`@lemmaoracle/mcp`](https://www.npmjs.com/package/@lemmaoracle/mcp)・[`@lemmaoracle/parser`](https://www.npmjs.com/package/@lemmaoracle/parser) へのリンク。
- **MCP config snippet** — そのまま貼れる `claude_desktop_config.json` ブロック。`YOUR_API_KEY` をstep 2で控えたキーに置き換えれば、MCPに対応するAIクライアントから検証済み属性が見えるようになります。

### 4. 最初のリソースを登録する

スキーマ・回路・ジェネレータ・ドキュメント・証明は Dashboard 上では作成できません。Dashboardはあくまで読み取り表示です。登録は workers API（`https://workers.lemma.workers.dev`、SDKのデフォルトbase URL）に対してプログラマティックに行います。経路は [SDK](https://www.npmjs.com/package/@lemmaoracle/sdk) 経由か、HTTPエンドポイントを直接叩くかの2通りです。最初に読むべきはSDKのREADMEで、関数名とペイロード形状のsource of truthはそこにあります。

### 5. Overview — スコープの登録状況を見る

何かを登録したら、**Overview** タブで反映を確認します。ページは `/api/resources` を10秒間隔でポーリングしており、スコープ配下を Schemas / Circuits / Generators / Documents / Proofs の5セクションに分けて表示します。各行をクリックすると、フルレコードが右ドロワーで開き、コピー用ヘルパーが付きます。

### 6. （任意）Marketplace — リソースを公開する

所有しているスキーマ・回路・ジェネレータが再利用可能なものであれば、**Marketplace** タブで公開できます。Withdrawはワンクリックで、リスティングはinactiveに切り替わり公開検索から外れます。掲載できるのは自分のスコープに登録されたリソースだけで、他人の作ったものを勝手に並べることはできません。

ここでいう Marketplace は、Dashboard が独自に持つリスティングUI（ご自身のスコープに登録されたリソースを公開するための機能）です。x402 のルートディスカバリー（x402 facilitator が広告するルートごとのメタデータ）や、ロードマップにある将来の「Bazaar」系インデックスとは別概念・別サーフェスですのでご注意ください。

### 7. Usage — リクエスト量を見る

**Usage** タブは、スコープのAPIリクエスト量グラフです。読み取り専用で、リクエストが溜まるにつれて更新されます。課金や rate limit を気にしはじめる前に状態を確認する用途で使ってください。

これでUIは全部です。ここに無い操作はSDKまたは直接APIコールから行います。

## Concepts — Dashboardが使う6つの名詞

すべてLemma独自の言葉ですが、既知の暗号要素にきれいに対応します。

### Scope（スコープ）

あなたのテナント境界です。登録物（キー・スキーマ・回路・ドキュメント・証明）はすべて1つのscope IDに紐づきます。GitHubで新規サインインすると新しいscopeが作られます。発行者DIDが同じでも、scopeをまたいでデータが見えることはありません。

scopeは明示的に登録するものではなく、DashboardでGitHub OAuthサインインした時点で自動的に作成されます。サインイン後の各ページのフッターにscope IDが表示されます。

### Schema（スキーマ）

ドキュメントの属性形状を固定する型宣言で、normalize artifact（同じ回路に解決されるWASMモジュール）にひも付きます。登録後はimmutableなので、変更したい場合は `age-over-eighteen.v2` のように `id` でバージョンを切ってください。

**登録方法。** SDKの `schemas.register` を呼び出します。登録後は **Overview → Schemas** に表示されます。

```ts
import { schemas } from "@lemmaoracle/sdk";

await schemas.register(client, {
  id: "weather.v1",
  normalize: { type: "ipfs", wasm: "Qm…" },
});
```

### Circuit（回路）

スキーマに紐づくZK回路で、circuit IDで参照します。現状ほぼすべてが `snarkjs` でコンパイルしたBN254上のGroth16です。APIが受け付けるアルゴリズム文字列はReferenceタブに列挙されています。関連：[ゼロ知識証明](/ja/glossary/zk-proof/)。

**登録方法。** SDKの `circuits.register` を呼び出します。登録後は **Overview → Circuits** に表示され、以降の証明送信で `circuitId` 指定して利用できます。

```ts
import { circuits } from "@lemmaoracle/sdk";

await circuits.register(client, {
  circuitId: "weather-threshold.v1",
  artifact: {
    type: "ipfs",
    wasm: "Qm…",
    provingKey: "Qm…",
    verificationKey: "Qm…",
  },
});
```

### Generator（ジェネレータ）

回路に対して証明を生成するクライアント側のアーティファクトで、通常はwitness builderとproving keyの所在を指します。外部の主体が回路ロジックを再実装せずに証明を作れるようにするための仕組みです。

**登録方法。** 対象の回路を登録してから `generators.register` を呼び出します。登録後は **Overview → Generators** に表示されます。

```ts
import { generators } from "@lemmaoracle/sdk";

await generators.register(client, {
  generatorId: "weather-threshold-gen.v1",
  circuitId: "weather-threshold.v1",
  artifact: { type: "ipfs", wasm: "Qm…" },
});
```

### Document（ドキュメント）

発行者署名付きの主張です。Dashboardは `docHash`・`cid`・`issuerId`・`subjectId`・document commitments・revocation状態だけを保存し、平文ペイロードは持ちません。関連：[docHash](/ja/glossary/doc-hash/)・[CID](/ja/glossary/cid/)・[プロヴナンス](/ja/glossary/provenance/)。

**登録方法。** まず `prepare()` で原ドキュメントの正規化とcommitments計算を行い、その結果を `documents.register` に渡します。登録後は **Overview → Documents** に表示されます。

```ts
import { documents } from "@lemmaoracle/sdk";

// commitments は prepare() の結果
await documents.register(client, {
  schema: "weather.v1",
  docHash: "0x…",
  cid: "Qm…",
  issuerId: "weather-issuer",
  subjectId: "tokyo-1",
  commitments,
});
```

### Proof（証明）

登録された回路に対して提出されたGroth16のinstanceで、ドキュメントに紐付けることもできます。Overviewには、scopeが生成したすべての証明がverification状態とともに並びます。関連：[選択的開示](/ja/glossary/selective-disclosure/)。

**提出方法。** `prover.prove` でブラウザ内で証明を生成し、`proofs.submit` でAPIに送信します。**Overview → Proofs** にverification状態とともに表示されます。

```ts
import { prover, proofs } from "@lemmaoracle/sdk";

// witness の形は回路に依存
const { proof, inputs } = await prover.prove(client, {
  circuitId: "weather-threshold.v1",
  witness,
});

await proofs.submit(client, {
  docHash: "0x…",
  circuitId: "weather-threshold.v1",
  proof,
  inputs,
});
```

## 次に読むもの

- **概念の深さ** — [用語集](/ja/glossary/)。ZK・プロヴナンス・エージェント決済・規制レイヤーを横断する27項目を紹介しています。
- **API仕様** — [`@lemmaoracle/sdk`](https://www.npmjs.com/package/@lemmaoracle/sdk) のREADME。関数名・fetchヘルパー・正確なペイロード形状の出典です。
- **AIエージェント向けMCP** — Referenceタブに [`@lemmaoracle/mcp`](https://www.npmjs.com/package/@lemmaoracle/mcp) の設定スニペットがそのまま貼ってあります。Claude Desktopに入れれば、エージェントから検証済み属性が見えます。
- **サインインで詰まったとき** — Seal Proofはサーバ側のverification keyが未登録だと `503` を返します。ローカル開発中はGitHub OAuthをフォールバックとして使ってください。

足りない情報があれば、[docsリポジトリ](https://github.com/lemmaoracle/posts/issues) に issue を立ててください。
