---
slug: "ppsi-stablecoin-aml-kyc-third-layer"
date: "2026.05.15"
category: "Business Strategy"
section: "Essays"
title: "x402 に第3層を足す — ステーブルコイン事業者の選択肢"
cover: "assets/ppsi-stablecoin-layer3.jpg"
abstract: "ステーブルコインを扱う事業者にとっての「Layer 3」 — エージェント決済における顧客理解を、データを送らずに証明する設計。米国 PPSI 規則案・EU MiCA 対応を含む複数ユースケースを、Lemma の ZK 属性証明で単一基盤に統合する選択肢。"
tags:
  - ppsi
  - genius-act
  - stablecoin
  - kyc-aml
  - x402
  - payments
  - zk-proof
  - compliance
---

**TL;DR**

x402 を採用したエージェント決済スタックは、署名検証と支出制御まで揃いました。残されているのは「顧客理解」の層で、誰が誰に支払っているかを、原データを送らずに証明する仕組みです。2026年4月に米財務省（FinCEN・OFAC）が公表した PPSI NPRM — Permitted Payment Stablecoin Issuer 向け規則案 — は、ステーブルコイン発行者にこの層への対応を求める内容になっています。Lemma の ZK 属性証明レイヤーは、x402 + AWS Bedrock AgentCore Payments の上に補完的に乗る形で Layer 3 を埋め、PPSI 対応・A2A エージェント決済・規制監査証跡など複数のユースケースを単一基盤で実装可能にします。

決済プロトコルは揃いました。支出ガバナンスも揃いました。残されているのは Layer 3 です。

*事実関係は 2026-05-12 時点のものです。preview scope や法令解釈は今後変動しうる点をご承知おきください。*

---

## 1. PPSI NPRM と x402 スタックの現在地

ステーブルコインを扱う事業者の視点で、規制と技術の両面の動きを概観します。

### 規制側 — PPSI NPRM

FinCEN（米財務省金融犯罪取締ネットワーク）と OFAC（外国資産管理局）が、GENIUS Act の下で stablecoin 発行者に対する AML/CFT および制裁プログラム要件を実装するための共同 NPRM を 2026-04-08 に公表しました。読み手にとって重要な日付は2つあります。**コメント期限が 2026-06-09**、**最終規則は公布から12ヶ月後に発効する見込み**です。

NPRM の中核は、PPSI を Bank Secrecy Act 上の financial institution として扱うことにあります。issuer に求められる主な要件は以下です:

- **顧客識別および顧客デューデリジェンス（CIP / CDD）**: 顧客の本人確認、リスク評価、ベネフィシャル・オーナーの特定。
- **疑わしい取引の届出（SAR）**: 規定の閾値および typology に該当する取引の継続的モニタリングと報告。
- **記録保持**: 取引記録および顧客書類の保管。
- **OFAC sanctions compliance program**: 上級管理職のコミットメント、リスク評価、内部統制、テスト・監査、トレーニングの5要素。

ここで規則案の特徴として読み取れるのは、これらの義務が「stablecoin の発行体それ自体」に課される設計になっている点です。

### 技術側 — x402 決済スタック

エージェント駆動の決済スタックは、過去6ヶ月で急速に層を重ねてきました。3層構造で整理します。

| 層 | 役割 | 現状（2026-05時点） | 提供主体 |
|---|---|---|---|
| Layer 1 | HTTP 層の決済プロトコル、署名検証 | 仕様確立、SDK 脆弱性は修正済み | x402 Foundation / Coinbase / Cloudflare |
| Layer 2 | spending limits、audit trail、wallet 連携 | preview として稼働 | AWS Bedrock AgentCore Payments（Coinbase CDP / Stripe Privy） |
| Layer 3 | AML/KYC 属性、CDD、beneficial ownership、OFAC スクリーニング | preview scope 外、上位委譲 | 発行者・MSB・採用企業（PPSI 下では実装が求められる立場） |

このスタックを構成する各プレイヤーの役割を整理すると、以下の通りです。

- **x402 Foundation** — プロトコル仕様（HTTP 402 Payment Required を核とする決済プロトコル）
- **Coinbase** — CDP wallet、SDK、x402 の共同推進
- **Stripe** — Privy wallet、AgentCore Payments の共同開発
- **AWS** — Bedrock AgentCore Payments による operational layer（spending limits / audit trail / wallet 連携）
- **Lemma** — attribute proof レイヤー（x402 Header 拡張として動作）

