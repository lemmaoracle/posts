---
title: "取引先の与信・反社チェック ── 結果だけを渡す"
abstract: "取引先の与信・反社判定を、中身を一切渡さず「基準を満たす／要注意」の ZK 証明として共有。判定根拠の漏洩リスクと、各社が同じ相手を重複チェックする無駄を構造的に外す。"
thesis: "結果は渡る。中身は渡らない。"
pillar: regulatory-attribute-proof
industries:
  - fin
  - mfg
  - sc
cardSummary: "反社・与信の判定結果だけを渡し、理由やスコアは出さずに確認できます。"
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

# ユースケース: 取引先の与信・反社チェック ── 結果だけを渡す

## テーゼ

**結果は渡る。中身は渡らない。**

取引先の与信・反社チェックは「判定の中身を共有すると漏洩・名誉毀損リスク、共有しないと各社が重複チェック」というジレンマを抱えています。Lemma は判定**結果だけ**を ZK 証明として渡し、理由・スコア・履歴は出しません。受け取った側(取引先・監査・AI)は中身を見ずに「基準を満たす／要注意」を独立に検証でき、しかも後から改ざんできません。
