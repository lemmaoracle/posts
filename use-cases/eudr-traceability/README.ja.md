---
title: "EUDR トレーサビリティ"
abstract: "EUDR が要求する原産地・伐採時期・許可属性を、サプライヤ間で原本を共有せずに ZK アテステーションとして連鎖検証する設計。"
thesis: "追跡される ≠ 証明できる"
pillar: regulatory-attribute-proof
industries:
  - sc
  - mfg
cardSummary: "EUDR 対象品目の属性を、多階層サプライチェーンで原本を渡さずに連鎖検証する。"
targetVerticals:
  - EUDR 対象品目を EU 向けに扱う事業者
  - コーヒー・カカオ・パーム油・大豆・ゴム・牛肉・木材
  - 協同組合・商社
  - EUDR デューデリ義務を負う輸入者
tags:
  - eudr
  - eu-regulation
  - supply-chain
  - provenance
  - selective-disclosure
relatedUseCases:
  - cbam-supplier-attestation
  - supply-chain-esg
  - supply-chain-component-provenance
---

# ユースケース: EUDR トレーサビリティ

## テーゼ

**追跡される ≠ 証明できる**

EUDR は、EU 向け出荷ごとに原産国・伐採時期・許認可状態のデューデリジェンスを求めます。文書ベースの運用は「追跡されている」と「証明できる」を同一視しがちです。商社は paper を再現できても、それが何を指しているかは証明できません。Lemma は各サプライヤの原本データに対して ZK アテステーションを発行し、輸入者の due-diligence 申告を、申告書の積み重ねではなく暗号的な連鎖として成立させます。
