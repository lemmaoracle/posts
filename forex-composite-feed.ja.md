---
slug: "forex-composite-feed"
date: "2026.07.24"
category: "Announcements"
section: "Changelog"
title: "証明付きの為替レートフィードを公開しました"
cover: "assets/cover-announcements.png"
abstract: "複数の公開為替APIを突き合わせた合成レートを、取得時点の証明付きで配信します。キー不要で呼び出せ、返ってきたレートは proof の再検証で誰でも確かめられます。何を保証し、何は保証しないか——そしてソースをどこまで明かすかを書きます。"
tags:
  - verifiable-origin
  - provenance
  - zk-proof
relatedLinks:
  - label: "検証センターで内訳を見る"
    href: "https://lemma.frame00.com/ja/verify/"
  - label: "3つの中核技術（Pillars）"
    href: "https://lemma.frame00.com/ja/pillars/"
---

Lemma の証明付きデータAPIの第1弾として、為替レートの合成フィードを公開しました。キーもアカウントも不要で呼び出せます。

```
GET https://workers.lemma.workers.dev/v1/suites/feeds/forex/composite/latest
```

複数の公開為替APIを突き合わせた合成レートを、全ソースを検証したあとにだけ応答します。更新は日次です。

この記事では、このフィードが何を返し、何を保証していて、そしてソースをどこまで明かしているかを書きます。ただ、その前に「証明付き」がここで何を意味するのかを、はっきりさせておきます。

## 「証明付き」とは、ここで何を指すのか

Lemma のフィードで言う「証明付き」は、データが生まれた場所で証明（proof）を発行し、あとから誰でもその proof を再検証できる状態を指します。3つの性質を持ちます。

- **取得時点で proof を発行します。** 為替レートを取得したその時点で、値そのものを預けるのではなく、「この値が、この手順で正しく計算された」という事実を証明する proof を発行します。
- **proof は誰でも再検証できます。** 各 proof は Groth16 で検証でき、その中の公開信号（public signals）に、実際のレート値が含まれています。再検証が通れば、その値が改ざんされていないことを確認できます。
- **検証は無料で、キーもアカウントも要りません。** 世界中のどこからでも、公開情報だけで真正性を確認できます。

つまり「証明付き」は、信頼を得るための装飾ではなく、信頼を前提にせずとも確かめられる仕組みです。この違いが、後述する「ソースを明かさない」判断の根拠になります。

## 何を返すか

レスポンスには、合成後のレートに加えて、その値がどれだけ検証済みかを示す `verification` オブジェクトが入ります。これはそのドキュメントに紐づく proof の再検証結果です。

```json
{
  "feedId": "forex/composite",
  "docHash": "0x68d4bff9…",
  "schema": "canonical-sort-v1",
  "subjectId": "forex/composite-2026-07-23",
  "createdAt": "2026-07-23 17:46:00",
  "attributes": {
    "source": "forex-composite",
    "base": "USD",
    "date": "2026-07-23",
    "rates.JPY": 163.27832,
    "rates.JPY_scaled": 16327832000,
    "rates.EUR": 0.877087
  },
  "verification": {
    "total": 29,
    "verified": 29,
    "failed": 0,
    "verifyIds": ["…"],
    "verifyUrl": "https://workers.lemma.workers.dev/v1/documents/0x68d4bff9…"
  }
}
```

`verification.total` はこのドキュメントに紐づく全 proof 数、`verified` は Groth16 verify が通った数、`failed` は失敗数です。`verifyIds` は各 verify 実行の内容由来レシート（`POST /v1/proofs/verify` が返す ID）で、`verifyUrl` は `GET /v1/documents/{docHash}`（登録記録）への到達先です。`rates.JPY_scaled` は proof の公開入力と同じスケール整数です。上記の `rates` は実 API のスナップショットから抜粋しています。

各 proof には**回路の公開入力（public signals）**が含まれ、その中に実際のレート値が入っています。`forex-average-v1` の公開入力の並びは次のとおりです（`GET /v1/circuits/forex-average-v1` で確認できます）。

