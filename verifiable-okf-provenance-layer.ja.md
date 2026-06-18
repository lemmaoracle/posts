---
slug: "verifiable-okf-provenance-layer"
date: "2026.06.15"
category: "Announcement"
section: "Essays"
title: "読める知識から、検証できる知識へ —— OKF に来歴を足す最初の実装を、OSS で公開しました"
abstract: "AI エージェントが読む「知識」を、組織やツールをまたいで同じ形式で共有する共通仕様 —— Google の Open Knowledge Format（OKF）が登場しました。ただし OKF が標準化したのは知識の表現と共有であり、「読める」ことと「信頼できる」ことは別の層です。誰が発行したか、改ざんされていないか、条件を満たすか —— その来歴は仕様の対象外です。Lemma は OKF 公開からわずか数日で、OKF に来歴（provenance）を足す最初の実装を OSS として公開しました。標準は一切変更せず、署名・検証・条件証明（Groth16）まで、誰でも自分のバンドルで試せる形でエコシステムに開きます。"
cover: "assets/cover-verifiable-okf.png"
tags:
  - open-knowledge-format
  - verifiable-okf
  - provenance
  - ai-agents
  - did
  - zero-knowledge-proof
  - open-source
---

**TL;DR**
AI エージェントが読む「知識」を、組織やツールをまたいで同じ形式で共有できる共通仕様（Google の OKF）が登場しました。ただし OKF が標準化したのは知識の表現と共有であり、「読める」ことと「信頼できる」ことは別の問題です。誰が発行したか、改ざんされていないか、条件を満たしているか——その来歴は、仕様の対象外です。**OKF 公開からわずか数日。Lemma は、OKF に来歴（provenance）を足す最初の実装を、OSS（オープンソース）として公開します。** 標準は一切変更せず、誰でも使える形でエコシステムに開きます。

Represented ≠ trusted.

---

## はじめに —— 「読める」と「信頼できる」を分けて考える

AI エージェントが、人の代わりに社内外のデータを読んで判断する。その使い方が実務に入りつつあります。エージェントが正しく動くかどうかは、読み込む「知識」——テーブルの定義、指標の意味、業務ルールやランブック——の質に大きく依存します。

先週 Google が公開した **Open Knowledge Format（OKF）** は、この知識を表現するための共通仕様です。各概念を1ファイルの markdown ＋ YAML frontmatter で記述し、組織やツールをまたいで同じ形式で受け渡しできるようにします。特定の SDK もランタイムも要求せず、最小限の取り決めだけで相互運用を成立させる、よく整理された設計です。

ここで、OKF が扱う範囲と扱わない範囲を分けて見る必要があります。OKF が標準化したのは知識の**表現と共有**です。一方で、その知識が**信頼に足るか**を判断するために必要な次の3点は、仕様の対象外です。

- **来歴（Origin）** —— その概念を発行したのは誰か。`resource` の URL は所在を指すポインタであって、発行者による署名ではありません。
- **整合（Integrity）** —— 本文が発行時点から改変されていないか。
- **条件（Conditions）** —— 個人情報を含まない、ガバナンス承認済み、有効期限内、といった規則を、元データを開示せずに満たしていると示せるか。

同じ組織の内部だけで使う知識であれば、これらは前提として置けます。しかし、別の組織や別のエージェントが発行した知識を、監査・KYC/AML・決済の判断に使う場合、表現の互換性だけでは不十分です。発行者・整合・条件を、機械が検証できる形で知識そのものに添える必要があります。

Lemma は、この層を実装しました。**OKF に来歴（provenance）を足す、最初の実装です。** OKF の仕様は一切変更せず、供給者が任意で付与できる拡張と、それを検証するツールとして公開します。**すべて OSS（オープンソース）として公開し、誰でも自分のバンドルで試し、自分のツールに組み込めます。** OKF というオープン標準の上に、検証可能性をオープンに積み上げる——エコシステムへの貢献として開きます。

> ここから先は、開発者向けに設計と実装を説明します。

---

## ▸ 背景：rail・format に、trust を重ねる

この数週間で、エージェント経済のインフラが一気に具体化しました。x402 はエージェントが API コールごとに支払う rail をマルチチェーンへ広げ（Injective、XRPL ほか）、Google Cloud は OKF で企業データの「目次」をエージェントが読むための共通フォーマットを示しました。同時に「Know Your Agent（KYA）」——エージェントの身元・来歴・権限を検証可能にする枠組み——が企業トレンドとして立ち上がっています。

構造で見ると、3つの層が見えてきます。決済の rail が「支払えること」を、知識フォーマットが「読めること」を標準化しました。そこに「そのデータが信頼に足るか」を確かめる層が重なると、エージェントの判断を監査・KYC/AML・決済の現場に、安心して載せられるようになります。**Verifiable OKF は、この3つ目の層を、知識フォーマットの側で担います。**

OKF の場合、目次が組織の境界を越えた瞬間に、フォーマット自体では答えられない問いが3つ現れます。

- **来歴（Origin）** —— この概念を発行したのは誰か。`resource` の URL はポインタであって、署名された証明ではありません。
- **整合（Integrity）** —— 本文は発行時のままか、途中で書き換わっていないか。
- **条件（Conditions）** —— 個人情報を含まない／ガバナンス承認済み／有効期限内、といった規則を、元データを開示せずに満たしていると言えるか。

監査や KYC/AML、サプライチェーンのように規制のかかる業務では、「ファイルにそう書いてあった」では不十分です。

