---
slug: "lemma-oracle-specs"
date: "2026.02.24"
category: "Foundations"
title: "Lemma Oracle 仕様"
abstract: "暗号学的に検証された真実レイヤー。ゼロ知識証明・選択的開示・オンチェーン出所により、AI が機密データ上で推論しながら、生コンテンツは暗号化されたまま保持されます。"
---

## Lemma 概要

Lemma は、機密コンテンツを露出させずに検証済みデータ上で AI が推論できる、数学的に保証された真実レイヤーです。あらゆる AI インタラクションを包む汎用ラッパーではなく、ミッションクリティカルなデータの高信頼ストレージおよび参照レイヤーとして機能します。

AI エージェントは MCP 経由で Lemma にアクセスし、安全クリティカルなワークフロー、公正な権利管理、暗号学的に検証された事実に基づく正確な応答といったユースケースで信頼できるデータソースとして利用します。

### 主な機能とメリット

- データライフサイクル全体での完全な透明性により、監査可能性と信頼を確保。
- 改ざん耐性により、AI システムは常に完全性が保護されたデータを参照。
- 機密情報を暗号化したまま保持し、セキュリティと正確性を維持する強力なプライバシー保証。

### 技術的基盤

- **ゼロ知識証明**: 任意の JSON ドキュメント（契約、ログ、申請など）は暗号化されたまま、Lemma は特定の条件を満たすかどうかのみを証明します。ブロックチェーンと組み合わせることで、機密性、条件充足の主張、公開かつ改ざん検知可能な出所を提供します。
- **選択的開示（BBS+）**: ドキュメントに署名し、ホルダーは文書全体が真正であることを証明しつつ、選択したフィールドのみを開示できます。これにより、ロールベースおよび当事者別の部分開示が、無関係なデータを漏らさず実現します。
- **コミットメント**: 各属性は暗号学的コミットメントとして保存され、復号なしにカテゴリや範囲への所属といったゼロ知識クエリを可能にします。選択的開示と組み合わせることで、適切な合意が整った場合にのみコンテンツを開示し、学習や推論に利用できます。

## 仕様サマリー

- **ドキュメントモデル**
  - W3C VC に限らず、任意の JSON ドキュメント（気象データ、KYC 結果、医療メトリクスなど）を扱う。
  - ドキュメントは暗号化（ハイブリッド ECDH+HKDF 鍵による AES-GCM）され、オフチェーンで IPFS/Ceramic に保存。Lemma が参照するのは `docHash` と `cid` のみ。
- **スキーマと正規化**
  - 各 `schemaId` ごとに `Raw`、`Norm`、および `normalize: Raw -> Norm` を定義し、比較と証明が正規化された属性空間（年齢や温度のバケット化など）で行われるようにする。
  - 正規化はクライアント側で実行。Lemma が信頼するのはコミットメント、ZK 証明、レジストリメタデータであり、正規化コードそのものではない。
- **コミットメントと ZK**
  - 正規化された属性は Pedersen + Merkle でコミットされ、`attrCommitmentRoot` および ZK のウィットネス/公開入力として使う属性ごとのコミットメントを生成する。
  - Circom、Halo2 などの任意の ZK 回路を、`circuitId` をスキーマ・公開入力・検証器（オン/オフチェーン）・成果物（IPFS/HTTPS 上の `wasm`、`zkey`）に紐づけるメタデータでサポート。
- **選択的開示（BBS+）**
  - 発行者が BBS+ で生ドキュメントに署名。ホルダーは選択した属性のみを開示する証明を作成。API で抽象化されているため、のちに SD-JWT などにも対応可能。
  - Lemma は、保存された署名メタデータと選択的開示証明を組み合わせて、開示された属性が署名済みドキュメントに含まれることを検証する。
- **オンチェーン出所とフック**
  - `LemmaRegistry` コントラクトは `docHash`、`attrCommitmentRoot`、スキーマ、発行者/主体 ID、失効データ、および検証器コントラクトへのリンクを保存する。
  - ドキュメント登録時に、レジストリ呼び出しと同じ出所（公開入力）を受け取るオプションのスマートコントラクトフックを実行可能。