```
sourceRootA, sourceRootB, randomnessA, randomnessB, pathHash, averageRate
```

末尾の `averageRate` がレートです。誤差を避けるため ×10⁸ の整数で、たとえば `JPY = 163.27832` は `16327832000` です。

## 検証する — 2段階

このフィードの検証は、目的に応じて2段階あります。

### 基本検証（誰でも、その場で）

フィードの `GET .../latest` は、応答を組み立てるときに各 proof を内部で `POST /v1/proofs/verify` にかけて再検証します。`verification.verified` が `verification.total` と一致していれば、そのドキュメントに紐づく全 proof が Groth16 で検証済みです。

```bash
curl -s https://workers.lemma.workers.dev/v1/suites/feeds/forex/composite/latest \
  | jq '.verification'
# verified === total なら、全 proof 検証済み
```

累計の検証実行数は公開レジストリ（`GET /v1/counters`）でも見られ、その数字自体にも検証センターで出典が付いています。検証コストは ¥0 です。

### 詳細検証（値の束縛と、proof の再検証）

値の一致だけでなく、「その値が公開入力に入り、かつ Groth16 検証が通る」ところまで分けて確かめられます。なお **`GET /v1/proofs/{id}` はありません**。`verifyId` は検証レシートであり、proof 本体を取り出すキーではありません。

#### 1. 公開入力の値を確かめる（キー不要）

フィード応答の `rates.<CCY>_scaled` が、proof の `averageRate` と同じ整数です。

```bash
curl -s https://workers.lemma.workers.dev/v1/suites/feeds/forex/composite/latest \
  | jq '{
      jpy: .attributes["rates.JPY"],
      scaled: .attributes["rates.JPY_scaled"]
    }'
# scaled === 16327832000 なら、公開入力に載る値と一致
```

回路の公開入力名の確認:

```bash
curl -s https://workers.lemma.workers.dev/v1/circuits/forex-average-v1 \
  | jq '.inputs'
# [..., "averageRate"] — 末尾がレート
```

APIキーがある場合は、`docHash` を指定して公開信号配列そのものを取れます（SDK なら `attributes.query`）。

```bash
curl -s -X POST https://workers.lemma.workers.dev/v1/verified-attributes/query \
  -H "Authorization: Bearer $LEMMA_API_KEY" \
  -H "content-type: application/json" \
  -d '{"docHash":"0x68d4bff9…","attributes":[]}' \
  | jq '.results[].proof | {circuitId, averageRate: .inputs[-1]}'
# inputs[-1] === rates.JPY_scaled を確認
```

#### 2. proof を Groth16 で再検証する（キー不要）

手元に `{ circuitId, proof, publicSignals }` があるとき、認証なしで再検証できます。

```bash
curl -s -X POST https://workers.lemma.workers.dev/v1/proofs/verify \
  -H "content-type: application/json" \
  -d '{
    "circuitId": "forex-average-v1",
    "proof": "<proof JSON or base64>",
    "publicSignals": ["<sourceRootA>", "<sourceRootB>", "<randomnessA>", "<randomnessB>", "<pathHash>", "16327832000"]
  }' \
  | jq '{valid, verifyId, circuitId}'
# valid === true なら pairing check 通過。verifyId が検証レシート
```

SDK で手元検証する場合は、`circuits.getById` で vkey を取り、`verifier.verify` に渡します。

```ts
import { create, circuits, verifier } from "@lemmaoracle/sdk";

const client = create({ apiBase: "https://workers.lemma.workers.dev" });
const meta = await circuits.getById(client, "forex-average-v1");
// meta.artifact.location.vkey から vkey JSON を取得したうえで:
const { ok } = await verifier.verify({
  alg: "groth16-bn254-snarkjs",
  inputs: { vkey, proof, publicSignals },
});
```

フィードの `verification` ブロックは、上記と同じ `POST /v1/proofs/verify` をサーバー側で全 proof に対して実行した結果です。値の束縛は `rates.*_scaled`（または `proof.inputs`）で、暗号的な正しさは `valid: true` / `verified === total` で、それぞれ別の軸として確かめます。

