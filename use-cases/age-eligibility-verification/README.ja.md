---
title: "年齢照合 ── 年齢・販売資格"
abstract: "購入者を、発行体が持つ年齢・販売資格の条件と照合し、「販売できる」という結果だけを ZK 証明として受け取ります。生年月日・本人確認書類は発行体の管理下に残るので、身分証を保存・目視することに伴う漏洩リスクと、対面確認の手間を、構造的に取り除けます。"
thesis: "購入者を、販売資格の条件と照合する。結果だけを受け取り、身分証は渡さない。"
pillar: regulatory-attribute-proof
industries:
  - svc
cardSummary: "購入者を販売資格の条件と照合し、結果だけを受け取ります。身分証は渡りません。"
targetVerticals:
  - 小売・コンビニ
  - EC
  - 酒類・たばこ・医薬品
  - 無人店舗・自販機
tags:
  - age-verification
  - selective-disclosure
  - range-proof
  - attribute-proof
  - zk-proof
relatedUseCases:
  - kyc-aml-selective-disclosure
  - customer-flag-need-to-know
  - credential-presentation
---

# ユースケース: 年齢照合 ── 年齢・販売資格

## テーゼ

**購入者を、販売資格の条件と照合する。結果だけを受け取り、身分証は渡さない。**

年齢確認は「身分証を提示・保存して確かめると流出の対象が増える、確かめなければ販売資格が成立しない」というジレンマを抱えています。Lemma は照合の**結果だけ**を ZK 証明として渡し、生年月日・本人確認書類は出しません。受け取った側（レジ・EC・監査）は券面を見ずに「販売できる年齢・資格を満たすか」を独立に検証でき、しかも後から改ざんできません。
