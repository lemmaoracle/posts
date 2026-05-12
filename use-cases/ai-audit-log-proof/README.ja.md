---
title: "AI 監査ログ証明"
abstract: "AI 判断の帰属を、判断時に ZK 証明で封じる。モデル更新後も遡れる説明責任を、30 分の会話から検討します。"
thesis: "監査される ≠ 説明できる"
pillar: verifiable-ai
targetVerticals:
  - 金融サービス
  - 保険
  - 医療AI
tags:
  - ai-audit
  - eu-ai-act
  - iso-42001
  - zk-proof
relatedUseCases:
  - rag-source-attestation
  - financial-data-exfiltration
---

# ユースケース: AI 監査ログ証明

## テーゼ

**監査される ≠ 説明できる**

金融・公共分野のAIが下した判断は、モデル更新で根拠が散逸します。EU AI Act・ISO 42001は説明可能な運用を要求しますが、平文ログだけでは決定の改ざん不能性を証明できません。LemmaはAI判断の帰属にZK証明を付け、モデルが変わっても過去判断の根拠を遡れる構造を残します。