- **アーキテクチャと信頼**
  - 5 層: オンチェーン出所、オフチェーン暗号化ストレージ、暗号化インデックス/ZK、オラクルコア/レジストリ/クエリゲートウェイ、クライアント/AI/dApps。
  - Lemma の信頼境界はクライアントコードを除外。ZK 証明、コミットメント、発行者署名、オンチェーンレジストリ/検証器の正当性に依存する。

---

## ユースケース

### DeFi プライベートレンディングと低担保ローン

- **課題:** DeFi は過担保に縛られ、オフチェーンの信用をオンチェーンで評価する手段がない。
- **解決策:** 借り手は Khaos に暗号化した収入/信用データを保存。プロトコルは ZK で「スコア ≥ 700」を検証し、生の財務情報は一切露出しない。
- **効果:** 低担保の DeFi ローンが現実のものとなり、TradFi の信用をオンチェーン市場に橋渡しする。

### DeFi コンプライアンス — プライバシーを守る KYC/AML

- **課題:** 規制当局は KYC/AML を要求するが、ユーザーの PII をオンチェーンに保存するとプライバシーが破壊される。
- **解決策:** 一度だけオフチェーンで KYC を実施し、Khaos が ZK クレデンシャル「本人確認済み・制裁対象外」を発行。氏名やパスポートは開示しない。
- **効果:** AML/CFT と GDPR を同時に満たしつつ、パーミッションレスな DeFi アクセスを維持。

### RWA — 実物資産のトークン化

- **課題:** 不動産・証券・コモディティをオンチェーン化するには厳格な KYC と所有権証明が必要だが、公開台帳への完全開示は現実的でない。
- **解決策:** 発行者とカストディアンは Khaos に暗号化した所有権/コンプライアンスデータを保存。RWA プロトコルは ZK で「ウォレットは KYC 済みかつ資産 X を保有」を検証し、本人情報や貸借対照表は露出しない。
- **効果:** コンプライアンスを満たし、プライバシーを守る RWA の発行・移転・担保付きレンディング。データリスクなしの機関向けグレード。

### 自律 AI エージェント決済と個人データ

- **課題:** ユーザーに代わって取引する AI エージェントは、決済を実行し、リスク限度・予算・KYC などの個人制約を守る必要があるが、取引先に個人データを漏らしてはならない。
- **解決策:** ユーザー制約と KYC クレデンシャルは Khaos に格納。エージェントは取引や決済指示ごとに ZK 証明（「リスク限度内」「取引先はコンプライアント」）を付与する。
- **効果:** 完全自律かつポリシー準拠のエージェント間取引。すべての判断が検証可能で、すべてのアイデンティティが保護される。

### KYC、アイデンティティ、クレデンシャル証明

- **課題:** ユーザーは同じ PII をサービスごとに再提出し、漏洩リスクが増幅する。
- **解決策:** Khaos に一度だけ登録し、ZK で「年齢 ≥ 20」や「免許 X を保有」を証明。生データは共有しない。
- **効果:** 1 回の登録で、全サービスにわたる無制限のプライバシー保護検証が可能。

### 機密データスクリーニング — 給与・健康・ローン

- **課題:** ローンや保険の審査では正確な給与や診断の開示を強いられる。
- **解決策:** 署名付きレコードを Khaos に保存。審査側は ZK で「年収 ≥ 500 万円」や「再検査不要」のみを確認する。
- **効果:** 資格は証明されつつ、実際の数値は金庫の外に出ない。

### オリジナリティの保護とデータクリエイターへの報酬

- **課題:** AI 学習には検証可能な出所と公正なクリエイター報酬が欠けている。
- **解決策:** クリエイターは Khaos に署名付きメタデータを登録。AI オペレーターはアクセス前に ZK で権利を検証する。
- **効果:** オリジナリティ証明 + 利用監査証跡 + 支払い配分。1 プラットフォームでクリーンデータマーケットプレイスに対応。

### AI / RAG 向けの検証済み情報

- **課題:** AI チャットボットは古い・未レビュー・制限付きのドキュメントを引用する。
- **解決策:** ドキュメントごとのメタデータ（バージョン、レビュー状態、分類）を ZK 証明付き属性として提供する。
- **効果:** RAG は検証済みソースのみを使用。制限コンテンツは未承認クエリには見えない。

