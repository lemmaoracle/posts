---
title: "RAG Content Provenance"
abstract: "RAG 取り込み時に文書の来歴を docHash と発行者署名として固定。AI 引用の真正性が暗号的に追跡可能になります。30 分の会話から、御社のパイプラインに乗るか確認します。"
thesis: "インデックス化される ≠ 信頼できる"
pillar: verifiable-origin
targetVerticals:
  - エンタープライズRAGプラットフォーム
  - ナレッジマネジメント
  - AIネイティブ企業
tags:
  - rag
  - content-provenance
  - doc-hash
  - data-integrity
relatedUseCases:
  - rag-source-attestation
  - supply-chain-component-provenance
---

# ユースケース: RAGコンテンツ来歴

## テーゼ

**インデックス化される ≠ 信頼できる**

エンタープライズRAGは社内文書を日々索引化しますが、原本との同一性も発行者署名も取り込みの瞬間に失われます。LemmaはAES-GCMで原本を暗号化し、docHashとCIDだけをインデックスに刻みます。AIが触れるのは、検証可能な来歴を持つ事実のみ。
