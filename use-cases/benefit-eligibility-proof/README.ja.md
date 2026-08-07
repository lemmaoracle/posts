---
title: "受給資格照合 ── 補助金・給付"
abstract: "申請者を、給付の受給要件と照合し、「受給資格あり」という結果だけを受け取ります。所得・世帯などの原本は発行者の管理下に残るので、申請のたびに証明書を集める負担と、窓口での確認待ちを、構造的に取り除けます。"
thesis: "申請者を、受給要件と照合する。結果だけを受け取り、所得・世帯は渡さない。"
pillar: regulatory-attribute-proof
industries:
  - pub
cardSummary: "申請者を受給要件と照合し、結果だけを受け取ります。所得・世帯は渡りません。"
targetVerticals:
  - 自治体・公共
  - 公共サービス事業者
tags:
  - eligibility
  - public-sector
  - selective-disclosure
  - attribute-proof
  - zk-proof
relatedUseCases:
  - public-procurement-attestation
  - credential-presentation
  - age-eligibility-verification
---

# ユースケース: 受給資格照合 ── 補助金・給付

## テーゼ

**申請者を、受給要件と照合する。結果だけを受け取り、所得・世帯は渡さない。**

受給資格の確認は「申請書類を集めれば窓口の待ち時間と保管を抱える、自己申告に頼れば誤支給が起きる」というジレンマを抱えています。Lemma は照合の**結果だけ**を ZK 証明として渡し、所得証明・世帯情報の原本は出しません。受け取った側（窓口・監査）は原本を保管せずに「受給要件を満たすか」を独立に検証でき、担当者が代わっても同じ根拠に照らした判断が残ります。
