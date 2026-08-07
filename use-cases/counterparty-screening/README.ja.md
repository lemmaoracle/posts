---
title: "取引先スクリーニング ── 与信・反社"
abstract: "取引先を、発行者が持つ与信・反社リストと照合し、「基準を満たす」という結果だけを ZK 証明として受け取ります。原本（取引履歴・スコア・照会履歴）は発行者の管理下に残るので、根拠を渡すことに伴う漏洩・名誉毀損リスクと、各社が同じ相手を重複して照合する無駄を、構造的に取り除けます。"
thesis: "取引先を、与信・反社リストと照合する。結果だけを受け取り、原本は渡さない。"
pillar: regulatory-attribute-proof
industries:
  - fin
  - mfg
  - sc
cardSummary: "取引先を与信・反社リストと照合し、結果だけを受け取ります。原本は渡りません。"
targetVerticals:
  - 金融・FinTech
  - 製造業
  - 基幹インフラ
  - 商社・サプライヤー管理
tags:
  - credit-screening
  - sanctions
  - aml
  - attribute-proof
  - zk-proof
relatedUseCases:
  - kyc-aml-selective-disclosure
  - financial-data-exfiltration
  - supply-chain-esg
---

# ユースケース: 取引先スクリーニング ── 与信・反社

## テーゼ

**取引先を、与信・反社リストと照合する。結果だけを受け取り、原本は渡さない。**

取引先の与信・反社の突合は「判定の根拠ごと共有すると漏洩・名誉毀損リスク、共有しないと各社が同じ相手を重複して照合」というジレンマを抱えています。Lemma は照合の**結果だけ**を ZK 証明として渡し、理由・スコア・照会履歴は出しません。受け取った側(取引先・監査・AI)は原本を見ずに「基準を満たす／要注意」を独立に検証でき、しかも後から改ざんできません。
