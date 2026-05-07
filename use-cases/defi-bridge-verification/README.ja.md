---
title: "DeFiブリッジ検証 — 実行前認証"
abstract: "クロスチェーンブリッジの悪用は、1つの構造的パターンを共有しています: トランザクションは暗号的に有効ですが、実行前に起点が検証されません。Lemmaは実行前認証を提供し、コミットを防ぎます。"
pillar: verifiable-origin
targetVerticals:
  - 流動性ステーキング / リステーキングプロトコル
  - クロスチェーンブリッジ
  - レンディングプロトコル
  - DEX
tags:
  - bridge-exploit
  - pre-execution-attestation
  - LayerZero
  - DeFi
relatedUseCases:
  - financial-data-exfiltration
---

# ユースケース: DeFiブリッジ検証 — 実行前認証

## 問題

2026年のクロスチェーンブリッジ悪用は、1つの構造的パターンを共有しています: トランザクションは暗号的に有効ですが、起点に関するクロスシステムの前提条件が実行前に検証されません。受信システムは、侵害可能であり、実際に侵害された信頼前提に基づいてコミットします。

**引き金となった事例:** Kelp DAO (2026年4月) — 攻撃者がLayerZeroの1-of-1 DVN設定を悪用し、rsETHで2億9200万ドルを盗みました。攻撃者がRPCノードを侵害し、正直なノードをDDoS攻撃し、偽のクロスチェーンメッセージを注入しました。レビューアは1つの署名のみが必要だったためそれを承認しました。事後のマルウェアは侵害されたRPCノードのログを削除しました。Lazarus Groupの関与。

**関連する2026年事例:** Drift Protocol (2億9500万ドル、Solana、管理者キー侵害)、および同じ構造的パターンに従う複数の小規模ブリッジ悪用。

## Lemmaの価値提案

| 機能 | Lemmaの役割 |
|---|---|
| 発行時の起点証明 | ✅ 中核 — 状態変更が主張するエンティティによって、主張するチェーン上で、検証可能な条件下で発行されたことのZK証明 |
| 実行前検証 | ✅ 中核 — 受信システムはコミット*前に*起点証明を検証; 証明が失敗した場合拒否 |
| 事後のフォレンジクス証拠 | ✅ 中核 — オンチェーン固定された認証はログ削除を生き延びる |
| リアルタイム悪用防止 | ✅ 直接的 — 偽のクロスチェーンメッセージは書き込み時に拒否され、事後に検知されるのではない |
| RPC侵害耐性 | ✅ 間接的 — 起点証明はアクションをそのソースにRPCの信頼性と独立して紐づける |

### 主要なポジショニング

**事後のフォレンジクスは症状を捕捉します。実行前認証はコミットを防ぎます。**

現在のスタック（フォレンジック追跡、ブロックレベル監視、凍結プリミティブ）は成熟し、高速化しています。しかしそのどれもコミットの瞬間を変えません。受信システムは依然として行動する前に起点を検証する標準的な方法を持ちません。Lemmaはその欠けたレイヤーを提供します。

## 内容

- **[scenario.md](./scenario.md)** — Kelp DAO攻撃タイムライン、Lemma導入前後
- **[architecture.md](./architecture.md)** — 実行前認証アーキテクチャ、LayerZero/DVN統合
- **[proof-points.md](./proof-points.md)** — 2026年ブリッジ悪用タイムライン、構造的パターン分析、規制コンテキスト
- **[pitch-deck.md](./pitch-deck.md)** — DeFiプロトコル向け1枚の販売アセット
- **[cross-case.md](./cross-case.md)** — TradFi（MetLife）ユースケースとの比較 — 統一された「証明レイヤー」メッセージ

## 公開記事との関係

このユースケースは[Bridge exploits in 2026: the case for verifiable origin proofs](https://lemma.frame00.com/blog/verifiable-origin-bridge-exploits-2026/)の議論を以下で拡張します:

- 具体的な攻撃タイムライン（パターン分析だけではない）
- 明示的な前後フロー（「何が変わるか」だけではない）
- LayerZero DVN統合アーキテクチャ（概念だけではない）
- TradFiとのクロスケース比較（DeFiだけではない）
- 営業対応可能なアセット（思想リーダーシップだけではない）

## 対象業界

- 流動性ステーキング / リステーキングプロトコル (LRT)
- クロスチェーンブリッジ (LayerZero, Wormholeなど)
- LRTを担保として受け入れるレンディングプロトコル (Aave, SparkLend, Compound)
- クロスチェーン交換機能を持つDEX
- 受信システムが送信システムの主張に基づいてコミットするあらゆるプロトコル

## 実装参照

- [example-origin](https://github.com/lemmaoracle/example-origin) — エンドツーエンドシナリオ: Kelp DAO / rsETH形状のフロー
- [example-x402](https://github.com/lemmaoracle/example-x402) — 証明バンドルプリミティブセット (Poseidon/BN254, BBS+/BLS12-381, Groth16)

## 関連ユースケース

- [金融データ流出防止](../financial-data-exfiltration/) — TradFi対応: 同じ構造的ギャップ、異なる表面