Cryptographically valid ≠ semantically right.

---

## ▸ What we're shipping

Lemma が公開するのは **Verifiable OKF** —— OKF に来歴（provenance）を付与する、**供給者側の拡張ツール**です。

OKF の仕様は、供給者が任意のキーを追加することを最初から開いています（*"Producers may add any extra keys; consumers should preserve them and not reject documents with unknown fields."*）。私たちはこの既存の拡張機構を使って `provenance` キーと、署名・整合・条件証明を検証するツールを提供します。供給者はこれまでどおり OKF を書き、その上に検証可能な来歴を任意で付与するだけです。

提供するのは、OKF 自身が producer / consumer / visualizer の参照実装を出しているのに倣った3点です。

- **Signing producer** —— 既存の OKF バンドルをそのまま受け取り、概念ごとに整合ハッシュと署名を付与します。供給者は今までどおり OKF を書くだけです。
- **Verifying consumer** —— 消費するエージェントが、整合・発行者・必要な条件証明を検証し、概念ごとに verified / unverified / failed の状態を返します。
- **Proof-status visualizer** —— バンドルのグラフに概念ごとの検証バッジを表示する、バックエンド不要・データがページ外に出ない単一 HTML。

---

## ▸ How it works（開発者向け）

供給者は frontmatter に任意の `provenance` キーを足します。中身はすべて任意で、出せるものだけを出します。

- 整合は本文を正準化した上での **SHA-256**（`content_hash`）。
- 署名は **Ed25519**。発行者 frontmatter 全体と本文を、**JCS（RFC 8785）** で正準化したペイロードに対して一括で結びます。これで title や条件証明だけを差し替える、といった改ざんができません。
- 発行者は **did:web / did:key** の DID。これはそのまま **KYA（Know Your Agent）** の、所有者に紐づく検証可能な識別子として機能します。
- 条件証明はプラガブルです。初回として **Groth16 over BN254（snarkjs）** によるオフライン検証と、「個人情報を含まない（`contains_no_pii`）」を証明するデモ回路を同梱しました。検証に必要なのは verification key だけで、proving key も circom もネットワークも要りません。属性の選択的開示（**BBS+ over BLS12-381**）も仕様に定義済みです。「必要な属性だけを見せる」という選択的開示は、OKF の「必要な知識だけを共有する」という思想とそのまま重なります。

検証はネットワークを必要としません。**コアは完全にオフラインで動き、特定のチェーンにもベンダーにも依存しません。** 公開アンカーのような、より強い保証が必要な場面に備えて、チェーンへのアンカリングを*任意の*層として後から差せる設計にしてありますが、コア機能はアンカリングなしで完結します。

`provenance` を理解しない消費者は、それを無視してただの OKF として読みます。付いていないバンドルは、今までどおり完全に妥当です。**何も壊しません。**

### 公開しているもの（今すぐ動きます）

- Verifiable OKF 拡張仕様 v0.3（`provenance` キー、正準化、署名、検証アルゴリズムをノーマティブに規定）と `provenance.schema.json`
- `@verifiable-okf/canon`（本文正準化＋JCS による署名ペイロード生成）
- `@verifiable-okf/signer`（`vokf-sign`）/ `@verifiable-okf/verifier`（`vokf-verify`・Ed25519・SHA-256・did:web / did:key、概念ごとの検証ステータス）
- conformance fixtures（signed / tampered の golden 例）とテスト
- `@verifiable-okf/visualizer`（バンドル→概念ごとの検証バッジ付き単一オフライン HTML）
- `@verifiable-okf/attest-zk-groth16`（Groth16 over BN254 / snarkjs・完全オフライン）と `contains_no_pii` デモ回路
- worked example（GA4 の実 OKF バンドルを署名→検証→レポート生成）

**署名から検証まで、エンドツーエンドで動きます。** 別のエージェントが——did:key なら完全オフラインで——整合・発行者・条件証明を検証し、概念ごとに検証バッジ付きのレポートを出すところまで完成しています。

---

## ▸ What's next

1. 属性の選択的開示（BBS+ over BLS12-381）と、`contains_no_pii` 以外の条件証明回路を追加。
2. 本番署名鍵の KMS/HSM 連携と、Python 版の参照実装。
3. W3C VC / DID / C2PA との相互運用と、失効（revocation）モデル。
4. OKF コミュニティへ「標準の拡張機構を使って検証層を作った」という**事例報告**を共有（仕様変更の要求はしません）。

---

## ▸ Who this is for

x402 のビルダー、MCP 開発者、AI エージェントの運用者、OKF バンドルを公開するデータ供給者、そしてシステムをまたいで信頼を運ぶワークフローを扱うエンタープライズチームへ——一緒に検証可能性のレイヤーを形にしたい方からのご連絡をお待ちしています。署名・検証を自分のバンドルで試したい、自社のユースケースで条件証明を設計したい、どちらも歓迎です。動くコードは今日から触れます。

**Built for decisions that matter.**

---

## ▸ Resources

- Verifiable OKF リポジトリ: https://github.com/lemmaoracle/verifiable-okf
- 拡張仕様 SPEC v0.3: https://github.com/lemmaoracle/verifiable-okf/blob/main/SPEC.md
- worked example（GA4 バンドルを署名→検証→レポート）: https://github.com/lemmaoracle/verifiable-okf/tree/main/examples/okf-bundle

— The Lemma team
