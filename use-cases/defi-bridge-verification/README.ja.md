---
title: "DeFiブリッジ検証"
abstract: "クロスチェーンブリッジが受信したメッセージは、暗号的に有効でありながら、意味的に正しいとは限らない。Lemmaは受信側システムが状態をコミットする前に、メッセージの起点を独立に検証する暗号レイヤを追加する。"
thesis: "暗号論理的に有効 ≠ 意味的に正しい"
pillar: verifiable-origin
targetVerticals:
  - リキッドステーキング/リステーキングプロトコル
  - クロスチェーンブリッジ
  - レンディングプロトコル
  - DEX
tags:
  - bridge-exploit
  - pre-execution-attestation
  - LayerZero
  - DeFi
relatedUseCases:
  - supply-chain-component-provenance
  - rag-content-provenance
  - financial-data-exfiltration
---

# ユースケース: DeFiブリッジ検証

## テーゼ

**暗号論理的に有効 ≠ 意味的に正しい**

クロスチェーンブリッジが受信したメッセージは、暗号的に有効でありながら、意味的に正しいとは限らない。Lemmaは受信側システムが状態をコミットする前に、メッセージの起点を独立に検証する暗号レイヤを追加する。
