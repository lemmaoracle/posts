---
title: "有資格者の配置照合 ── 資格・安全教育"
abstract: "作業者を、発行者が持つ資格・安全教育の要件と照合し、「配置してよい」という結果だけを受け取ります。資格の内訳・個人情報は発行者の管理下に残るので、名簿・証明書を集めて保管する負担と、現場ごとの重複確認を、構造的に取り除けます。"
thesis: "作業者を、配置要件と照合する。結果だけを受け取り、資格の内訳は渡さない。"
pillar: regulatory-attribute-proof
industries:
  - mfg
  - pub
cardSummary: "作業者を配置要件と照合し、結果だけを受け取ります。資格の内訳は渡りません。"
targetVerticals:
  - 製造業
  - 基幹インフラ
  - 建設・保守
  - 自治体（公共工事）
tags:
  - credential
  - safety
  - construction
  - attribute-proof
  - zk-proof
relatedUseCases:
  - credential-presentation
  - work-fitness-attestation
  - supplier-credential-verification
---

# ユースケース: 有資格者の配置照合 ── 資格・安全教育

## テーゼ

**作業者を、配置要件と照合する。結果だけを受け取り、資格の内訳は渡さない。**

配置の確認は「資格証・受講記録を集めて突き合わせれば保管と個人情報を抱える、現場任せにすれば無資格配置のリスクが残る」というジレンマを抱えています。Lemma は照合の**結果だけ**を ZK 証明として渡し、資格の内訳・受講記録・個人情報は出しません。受け取った側（元請・発注者・監査）は名簿を見ずに「配置要件を満たすか」を独立に検証でき、失効（期限切れ・取り消し）も追えるため「配置時は有効だったが今は失効」も検知できます。
