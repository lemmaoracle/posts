---
title: "本人属性照合 ── KYC/AML"
abstract: "本人を、発行者が持つ KYC/AML 要件と照合し、「要件を満たす」という結果だけを ZK 証明として受け取ります。原本（氏名・住所・生年月日・取引履歴）は発行者の管理下に残るので、本人情報を渡すことに伴う漏洩・拡散リスクと、各社・各委託先が同じ本人を重複して確認する無駄を、構造的に取り除けます。"
thesis: "本人を、KYC/AML の要件と照合する。結果だけを受け取り、原本は渡さない。"
pillar: regulatory-attribute-proof
industries:
  - fin
cardSummary: "本人を KYC/AML 要件と照合し、結果だけを受け取ります。原本は渡りません。"
targetVerticals:
  - 銀行
  - フィンテック
  - クロスボーダー決済
tags:
  - kyc
  - aml
  - selective-disclosure
  - privacy
relatedUseCases:
  - financial-data-exfiltration
  - supply-chain-esg
---

# ユースケース: 本人属性照合 ── KYC/AML

## テーゼ

**本人を、KYC/AML の要件と照合する。結果だけを受け取り、原本は渡さない。**

本人確認の突合は「本人属性ごと共有すると漏洩・拡散リスク、共有しないと各社・各委託先が同じ本人を重複して確認」というジレンマを抱えています。Lemma は照合の**結果だけ**を ZK 証明として渡し、氏名・住所・生年月日・取引履歴は出しません。受け取った側（委託先・提携先・監査人・当局）は原本を見ずに「要件を満たすか」を独立に検証でき、しかも後から改ざんできません。