この5者の組み合わせで、「決済プロトコル → 支出ガバナンス → 顧客理解」までを一貫して扱うスタックが成立します。Layer 1 と Layer 2 が決済プラットフォーム側でカバーされ、Layer 3 が上位委譲されている設計は、決済自動化と顧客理解が本来別の関心事であることを反映した、合理的なアーキテクチャ分業といえます。決済プラットフォームが KYC を内包すると、ベンダーロックインや管轄問題を生むためです。

ただし PPSI NPRM が施行されると、その委譲先（ステーブルコイン発行者・MSB ライセンスを持つ採用企業・middleware 提供者）が、自身の AML/CFT プログラムの一部として、エージェント経由の取引にも CDD と SAR を適用する立場になります。

---

## 2. 委譲先の責任 — 米国法律事務所の解釈

PPSI NPRM が委譲モデルに与える影響については、Lemma 独自の解釈ではなく、米国の主要法律事務所が公開している分析を参照することをおすすめします。

- **Mayer Brown**: issuer が直接顧客と接していない場合（典型的にはエージェント・middleware を介する場合）であっても、issuer が自身のリスク評価に基づいて取引監視と CDD を実装する必要があると指摘しています。
- **Holland & Knight**: エージェント駆動取引が増加する局面では、issuer が間接的な顧客アクセス経路についても明示的なリスク評価と controls を設けることが期待されると分析しています。
- **Fried Frank・PwC**: 委譲モデル下で issuer が間接的取引にも責任を負う構造を、別の角度から確認しています。

複数の独立した法的解釈が、Layer 3（顧客理解の属性）を委譲先である issuer・MSB・採用企業のいずれかが担う方向を示している、と読むことができます。具体的に何をどこまで実装すべきかは、各事業者が自社の法務・コンプライアンス体制と照らして判断する領域です。

本記事では、この判断を踏まえて選択肢のひとつとなる技術アーキテクチャを次節で示します。

---

## 3. Lemma 導入で Layer 3 を埋める

Layer 3 が満たすべき要件と、Lemma の attribute proof レイヤーがどう応えるかを、順にご紹介します。

### Layer 3 が満たすべき要件

- エージェント／その背後の主体が KYC を通過していることの証明
- 必要に応じて beneficial ownership・国籍・制裁リスト非掲載の属性
- これらを取引ごとに送付すること自体は privacy 上のコスト（PII を全送信するのは違法・実務的に不可）
- ゆえに **属性証明（attribute proof）** が必要となります — 「この主体は KYC を通過している」「制裁リストに含まれていない」を、原データを送らずに証明する仕組みです

### Lemma の ZK 属性証明レイヤー

Lemma が提供しているのは、まさにこの Layer 3 の attribute proof レイヤーです。BBS+ 署名と zkSNARK による選択的開示で、issuer が要求する属性のみを HTTP 層に乗せて送付する仕組みになっています。仕様上の特徴は以下の通りです:

- **ウォレット非依存**: Coinbase CDP wallet / Stripe Privy wallet いずれの経路でも、x402 の Header 拡張として動作します
- **HTTP 層完結**: チェーン上の追加検証を必須としません。発行者または検証者は属性 proof を HTTP Header から取り出して検証します
- **選択的開示**: 「KYC 済み」だけを開示することも、「KYC 済み + 居住国 + 制裁リスト非掲載」のように属性集合を指定することも可能です
- **PPSI 要件との対応**: CDD・beneficial ownership・OFAC スクリーニングのいずれもが、属性として表現可能です

具体的な統合は、`extensions.lemma` Header を x402 response に同梱する設計を採用しています。x402 SDK の import は 1 行で済み、`@lemmaoracle/x402` を呼び出すことで attribute proof の生成と検証が AgentCore Gateway や任意のエージェント runtime で動きます。

```ts
// x402 response に Lemma attribute proof を同梱
import { x402 } from "@x402/sdk";
import { attachAttributeProof } from "@lemmaoracle/x402";

const response = await x402.respond({
  amount: "1.00",
  asset: "USDC",
  recipient: "0x...",
  extensions: {
    lemma: await attachAttributeProof({
      attributes: ["kyc_verified", "ofac_clear"],
      issuer: "did:lemma:issuer/...",
    }),
  },
});
```

