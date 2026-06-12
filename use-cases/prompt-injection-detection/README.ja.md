---
title: "プロンプトインジェクションを、見せずに検知する。"
abstract: "不可視 Unicode・隠しコマンドで「人が見た入力」と「AI が読む入力」がずれるプロンプトインジェクション。Lemma で入力の normalized 形を hash 化し、人が意図した入力と AI が受け取った入力の visible_eq を runtime に検証。content を開示せずに、改ざんを検知。"
thesis: "AI が読んだ ≠ 人が見た"
pillar: verifiable-ai
industries:
  - ai
  - dev
cardSummary: "人が見た入力と AI が読む入力の整合性を、content を出さずに検証する。"
targetVerticals:
  - AI 導入（業種横断）
  - セキュリティ
tags:
  - prompt-injection
  - input-integrity
  - ai-security
  - verifiable-ai
relatedUseCases:
  - ai-document-isolation
  - ai-audit-log-proof
  - model-version-attestation
---

# ユースケース: プロンプトインジェクションを、見せずに検知する。

## テーゼ

**AI が読んだ ≠ 人が見た。**

不可視 Unicode や隠しコマンドを使うと、「人が画面で見た入力」と「AI が実際に読む入力」がずれます。プロンプトインジェクションの本質はこのズレです。content そのものを覗かずに、二つが一致していることを検証できる必要があります。

## Lemma が証明するもの

- 入力の正規化形（normalized form）のコミットメント
- 「人が見た入力 = AI が読む入力」の整合性（visibleEq）
- 適用したポリシーへの適合（satisfiesPolicy）
- 改ざんが検知された場合の実行停止

## 問題

プロンプトインジェクションへの対策は、典型的に次のいずれかで実装されます。

1. **入力フィルタ** — 既知の攻撃パターンをブロックする。だが不可視文字や新種のエンコーディングは抜け、「見た目は正常」な入力が AI には別物として届きます。
2. **出力モニタリング** — 不正な出力を後から検知する。だが攻撃はすでに実行された後で、入力の改ざん自体は証明できません。

どちらも、「AI が処理した入力が、ユーザーが意図したものと一致していた」ことを構造的に保証しません。

## Lemma がどう変えるか

Lemma は AI 推論の前段に**入力整合性の検証**を挟みます。

1. **正規化:** 入力を normalized form（Unicode NFC、空白・不可視文字の扱いを定義）に変換し、その指紋をコミットします。
2. **推論前:** 「人が意図した入力」と「AI が受け取る入力」の visibleEq を runtime で検証します。一致しなければ、推論は実行前に止まります。
3. **監査時:** すべての推論に、入力が改ざんされていなかったことの証明が付きます。入力 content を開示せずに、整合性を独立に検証できます。

入力の整合性が証明であり、visibleEq が境界です。ズレた入力は、AI に届く前に止まります。
