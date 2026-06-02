---
title: "RAGソース認証"
abstract: "AI 回答の引用ごとに、参照先文書バージョンの docHash を ZK 証明として紐付け。インデックス再構築後も引用整合性が暗号的に保たれます。30 分の会話から、御社のワークフローに乗るか確認します。"
thesis: "引用される ≠ 検証されている"
pillar: verifiable-ai
industries:
  - ai
cardSummary: "AI回答の引用ごとに参照元の版を紐付け、引用の真正性を保てます。"
targetVerticals:
  - 法務テック
  - 企業ナレッジマネジメント
  - 金融コンプライアンス
tags:
  - rag
  - citation-verification
  - source-attestation
relatedUseCases:
  - ai-audit-log-proof
  - rag-content-provenance
---

# ユースケース: RAGソース認証

## 問題

企業RAGシステムは引用付きで回答を提供するが、引用されたソースが変更されていない原本と一致する暗号論的保証はない。文書は進化し、エンベディングはドリフトし、インデックスは引用の完全性を保たずに再構築される。

**引用される ≠ 検証されている。** それがLemmaが埋める構造的ギャップである。

## 内容

- **[01-problem.ja.md](./01-problem.ja.md)** — 詳細な問題分析: なぜ引用がソースへの検証可能な紐付けを欠くか
- **[02-scenario.ja.md](./02-scenario.ja.md)** — 法務コンプライアンスシナリオ: 旧版ポリシー引用タイムライン
- **[03-architecture.ja.md](./03-architecture.ja.md)** — docHash + ZK証明紐付けアーキテクチャ
- **[04-proof.ja.md](./04-proof.ja.md)** — 証明される事実: 文書ハッシュ、バージョン、アクセスタイムスタンプ、引用-ソース紐付け
- **[05-related.ja.md](./05-related.ja.md)** — 関連ユースケースへのリンク（ピラーの説明付き）

## 対象業界

- 法務テック（判例研究、契約分析）
- 企業ナレッジマネジメント（ポリシーコンプライアンス、内部文書化）
- 金融コンプライアンス（規制報告、監査証跡）

## 実装リファレンス

- [example-rag-attestation](https://github.com/lemmaoracle/example-rag-attestation) — エンドツーエンドシナリオ: 引用証明生成と検証
- [example-x402](https://github.com/lemmaoracle/example-x402) — 証明バンドルのリファレンス実装（コミットメント・選択的開示・ZK証明）

## 関連ユースケース

- [AI監査ログ証明](../ai-audit-log-proof/) — 判断根拠のためのVerifiable AI対応ユースケース
- [RAGコンテンツ出典](../rag-content-provenance/) — 取り込みコンテンツのためのVerifiable origin対応ユースケース