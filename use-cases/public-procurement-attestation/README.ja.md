---
title: "公共調達属性証明"
abstract: "公共調達における供給元・実績・適格性の属性を、原本提出なしに ZK 証明として確認できます。発注側は要件の充足を検証でき、受注側は原本書類を手元に留めて機密情報を保護できます。"
thesis: "提出される ≠ 適格性を証明できる"
pillar: regulatory-attribute-proof
industries:
  - pub
  - sc
cardSummary: "入札者の適格性を、原本書類を渡さずに属性単位で証明し、発注側の要件遵守と受注側の機密保護を両立する。"
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
  - kyc-aml-selective-disclosure
  - supply-chain-esg
  - supplier-credential-verification
---

# ユースケース: 公共調達属性証明

## テーゼ

**提出される ≠ 適格性を証明できる**

公共調達の評価者の仕事は、検証することであって、書類を読むことではありません。一方で、現場の文書提出はこの 2 つを混同しがちです。認証書・財務諸表・実績証明が原本のまま評価部門を通り、入札者と発注者の双方で開示面が広がります。Lemma は属性単位の ZK アテステーションを発行し、評価者は「入札者が適格性述語を満たしている」ことを暗号証明に対して検証します。原本書類は入札者の手元に留まります。