### x402 + AgentCore Payments + Lemma で広がるユースケース

x402 のプロトコル、AgentCore Payments の支出ガバナンス、Lemma の attribute proof を組み合わせることで、ステーブルコインを扱う事業者の複数のワークフローを単一基盤で支えられます。

- **規制対応（PPSI / MiCA）**: x402 の決済フローに attribute proof Header を載せることで、KYC・OFAC スクリーニング結果を原データ共有なしに連携相手へ証明
- **A2A エージェント決済**: AgentCore Payments の支出ガバナンス上で、取引相手のエージェント権限・属性も HTTP 層で検証
- **オンボーディング**: ベンダー・パートナー・顧客の属性を、各組織が独自に保持しながら、必要な属性のみを共有
- **規制報告・監査対応**: x402 + AgentCore Payments の取引証跡に attribute proof を組み合わせ、改ざん不能な形で長期保管し、規制当局・第三者監査に対する説明責任を支える

PPSI 対応という直近の必要性を契機に、x402 + AgentCore Payments + Lemma の組み合わせで複数のユースケースを段階的に展開できる構成として、ご活用いただけます。

---

## 4. EU MiCA との同時対応も視野に

米国の PPSI NPRM と並行して、EU MiCA も 2026-07-01 に grandfathering 期間が終了します。以降、非ライセンス CASP は EU 域内でのサービス提供が違法となります。MiCA も stablecoin issuer に対して、1:1 reserve・AML/KYC・定期 audit・取引報告を要求しており、issuer が顧客理解を行う必要性は米欧で重なっています。

Lemma の ZK 属性証明レイヤーは、米国 PPSI と EU MiCA の両方で要求される顧客理解の枠組みを、単一の attribute proof レイヤーで対応できる設計になっています。米国市場と欧州市場の両方を視野に入れる事業者にとって、規制対応コストの圧縮につながります。

---

## 5. まとめ

- 2026年4月の PPSI NPRM は、stablecoin 発行者に Bank Secrecy Act 上の AML/CFT 義務を課す内容です。同月の AWS Bedrock AgentCore Payments preview を含む x402 決済スタックは、Layer 1・Layer 2 が揃った一方、Layer 3（KYC属性の証明）は scope 外として上位に委譲されています。
- 委譲先となるステーブルコイン発行者・MSB・採用企業にとっては、Layer 3 を自前で実装するか、第三者の attribute provider を組み込むかという選択を迫られる立場になります。具体的な義務範囲については §2 で参照した法律事務所の解釈をご確認ください。
- Lemma の ZK 属性証明レイヤーは、ウォレット非依存・HTTP 層完結・選択的開示で Layer 3 を埋める設計です。PPSI 対応に加え、A2A エージェント決済・オンボーディング・規制監査証跡など、複数のユースケースを単一基盤で実装できます。
- 米国 PPSI と EU MiCA の同時対応も、単一の attribute proof レイヤーで支えられる設計となっています。

---

PPSI 施行は、ステーブルコインを扱う事業者にとって、顧客理解の仕組みを再設計する契機となります。Lemma の ZK 属性証明レイヤーをご導入いただくことで、

- **PPSI が要求する CDD・OFAC スクリーニング** を、顧客データを共有することなく証明
- **A2A エージェント決済** における取引相手の権限・属性を、HTTP 層で検証
- **規制対応の監査証跡** を、改ざん不能な形で長期保管

といった複数のユースケースを、ひとつの基盤の上に構築できます。

**本記事の対象読者**:

- **ステーブルコイン発行者** — JPYC・Progmat・USDC・USDT 等
- **fintech・AI エージェント事業者** — x402 や AgentCore 経由で決済を扱う組織
- **採用企業のコンプライアンス担当者** — MSB ライセンスを持つ事業者
- **規制対応・リスク管理責任者** — PPSI / MiCA 対応の実装を検討中の方

PPSI 対応をきっかけに、それ以外の用途も含めて Lemma の導入をご検討中のステーブルコイン発行者・採用事業者様からのご相談をお待ちしております。原則 1 営業日以内にご返信いたします。

[**Talk to us →**](https://tally.so/r/EkBqDX)

*Built for decisions that matter.*
