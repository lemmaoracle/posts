---
title: "LP クレーム属性証明"
abstract: "DeFi の流動性提供者属性（KYC 結果・地域・リスク許容）を、原本データを開示せずに ZK 証明として発行します。プロトコルは必要な属性述語だけを検証し、原本の本人確認記録を保持しないまま規制要件を満たせます。"
thesis: "プールに入る ≠ 検証されている"
pillar: regulatory-attribute-proof
industries:
  - fin
cardSummary: "LP の規制要件を、各 LP の本人確認データを開示せずに、属性単位の証明で満たす。"
targetVerticals:
  - DeFi プロトコル運営チーム
  - 規制下のデジタル資産プラットフォーム
  - クロスボーダー DeFi エクスポージャー担当
  - 暗号資産事業者のコンプライアンス・リスク責任者
tags:
  - defi
  - kyc
  - selective-disclosure
  - liquidity-provider
  - regulatory-attribute
relatedUseCases:
  - kyc-aml-selective-disclosure
  - defi-bridge-verification
  - financial-data-exfiltration
---

# ユースケース: LP クレーム属性証明

## テーゼ

**プールに入る ≠ 検証されている**

DeFi プロトコルはどのウォレットからの流動性も受け入れられます。それは、貢献者の規制適格性・jurisdiction・リスク許容が検証されたという意味ではありません。現場の選択肢は、検証なし（規制リスク）か、オフチェーンの完全 KYC 引き渡し（プライバシー喪失・カストディアン依存）の二択になりがちです。Lemma は LP 単位で ZK アテステーションを発行し、プロトコルが必要とする属性単位の述語のみ — 適格性・地域・リスク階層 — を検証し、原本の本人確認記録は保持しない構造にします。
