---
slug: "disclose-only-what-ai-needs"
date: "2026.02.28"
category: "Guides"
section: "Essays"
title: "AI に必要なものだけを開示する"
abstract: "選択的開示により、ホルダーはモデルが必要とする属性だけを公開でき、元の発行者署名との紐づけは維持されます。"
tags:
  - selective-disclosure
  - privacy
  - provenance
  - agent-security
---

## 選択的開示の課題

署名済みドキュメントには多くのフィールドが含まれます。KYC レコードには氏名、生年月日、住所、国籍、ID 番号、確認状態が含まれるかもしれません。しかしレンディングプロトコルが知る必要があるのは「この人物は確認済みで制裁対象外か？」だけであり、自宅住所ではありません。

選択的開示がなければ、すべてを共有するか何も共有しないかの二択を迫られます。Lemma は第三の選択肢を導入します：必要なフィールドだけを、それが元の署名済みドキュメントに含まれることの暗号学的証明とともに共有することです。

## BBS+ 選択的開示の仕組み

Lemma は BBS+ 署名をデフォルトの選択的開示メカニズムとして使用します。SDK の `disclose` モジュールは完全な BBS+ 実装を提供します：

**鍵生成とメッセージ準備：**

```typescript
import { disclose } from "@lemmaoracle/sdk";

// BBS+ 鍵ペアを生成
const kp = await disclose.generateKeyPair();

// ペイロードをソートされた「キー:値」メッセージに変換
const messages = disclose.payloadToMessages({
  age: 25,
  name: "Alice",
  country: "JP",
});
// → ["age:25", "country:JP", "name:Alice"]
```

**発行者がドキュメント全体に署名：**

```typescript
const signed = await disclose.sign(client, {
  messages,
  secretKey: kp.secretKey,
  header: new TextEncoder().encode("my-app-header"),
  issuerId: "issuer-1",
});
// signed.signature → BBS+ 署名
```

**ホルダーが選択した属性のみを公開：**

```typescript
const revealed = await disclose.reveal(client, {
  signature: signed.signature,
  messages,
  publicKey: signed.publicKey,
  indexes: [0], // messages 配列内の "age:25" のインデックス（属性名ではなく）
  header: signed.header,
});
// revealed.attributes → { age: "25" }
// revealed.proof → BBS+ 選択的開示証明

// 仕様準拠のためのラップ
const sd = disclose.toSelectiveDisclosure(revealed);
// sd.format → "bbs+", sd.attributes → { age: "25" }, sd.proof → 16進エンコード
```

この証明は `{ age: 25 }` が発行者の署名したドキュメントの一部であることを数学的に保証し、他のフィールドは一切公開しません。検証者がドキュメント全体を見ることはありません。

## 部分ビューでも来歴は保持される

選択的開示でよくある懸念はコンテキストの喪失です。1つのフィールドしか見えない場合、それが信頼できるとどうわかるのか？

Lemma では、開示された属性は完全な来歴を伴います：

- 元ドキュメントに署名した発行者。
- 属性の型と正規化を定義するスキーマ。
- 開示された値を元の BBS+ 署名に紐づける証明。
- ドキュメントの存在をアンカーするオンチェーンレジストリエントリ。

単一の開示フィールドであっても、暗号学的証拠の切れ目のないチェーンを通じて起源まで遡れます。

## ロールベース・当事者別ビュー

同じドキュメントでも、利用者によって必要なビューが異なります：

- レンディングプロトコルは `{ verified: true, not_sanctioned: true }` を見る。
- 年齢制限サービスは `{ age_bucket: "adult" }` を見る。
- 内部監査人は `{ country: "JP", verification_date: "2026-01-15" }` を見る。

各ビューは同じ署名済みドキュメントから、`disclose.reveal` に異なる `indexes` 配列を渡すことで生成されます。データの別コピーは作成されず、追加の署名手順も不要です。

## 将来を見据えた設計

SDK の `disclose` 名前空間は暗号スキームではなく、実行する機能にちなんで命名されています。初期実装は BBS+ ですが、API は SD-JWT やその他のメカニズムを開発者向けインターフェースの変更なしにサポートできるよう設計されています。`disclose.reveal` を呼ぶとき、あなたが表現しているのは「これらのフィールドだけ見せて」という意図であり、特定のアルゴリズムの選択ではありません。

### 完全な BBS+ API

`disclose` モジュールには必要なすべての BBS+ 操作が含まれます：

- `generateKeyPair()`: BBS+ 鍵ペア作成（32バイト秘密鍵、96バイト公開鍵）
- `payloadToMessages()`: 属性オブジェクトをソートされた「キー:値」配列に変換
- `sign()`: 発行者秘密鍵とヘッダーによる BBS+ 署名
- `verify()`: 発行者公開鍵に対する BBS+ 署名検証
- `reveal()`: 特定のメッセージインデックスに対する選択的開示証明生成
- `verifyProof()`: 選択的開示証明の検証
- `toSelectiveDisclosure()`: `RevealOutput` を仕様準拠の `SelectiveDisclosure` 型にラップ
- `messagesToDisclosedMap()`: 開示された属性マップを再構築するユーティリティ