補足: `contentHash` を再ハッシュして手元照合する検証方法は、祝日・郵便番号のフィードで使えます（canonical JSON の SHA-256 と照合）。為替は合成値を回路で証明する仕組みのため、`verification` と公開信号で確かめます。フィードの性質に応じて、確かめ方が変わります。

## なぜ「合成」なのか

為替レートは、観測するAPIによって値がわずかに異なります。特定の一社の数字を「正」とするのは、その一社を信用するのと同じです。

そこで複数の公開為替APIから取得し、それらを突き合わせた合成値を計算します。全ソースの取得と検証が完了したスナップショットだけを応答に使います。1本が落ちても残りで継続でき、1本が外れ値を出しても合成の段階で影響を抑えられます。

現状は2ソースですが、ソースは今後増やす前提の設計です。ソースが増えれば増えるほど、特定の1社への依存は薄まり、値の妥当性は高まります。

## ソースをどこまで明かすか

ここが、この記事でいちばん書きたかったところです。結論から言うと、個々の提供元の名前と、取得したレート値は出していません。理由は2つです。

第一に、ライセンス上の制約です。為替APIの利用規約には、取得したレート値をそのまま再配布することを禁じているものがあります。合成値の配信はこの制約に抵触しませんが、元のレート値をAPIレスポンスに含めることはできません。

第二に、設計上の理由です。元のレート値を明かさなくても、合成値が正しいことは proof の検証で確かめられます。`forex-average-v1` 回路は「2つの入力から正しく平均が計算されたこと」を証明します。proof の再検証が通れば、合成計算の正しさは確認済みです。APIレスポンスに含める必要がなかった——証明がその役割をすでに果たしているからです。

また、ソース自体はいずれも公開APIです。誰でも直接そのAPIを呼び出せば元のレート値を取得できます。その値を使って proof を手元で再検証すれば、「この合成値は、たしかにこのソースから正しく計算されている」ことを、第三者が独立に確かめられます。「隠している」のではなく、再現可能なのです。

なお、郵便番号や祝日のように出所が価値のフィードでは逆に、出典（日本郵便・内閣府）を表面に明記します。これは開示戦略ではなく、利用規約上のコンプライアンスであり、かつ「そのデータであること」自体が価値だからです。同じ基盤でも、合成フィードと公式単一ソースでは、出す情報の向きが逆になります。

## 何を保証し、何を保証しないか

証明を名乗る以上、境界をはっきりさせます。このフィードの proof が言えるのは、次までです。

- この合成レートは、この合成手順で正しく計算され、その値が検証済みの proof に含まれている。

言えないことも、はっきりさせます。

- 個々のソースが出した値が、市場の「正しい」値である保証はしません。proof が対象にしているのは合成の計算と値の完全性であって、一次情報の真偽ではありません。

合成が担っているのは、まさにこの「一次情報の真偽は保証できない」という穴を、複数ソースの突き合わせで実務的に狭めることです。proof は「値が正しく計算され改ざんされていないこと」を、合成は「値の妥当性」を、それぞれ別の手段で受け持っています。どちらか一方に両方を語らせないことが、設計上の芯です。

## 使いどころ

たとえば、海外売上（USD 建て）を円に換算して計上するとき、その換算レートに「時点・出典」の証明が付きます。経費側でも同じで、USD 建て請求の円換算に出典が付きます。自己申告のスクリーンショットと違い、換算レートまで第三者が検証できる——これが証明付きフィードの実用的な差です。

## 次回

次回以降は、残り2本を1本ずつ書きます。

- **郵便番号:** 全国12万件超を、単件取得もできる形で。4/4 の data-commitment が意味すること（こちらは `contentHash` の手元照合が使えます）。
- **祝日:** 年に一度しか変わらないデータに、なぜ証明を付けるのか。

このフィードは Lemma の証明付き公共データAPIの一部です。
