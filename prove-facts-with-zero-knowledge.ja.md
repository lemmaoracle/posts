---
slug: "prove-facts-with-zero-knowledge"
date: "2026.02.28"
category: "Guides"
section: "Essays"
title: "ゼロ知識で事実を証明する"
abstract: "「18歳以上」「収益が閾値超」などのビジネスルールを機械検証可能な事実に変換。各証明は回路とジェネレーターとともに永続記録されます。"
---

## ビジネスルールから証明可能な事実へ

「このユーザーは 18 歳以上か？」はビジネスルールです。「コミットされた属性 `age_bucket` が回路 `age-over-18` の下で `adult` に等しく、チェーン 1 上の `attrCommitmentRoot` に対して検証済み」は証明可能な事実です。

Lemma はこのギャップを埋めます。ビジネスロジックを ZK 回路として定義すれば、条件がチェックされるたびに、元データを見ずに誰でも検証できる暗号学的証明が生成されます。

## Lemma における ZK 証明の仕組み

プロセスは3ステップです：

1. スキーマを使って生ドキュメントを型付き属性に**正規化**。
2. 正規化された属性を Pedersen + Merkle で**コミット**し、`attrCommitmentRoot` を生成。
3. ZK 回路を使ってコミットメントに対する述語を**証明**。

```typescript
import { prepare, prover } from "@lemmaoracle/sdk";

const prep = await prepare<UserKycRaw, UserKycNorm>(client, {
  schema: "user-kyc-v1",
  payload: rawDoc,
});

const zkResult = await prover.prove(client, {
  circuitId: "age-over-18",
  witness: {
    age_bucket: prep.normalized.age_bucket,
    randomness: prep.commitments.randomness,
    attr_commitment_root: prep.commitments.attrCommitmentRoot,
  },
});
```

回路 `age-over-18` は `age_bucket` が `adult` に等しいかを検査します。実際の年齢は見ません — コミットされた正規化値とコミットメントを開くランダムネスのみを扱います。

## 独自回路の持ち込み

Lemma は組み込みの述語に限定しません。任意の ZK 回路（Circom、Halo2 等）を、スキーマ、公開入力、検証器コントラクト、プリコンパイル済みアーティファクトに紐づけるメタデータとともに登録できます。

SDK の `prover.prove` は `circuitId` からアーティファクトの場所を解決し、`wasm`/`zkey` をダウンロードし、ローカルで証明を生成します。生データがクライアントの外に出ることはありません。

## 永続的な証明記録

生成された証明は Lemma に送信され、オンチェーンまたはオフチェーンで検証されます。検証結果は完全なコンテキストとともに記録されます：

- 証明が対象とする `docHash`。
- 使用された `circuitId`。
- 検証された `publicInputs`。
- 元ドキュメントを生成した `generatorId`。

これにより、AI は後から「この事実はどう証明されたか？」と尋ね、回路、スキーマ、ジェネレーター、検証方法を含む完全な監査証跡を受け取れます。ブール値だけではありません。

## 信頼境界

Lemma はクライアントの正規化コードやジェネレータースクリプトを信頼しません。ZK 証明、コミットメント、オンチェーン検証器を信頼します。クライアントが不正な正規化を行えば、証明は検証に失敗します。正しさを担保するのはコードではなく数学です。
