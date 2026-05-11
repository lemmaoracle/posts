---
title: "サプライチェーン部品来歴"
abstract: "部品ロット単位の来歴属性を、各サプライヤ階層から発行者署名付きで連鎖。組立側は ZK 証明として検証できます。30 分の会話から、御社の調達経路に乗るか確認します。"
thesis: "記録される ≠ 改ざんされていない"
pillar: verifiable-origin
targetVerticals:
  - 製造業
  - 自動車
  - 電子部品サプライチェーン
tags:
  - supply-chain
  - provenance
  - component-tracking
  - dpp
relatedUseCases:
  - rag-content-provenance
  - supply-chain-esg
---

# ユースケース: サプライチェーン部品来歴

## テーゼ

**記録される ≠ 改ざんされていない**

多階層サプライヤの来歴は表計算とPDFの連鎖で運ばれ、改ざんは検出されません。自律発注エージェントが読む属性は、信頼の根拠を欠いたままです。Lemmaは各ノードの属性を発行者署名と共に暗号化し、改ざん不能な来歴連鎖を残します。
