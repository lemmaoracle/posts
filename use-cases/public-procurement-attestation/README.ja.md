---
title: "入札資格照合 ── 公共調達"
abstract: "入札者を、調達要件（資格等級・許認可・納税・排除条件）と照合し、「入札資格あり」という結果だけを受け取ります。証書・納税情報の原本は発行者の管理下に残るので、入札のたびに書類一式を集める負担と、確認の往復を、構造的に取り除けます。"
thesis: "入札者を、調達要件と照合する。結果だけを受け取り、原本書類は渡さない。"
pillar: regulatory-attribute-proof
industries:
  - pub
  - sc
cardSummary: "入札者を調達要件と照合し、結果だけを受け取ります。資格証・納税証明は渡りません。"
targetVerticals:
  - 公共調達機関
  - 自治体・中央政府の調達担当
  - 防衛・基幹インフラ調達のサプライヤ
  - 入札コンソーシアム・元請事業者
tags:
  - public-procurement
  - civic
  - selective-disclosure
  - attestation
  - regulatory-attribute
relatedUseCases:
  - benefit-eligibility-proof
  - supplier-credential-verification
  - counterparty-screening
---

# ユースケース: 入札資格照合 ── 公共調達

## テーゼ

**入札者を、調達要件と照合する。結果だけを受け取り、原本書類は渡さない。**

入札資格の確認は「書類一式を集めれば確認の往復と保管を抱える、事業者任せにすれば要件漏れが残る」というジレンマを抱えています。公共調達の評価者の仕事は、検証することであって書類を読むことではありません。Lemma は照合の**結果だけ**を ZK 証明として渡し、認証書・財務諸表・実績証明・納税証明の原本は入札者の手元に留めます。受け取った側（発注機関・監査）は原本を保持せずに「入札資格を満たすか」を独立に検証できます。
