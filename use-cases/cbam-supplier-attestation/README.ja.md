---
title: "CBAM サプライヤ属性証明"
abstract: "EU CBAM が要求する国・原産・カーボン強度の属性を、サプライヤの原本データを開示せずに ZK 証明として連鎖する設計。"
thesis: "申告される ≠ 証明できる"
pillar: regulatory-attribute-proof
industries:
  - mfg
  - sc
cardSummary: "CBAM 要件のサプライヤ属性を暗号証明として連鎖し、各社の生産データは社内に留めたまま規制対応する。"
targetVerticals:
  - EU 向け輸出を行う製造業
  - 鉄鋼・アルミ・セメント・肥料
  - 多階層サプライチェーン
  - 商社・ブローカー
tags:
  - cbam
  - eu-regulation
  - carbon-intensity
  - selective-disclosure
  - supply-chain
relatedUseCases:
  - eudr-traceability
  - supply-chain-esg
  - supply-chain-component-provenance
---

# ユースケース: CBAM サプライヤ属性証明

## テーゼ

**申告される ≠ 証明できる**

EU 炭素国境調整メカニズム (CBAM) は輸入者に対し、出荷ごとに製品が抱えるカーボン強度を証明することを求めます。現場の運用は PDF・申告書・メールの連鎖で、形式上は監査可能ですが、サプライヤの営業秘密にとっては構造的な脅威です。Lemma はサプライチェーン上で属性単位の ZK アテステーションを発行し、各社の生産データを社内に留めたまま、輸入者が CBAM 適合を暗号的事実に対して検証できるようにします。