### 属性ベースアクセス制御

- **課題:** ユーザー認可のためだけに、各アプリが HR/組織データのコピーを保有している。
- **解決策:** 属性（ロール、部門、クリアランス）は Khaos に格納。アクセス権は ZK で検証し、PII はアプリに渡らない。
- **効果:** 真のゼロトラスト。「サービス A は部門のみ、サービス B はロールのみを見る」。

---

## 最小限の開発者ワークフロー（簡略版）

### 1. インストールと初期化

```shellscript
npm install @lemmaoracle/sdk
```

```typescript
import { create } from "@lemmaoracle/sdk";

const client = create({
  apiBase: "https://api.lemma.example.com",
  apiKey: process.env.LEMMA_API_KEY!,
});
```

### 2. スキーマと正規化の定義

```typescript
import { define } from "@lemmaoracle/sdk";

type UserKycRaw = { age: number; country: string };
type UserKycNorm = { age_bucket: "adult" | "minor"; country: string };

const userKycSchema = define<UserKycRaw, UserKycNorm>({
  id: "user-kyc-v1",
  normalize: (raw) => ({
    age_bucket: raw.age >= 18 ? "adult" : "minor",
    country: raw.country,
  }),
});
```

### 3. 暗号化と準備

```typescript
import { encrypt, prepare } from "@lemmaoracle/sdk";

const rawDoc: UserKycRaw = { age: 25, country: "JP" };
const holderPubKey = "...";

const enc = await encrypt(client, {
  payload: rawDoc,
  holderKey: holderPubKey,
});
// enc.docHash, enc.cid

const prep = await prepare<UserKycRaw, UserKycNorm>(client, {
  schema: userKycSchema.id,
  payload: rawDoc,
});
// prep.normalized, prep.commitments
```

一行説明: `encrypt` は `docHash`/`cid` を、`prepare` は正規化された属性とコミットメントを返します。

### 4. 署名と選択的開示の作成

```typescript
import { disclose } from "@lemmaoracle/sdk";

const issuerPrivateKey = "...";

const signed = await disclose.sign(client, {
  payload: rawDoc,
  issuerKey: issuerPrivateKey,
});
// signed.signature

const sd = await disclose.reveal(client, {
  signedPayload: signed.signature,
  attributes: ["age"],
});
// sd.disclosed, sd.proof
```

### 5. ドキュメントの登録（オプションは省略）

```typescript
import { documents } from "@lemmaoracle/sdk";

await documents.register(client, {
  schema: userKycSchema.id,
  docHash: enc.docHash,
  cid: enc.cid,
  commitments: {
    attrCommitmentRoot: prep.commitments.attrCommitmentRoot,
  },
  signature: {
    format: "bbs+",
    payload: signed.signature,
    issuerId: "issuer-1",
  },
});
```

失効の詳細設定や onchainHooks などの本質的でないオプションはここでは省略しています。

### 6. 証明の生成と送信（オプションは省略）

```typescript
import { prover, proofs } from "@lemmaoracle/sdk";

const zkResult = await prover.prove(client, {
  circuitId: "age-over-18",
  witness: {
    age_bucket: prep.normalized.age_bucket,
    randomness: prep.commitments.randomness,
    attr_commitment_root: prep.commitments.attrCommitmentRoot,
  },
});
// zkResult.proofBytes, zkResult.publicInputs
```

```typescript
await proofs.submit(client, {
  docHash: enc.docHash,
  circuitId: "age-over-18",
  proofBytes: zkResult.proofBytes,
  publicInputs: zkResult.publicInputs,
  selectiveDisclosure: {
    format: "bbs+",
    disclosedAttributes: sd.disclosed,
    proof: sd.proof,
  },
});
```

ここでは証明を紐づけドキュメントにリンクするために必要なコアフィールドのみを扱います。

### 7. 検証済み属性のクエリ

```typescript
import { attributes } from "@lemmaoracle/sdk";

const results = await attributes.query(client, {
  query: "users over 18 in Japan",
  mode: "natural",
  proof: { required: true, type: "zk-snark" },
  targets: { schemas: ["user-kyc-v1"] },
});
```

検証済み属性と証明状態が返され、アプリや AI エージェントが利用できます。
