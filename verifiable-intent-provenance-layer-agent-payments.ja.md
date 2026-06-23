---
slug: "verifiable-intent-provenance-layer-agent-payments"
date: "2026.06.22"
category: "Industry"
section: "Essays"
title: "意図の証明に、根拠の検証を重ねる──AIエージェント決済時代の検証可能AI"
cover: "assets/cover-industry.png"
abstract: "AP2 と Mastercard の Verifiable Intent によって、AIエージェント決済で「意図の真正性」を証明する標準が揃いつつあります。実運用では、その上に「エージェントの出力が何を根拠にしているか＝来歴」を独立に検証する層が要ります。本稿は意図の真正性（consent layer）と根拠の検証可能性（provenance layer）を整理し、検証可能AIの観点から、標準の上に重ねる来歴証明の層を解説します。"
tags:
  - verifiable-ai
  - agent-payments
  - ai-provenance
  - verifiable-intent
  - ap2
  - zk-proof
  - agent-authority
---

**TL;DR**
AIエージェントが「何を認可されたか」を改ざん耐性のあるクレデンシャルとして証明する標準が、決済領域で一気に揃いつつあります。AP2 と Mastercard の Verifiable Intent によって、意図の真正性はもう業界の前提になりました。ここに一層を重ねると、AIエージェント決済はさらに実務に出しやすくなります。それが「真正な意図にもとづくエージェントの出力が、何を根拠にしているか」を独立に検証する層――**AIの来歴証明**です。本稿では、揃った層と、その上に重ねる検証可能AIの層を整理します。

> Pays ≠ trustworthy. — 支払えること（認可）と、信頼に足ること（根拠）は別の層。

**Built for decisions that matter.**

---

## ▸ いま決済で起きていること：AIエージェント決済と「意図の証明」標準（AP2 / Verifiable Intent）

2026 年に入って、AIエージェント決済の周辺で「意図（intent）を証明する」標準が急速に揃いました。

Google の **Agent Payments Protocol（AP2）** は、エージェントによる購買を 3 つの署名済み Mandate ―― Intent Mandate（利用者が何を望んだか）、Cart Mandate（エージェントが組んだ内容）、Payment Mandate（どう支払うか）―― として表現します。いずれも W3C Verifiable Credential として署名され、検証可能な JSON として当事者間を渡っていきます。

これに対し Mastercard が Google と共同開発した **Verifiable Intent** は、「利用者の identity・元の指示・取引の結果」を一つの改ざん耐性レコードに束ね、第三者が検証できる証拠（evidence）へと変換する暗号クレデンシャル枠組みです。FIDO Alliance・EMVCo・IETF・W3C の標準の上に構築され、必要最小限だけを開示する Selective Disclosure を用います。2026 年 4 月 28 日に AP2 とともに FIDO Alliance へ寄贈され、60 を超える組織が名を連ねました。

AP2 が「意図をどう作り、どう共有するか」を定めるとすれば、Verifiable Intent は「意図をどう証明するか」を定める ―― 業界はそう整理し始めています。AIエージェント決済の土台が、いま一気に標準化へ向かっています。

## ▸ 証明されるのは「意図の真正性」── では、その上に何を重ねるか

これらの標準が保証しているのは、いずれも「意図の真正性」の層です。

「この支払いは、利用者が確かに認可したものか」「エージェントは署名された範囲を超えていないか」。これらは、3 つの Mandate が W3C Verifiable Credential として非対称署名されることで、たしかに守られます。価格や品目をエージェントが事後に書き換えれば、クレデンシャル全体が無効になる ―― tamper-evident な設計です。

実運用では、ここにもう一つの要件が加わります。「その意図にもとづいてエージェントが返した出力・参照した事実が、何を根拠にしているか」です。この要件を満たすほど、AIエージェントの判断は実務に投入しやすくなります。支払いが正しく署名されていることと、その判断材料の出所が辿れることは、別の層の問題です。

> Cryptographically valid ≠ semantically right. — 暗号的に正しいことと、判断の根拠が確かなことは、別。

現行のセキュリティ・決済の運用モデルでは、この「根拠の検証可能性」は、まだ独立した一層として標準化されていません。決済の標準化が進むほど、その上に必要な**検証可能AI**の層の要件は、より明確になります。

## ▸ どこまで証明され、どこに検証の層を重ねるのか（consent layer と provenance layer）

技術プリミティブのレベルで見ると、境界はより明確になります。

**consent layer（意図の真正性）** ―― Verifiable Intent や AP2 の Mandate は、W3C Verifiable Credential を基盤にし、選択的開示には BBS+ over BLS12-381 のような署名スキームが用いられます。担保するのは「誰が、何を認可し、その記録が改ざんされていないか」。identity と consent の真正性を、portable な証拠に変えるところに価値があります。

