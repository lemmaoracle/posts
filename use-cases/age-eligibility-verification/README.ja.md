---
title: "年齢・販売資格の確認"
abstract: "酒類・たばこ・医薬品などの販売で、生年月日や本人確認書類を出さずに「販売できる年齢・資格を満たす」だけを店頭・EC で証明する。"
thesis: "年齢は確かめる。生年月日は渡さない。"
pillar: regulatory-attribute-proof
industries:
  - svc
cardSummary: "生年月日や本人確認書類を出さず、「販売可能な年齢・資格を満たす」だけを店頭・ECで証明できます。"
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

# ユースケース: 年齢・販売資格の確認

## テーゼ

**年齢は確かめる。生年月日は渡さない。**

酒類・たばこ・医薬品など、年齢や資格で販売可否が決まる商品では、年齢確認のたびに生年月日や本人確認書類を受け取れば、その保管自体が漏洩面になります。Lemma は「販売できる年齢・資格を満たす」ことだけを証明として渡し、生年月日・書類は本人と発行体に留まる構造を提供します。
