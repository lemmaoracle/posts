---
title: "学歴・在籍照合 ── 学修歴・在籍/卒業"
abstract: "応募者を、発行者（学校）が持つ学歴・在籍の事実と照合し、「要件を満たす」という結果だけを受け取ります。成績・証明書の原本は発行者の管理下に残るので、原本の提出・保管に伴う漏洩リスクと、学校への照会待ちを、構造的に取り除けます。"
thesis: "応募者を、募集要件と照合する。結果だけを受け取り、原本は渡さない。"
pillar: verifiable-origin
industries:
  - pub
  - edu
cardSummary: "応募者を募集要件と照合し、結果だけを受け取ります。成績・証明書は渡りません。"
targetVerticals:
  - 大学・教育機関
  - 資格・検定団体
  - 企業 HR・採用
tags:
  - credential
  - education
  - w3c-vc
  - selective-disclosure
  - zk-proof
relatedUseCases:
  - kyc-aml-selective-disclosure
  - age-eligibility-verification
  - qualified-worker-attestation
---

# ユースケース: 学歴・在籍照合 ── 学修歴・在籍/卒業

## テーゼ

**応募者を、募集要件と照合する。結果だけを受け取り、原本は渡さない。**

学歴確認は「証明書の原本を提出させれば保管の責任と漏洩を抱える、学校へ照会すれば待ち時間が発生する」というジレンマを抱えています。Lemma は照合の**結果だけ**を ZK 証明として渡し、成績・学籍の個人情報は出しません。受け取った側（採用・審査部門・委託元）は原本を見ずに「学歴・在籍が要件を満たすか」を独立に検証でき、しかも後から改ざんできません。選択的開示は W3C VC 2.0 / BBS+ に整合します。