**provenance layer（根拠の検証可能性）** ―― Lemma が扱うのは、その隣にある問いです。「エージェントが参照した事実・モデルの出力が、検証可能な来歴（provenance）を持つか」。ここでは Poseidon over BN254 をハッシュに、Circom で記述した回路を Groth16 で証明する、といった**来歴証明**のプリミティブが効いてきます。出力そのものの意味を保証するのではなく、出力の根拠が辿れることを暗号的に示すアプローチです。

意図の真正性（consent layer）と、根拠の検証可能性（provenance layer）。この二つは補完関係にあり、両方を重ねてはじめて「信頼して任せられる判断」に届きます。Verifiable Intent の登場は、前者が業界標準になった証であり、同時に後者を重ねる好機でもあります。

> Models change. Proofs remain. — モデルは入れ替わります。証明は残ります。

だからこそ、検証の層はモデルから独立していなければならない ―― これが Lemma の出発点です。Lemma の[4 つの柱](https://lemma.frame00.com/ja/pillars)のうち、ここで効くのは [Pillar 01・来歴証明](https://lemma.frame00.com/ja/pillars/verifiable-origin/) と [Pillar 03・エージェント権限証明](https://lemma.frame00.com/ja/pillars/agent-authority-proof/) です。

## ▸ Lemma の強み（3点）

- **標準と並ぶ（相互運用）**：AP2 / Verifiable Intent が依拠する W3C Verifiable Credential に加え、**MCP・A2A・x402・C2PA** と並んで動く設計。consent layer を置き換えず、その上に provenance layer を噛み合わせます。
- **開示せず検証（prove ≠ reveal）**：原文を渡さず、Lemma も平文を見ません。受け取るのは「根拠が辿れる」証明だけ。確率的な検出ではなく、決定的な証明です。
- **モデル非依存**：Models change. Proofs remain. 検証の層をモデルから独立させ、モデルが入れ替わっても根拠は残ります。

## ▸ 対エージェントの「信頼」を底上げする

AIエージェントに任せられる範囲は、そのエージェントを「どこまで信頼できるか」で決まります。Lemma の [Agent Authority Proof（Pillar 03）](https://lemma.frame00.com/ja/pillars/agent-authority-proof/) は、エージェントの権限と、判断の根拠（来歴）を検証可能にします。

「誰の・どの権限のエージェントか」を確かめ、「何を根拠に動いたか」を後から辿れる。これは脅威を数える話ではなく、エージェントを安心して実務に出すための、信頼の底上げです。意図が証明される時代に、その意図にもとづく行動の確からしさまで重ねられます。

## ▸ どこで効くか（ユースケース）

来歴の検証は、すでにある業務の上にそのまま乗ります。

- **金融・FinTech**：KYC/AML を、書類を渡さず「条件を満たす」証明だけで完結。エージェント決済の相手が「AML 審査済みの組織か」をミリ秒単位で確認でき、与信・審査の説明コストも下げられます。[→ KYC/AML 選択的開示](https://lemma.frame00.com/ja/solutions/use-cases/kyc-aml-selective-disclosure/)
- **監査・コンプライアンス**：AI が「何を根拠に判断したか」を、後から再現・提出できる形で残す。監査対応・規制報告に出せるのは、検出スコアではなく事前の証跡です。[→ AI 監査ログ証明](https://lemma.frame00.com/ja/solutions/use-cases/ai-audit-log-proof/)
- **製造・基幹インフラ／調達**：品質・検査記録や取引先の証明を、原本を抱えずに確認。サプライヤーの認証・信用を、データを預からずに検証できます。[→ サプライチェーン ESG](https://lemma.frame00.com/ja/solutions/use-cases/supply-chain-esg/)
- **自治体・公共**：個人データを見ずに、必要な属性だけを検証して手続きを進める。給付・窓口・徴収などを、生の個人情報を保持せずに回せます。
- **AIエージェント運用（業種横断）**：連携先エージェントの権限・ポリシー・来歴を、接続時に一度ではなくタスクごとに検証。「渡しっぱなしの権限」を避けられます。

業種・タスク横断の全ユースケースは[こちら](https://lemma.frame00.com/ja/solutions/use-cases)。

## ▸ 開発者は数行から試せる

検証可能AIは、基盤を入れ替えなくても始められます。

- **[Seal](https://lemma.frame00.com/ja/seal/)**：鍵ではなく証明を送る ZK サインイン SDK。アプリや AIエージェントのスタックに数行で組み込めます。
- **[Trust402](https://lemma.frame00.com/ja/trust402/)**：鍵を持たずに AI を動かすキーレス認可。タスク単位の権限委譲を、x402 / MCP と組み合わせて使えます。
- まずは [Dashboard](https://dashboard.lemma.workers.dev) と[仕様書](https://lemma.frame00.com/ja/guides)から。

---

### ▸ デモで確かめる：あなたのエージェントの「根拠」は辿れますか

consent layer は標準が担います。**provenance layer は、いま動くデモで確かめられます。**

- ▶ **来歴証明のデモを見る**：[demo.lemma.frame00.com](https://demo.lemma.frame00.com)
- ▶ **自社のユースケースで相談する（30分・機微データの開示不要）**：[デモをリクエスト / Discovery Call](https://tally.so/r/EkBqDX?utm_source=blog&utm_medium=cta_mid&utm_campaign=verifiable_intent)

意図が証明される時代に、その判断の根拠まで検証可能にする設計を、一緒に描きます。

---

## ▸ これから取り組む方向性

- consent layer（Verifiable Intent / AP2）と provenance layer（来歴証明）の接続点を設計し、相互運用の参照実装を示す
- エージェント来歴（[Agent Authority Proof / Pillar 03](https://lemma.frame00.com/ja/pillars/agent-authority-proof/)）を、検証可能な形で提供する
- 規制側の要請（高リスク AI のログ義務など）と業界標準の収斂点を、Regulatory Attribute Proof（Pillar 04）として接続する
- 業種横断（製造・基幹インフラ・金融など）の来歴証明ユースケースを、順次デモとして公開する

エンタープライズ向けはカスタム導入で提供します。自社の要件に合わせた検証可能AIの構成は、デモリクエスト / Discovery Call でご相談ください。

## ▸ FAQ

**Q. Verifiable Intent と AP2 は何が違うのですか？**
AP2（Agent Payments Protocol）は「意図をどう作り、どう共有するか」を、3 つの署名済み Mandate として定めます。Verifiable Intent は「その意図をどう証明するか」を、利用者の identity・指示・結果を束ねた改ざん耐性レコードとして定めます。両者は補完関係で、2026 年 4 月に FIDO Alliance へ寄贈されました。

**Q. 「意図の証明」が揃えば、AIエージェントの判断は信頼できますか？**
意図の真正性（誰が何を認可したか）は証明できます。一方で、エージェントが参照した事実やモデル出力の**根拠（来歴）が辿れるか**は別の層です。両方を重ねてはじめて、判断を実務に出しやすくなります。

**Q. 検証可能AI（AI来歴証明）は、具体的に何を証明するのですか？**
出力の「意味の正しさ」そのものではなく、**出力が何を根拠にしたか＝来歴**を暗号的に検証します。Lemma は Groth16 / Circom / Poseidon over BN254 などのプリミティブで、原文を開示せずに「根拠が辿れること」を示します。

**Q. 試すには？**
[デモ環境](https://demo.lemma.frame00.com)で来歴証明を確認できます。自社のユースケースに沿った検証は[デモリクエスト / Discovery Call](https://tally.so/r/EkBqDX?utm_source=blog&utm_medium=faq&utm_campaign=verifiable_intent)から（機微データの開示は不要です）。

## ▸ 一緒に設計しませんか（CTA）

x402 builder、MCP 開発者、AIエージェントを運用するチーム、あるいはシステムをまたいだ信頼のワークフローを構築するエンタープライズの方へ。意図が証明される時代だからこそ、その意図にもとづく**判断の根拠を検証する層**を重ねる価値があります。

- ▶ **デモをリクエスト / Discovery Call（30分）**：[こちら](https://tally.so/r/EkBqDX?utm_source=blog&utm_medium=cta_tail&utm_campaign=verifiable_intent)
- ▶ **来歴証明のデモを見る**：[demo.lemma.frame00.com](https://demo.lemma.frame00.com)

**Built for decisions that matter.**

## ▸ 関連リンク

- [エージェント間の暗号的信頼チェーン：A2A連携が変えるAPI経済](https://lemma.frame00.com/ja/blog/agent-cryptographic-trust-chain-a2a-api-economy/)
- [x402 のためのトラストレイヤー](https://lemma.frame00.com/ja/blog/x402-trust-layer-for-autonomous-agent-payments)
- [検証可能なAI：信頼の出所を暗号で示す新しいRAG設計](https://lemma.frame00.com/ja/blog/verifiable-ai-cryptographic-rag-design)
- [Lemma の4つの柱](https://lemma.frame00.com/ja/pillars) ／ [Critical Brief（実事案の構造分析）](https://lemma.frame00.com/ja/critical/briefs)

― The Lemma team
