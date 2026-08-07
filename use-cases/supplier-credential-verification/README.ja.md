---
title: "仕入先資格照合 ── 許認可・ISO・証書"
abstract: "仕入先を、発行者が持つ許認可・ISO・保険の有効条件と照合し、「資格が有効」という結果だけを受け取ります。証書の原本は発行者の管理下に残るので、更新のたびの証書回収・保管の負担と、各社が同じ仕入先を重複して確認する無駄を、構造的に取り除けます。"
thesis: "仕入先を、資格の有効条件と照合する。結果だけを受け取り、証書は渡さない。"
pillar: regulatory-attribute-proof
industries:
  - mfg
  - sc
cardSummary: "仕入先を許認可・ISO の有効条件と照合し、結果だけを受け取ります。証書は渡りません。"
targetVerticals:
  - 製造業
  - 調達・サプライチェーン
  - 商社
  - 基幹インフラ
tags:
  - credential
  - iso
  - supply-chain
  - attribute-proof
  - zk-proof
relatedUseCases:
  - counterparty-screening
  - supply-chain-esg
  - kyc-aml-selective-disclosure
---

# ユースケース: 仕入先資格照合 ── 許認可・ISO・証書

## テーゼ

**仕入先を、資格の有効条件と照合する。結果だけを受け取り、証書は渡さない。**

サプライヤー審査は「更新のたびに証書を集め直すと手間と催促が増える、相手任せにすると有効性が担保されない」というジレンマを抱えています。Lemma は照合の**結果だけ**を ZK 証明として渡し、許認可・ISO・保険証券の原本は出しません。受け取った側（発注部門・監査人）は証書を見ずに「資格が有効か」を独立に検証でき、失効（期限切れ・取り消し）も追えるため「提出時は有効だったが今は失効」も検知できます。
