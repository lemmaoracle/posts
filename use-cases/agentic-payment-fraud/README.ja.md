---
title: "エージェント決済の濫用と、Trust 層の不在"
abstract: "AI エージェント決済のコールごとに、誰が・どの範囲で・いくらまで委任したかを暗号証明として添付する設計。"
thesis: "支払われた ≠ 委任された"
pillar: agent-authority-proof
industries:
  - fin
  - ai
cardSummary: "エージェント決済のコールごとに、委任関係・spend limit・jurisdiction を ZK 証明として埋め込む。"
targetVerticals:
  - AI エージェント運用
  - 金融サービス
  - 暗号資産取引所
  - API ベース決済基盤
tags:
  - agentic-payments
  - x402
  - trust402
  - zk-proof
  - agent-authority
relatedUseCases:
  - delegated-treasury
  - multi-agent-workflows
  - x402-commerce
---

# ユースケース: エージェント決済の濫用と、Trust 層の不在

## テーゼ

**支払われた ≠ 委任された**

x402・Stripe Agent SDK によって、AI エージェントが取引を通すこと自体は容易になりました。一方で、そのエージェントが本当に委任を受けていたのか、いくらまでの権限だったのか、jurisdiction 属性を満たしていたのかは、決済確定の前に検証されていません。Lemma のエージェント権限証明は、コールごとに「誰が・どの範囲で・いくらまで」を ZK 証明として添付し、API キーへの信頼ではなく、暗号的に検証された委任に基づいて決済を成立させます。
