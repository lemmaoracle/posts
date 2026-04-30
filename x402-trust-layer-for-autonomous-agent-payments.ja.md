---
slug: "x402-trust-layer-for-autonomous-agent-payments"
date: "2026.04.28"
category: "Announcement"
section: "Essays"
title: "x402 に信頼レイヤーを足す"
cover: "assets/Q50vhoUkafc.jpg"
abstract: "AIエージェントは x402 によって HTTP 経由で自律的に支払えるようになりました。しかし、ウォレットアドレスとトランザクションハッシュだけでは、受け手のサーバーは「誰が・どの権限で支払ったのか」「返したデータが改ざんなく届いたか」を知ることができません。本日、Lemma × x402 のリファレンス実装を Base Sepolia 上で公開します。すべての決済は PAYMENT-RESPONSE に ZK 証明バンドル — 発行者の身元・支払い完了・データ整合性 — を同梱し、独立に検証可能になります。"
tags:
  - agent-security
  - provenance
  - zero-knowledge-proof
  - payments
relatedLinks:
  - label: "example-x402（リファレンス実装）"
    href: "https://github.com/lemmaoracle/example-x402"
---

## 予告した設計が動きます

前回の[ホワイトペーパー公開記事](https://lemma.frame00.com/blog/whitepaper-v1-prove-ai-decisions)で、ADVANCED シナリオの一つとして「エージェントが証明を持ちながら自律的に決済するシナリオ」を予告しました。本日、その設計が動作するリファレンス実装としてリリースされました。

リポジトリ: [github.com/lemmaoracle/example-x402](https://github.com/lemmaoracle/example-x402)  
サービスページ: [Trust402 — ZK-Verified Agent Payments](https://lemma.frame00.com/trust402)

Base Sepolia 上で稼働中です。この信頼レイヤーは **Trust402** というサービス名で提供しています。

![Demo: Agent fetches content → discovers attestation → pays $0.001 via x402 → receives ZK-verified attributes → selectively discloses specific fields](https://github.com/lemmaoracle/example-x402/raw/main/assets/terminal.gif)

---

## x402 が残した空白

[x402](https://www.coinbase.com/blog/coinbase-and-cloudflare-will-launch-x402-foundation) は HTTP の `402 Payment Required` を本来の用途に蘇らせ、AI エージェントが HTTP を「ネイティブに話せる決済レール」として使えるようにしました。アカウント登録もAPIキーも請求フォームも不要、ウォレットと HTTP リクエストだけで決済が完結します。Coinbase・Cloudflare を中心に、Google Cloud・AWS・Visa・Mastercard が x402 Foundation の founding member として参加しており、エージェントが経済主体になる前提のインフラが整いつつあります。

ただし、x402 単体が保証するのは支払いの完了までです。受け手のサーバーは次の3点を知ることができません。

- **誰が支払いを承認したのか。** 払い手はウォレットアドレスでしかなく、ウォレットアドレスは匿名のプリミティブであって「主体（principal）」ではありません。
- **どの権限で支払ったのか。** ロール・スコープ・spendLimit といったポリシーは、リソース側からは一切見えません。
- **返したデータが改ざんなく届いたか。** バイト列が出ていったあと、受け取ったエージェントが「自分がパースしているものは確かに支払いの対象だったか」を暗号学的に確認する手段がありません。

エージェント間決済が「経済」として成立するためには、これら3つの問いが、決済と同じ HTTP 往復のなかで答えられている必要があります。

---

## 追加するもの

このリファレンス実装では、すべての x402 決済が `PAYMENT-RESPONSE` ヘッダの中に ZK 証明バンドルを同梱します。バンドルは独立に検証可能で、後続のエージェント・スマートコントラクト・監査者など、下流の検証者はそれぞれ自分の手元で検証できます。

バンドルが証明する内容は次の3つです。

### 発行者の身元

検証データを発行した主体（issuer）と、その対象（subject）は、Poseidon Merkle commitment としてオンチェーンに記録されています。検証者は `issuerId` / `subjectId` を on-chain commitment に対して照合することで、「このデータの発行元は確かにこの組織である」ことを暗号学的に確認できます。発行者を後から差し替えたり、過去の発行を装ったりすることは構造上できません。

### 支払いの完了

x402 標準では、決済の証拠は基本的に `transaction` フィールドに入る tx hash です。Lemma の拡張は、tx hash に加えて settlement proof bundle 全体を `PAYMENT-RESPONSE` の `extensions.lemma` ブロックに同梱します。これにより、下流の検証者（後続のエージェントや、入金を検証するスマートコントラクト）は、x402 の往復をもう一度走らせることなく、レスポンスを保持しているだけで支払いの正当性を検証できます。

### データの整合性

レスポンス body の `SHA-256` は、データ発行時にオンチェーンの commitment（`docHash`）と紐付けられています。受け取ったエージェントは、自分が手にしたバイト列を `SHA-256` でハッシュし、レスポンスに同梱された `attributes.integrity` と突き合わせるだけで、「このバイト列は確かに発行時のものと一致する」ことを確認できます。Lemma に再問い合わせする必要はありません。

### 選択的開示

4つ目の証明として、後続の `POST /query` 経由で **選択的開示（selective disclosure）** が利用できます。これは「発行者が署名した属性集合のうち、検証者が必要とする属性だけを開示し、それ以外は伏せたまま検証可能にする」仕組みで、BBS+ 署名スキームを用いて実装しています。

たとえば、ある研究データに `author` / `institution` / `published` / `funding-source` / `coauthors` / `salary` のような属性が紐付いていた場合、検証者は `author` と `published` の2つだけを開示要求でき、残りは「発行者の署名の一部であった」という事実だけを暗号学的に保ったまま、見せないでおくことができます。プライバシー要件と検証要件を同時に満たすための、現実的な設計です。

Lemma ではさらに、この開示を `condition.circuitId = "x402-payment-v1"` という条件で x402 の settlement に束縛しています。「この開示は、対応する x402 の支払いを実行できた主体だけが導出できる」ことを意味し、開示の無断コピー・無断再利用は構造上できません。支払いと開示が、同じ暗号学的事実の表裏として一体になります。

---

## 1リクエストの4フェーズ

リファレンスエージェント（`packages/agent/src/index.ts`）は次の4つのフェーズを順に実行します。

1. **無料で取得**
   - `GET /resource` はデータと、検証エンドポイントを指す `X-Lemma-Attestation` ヘッダ（または HTML 内の `<link rel="lemma-attestation">`）を返します。
   - データの配布は安く、`$0.001` の対価が支払われるのは「証明」に対してです。
2. **`UNVERIFIED` として保持**
   - エージェントの判断ロジックには「未検証」のラベル付きでデータが渡ります。
   - この時点では業務行動は起こしません。
3. **支払いと検証を1往復で**
   - `GET /verify/:hash` を `@x402/fetch` で叩くと `402 Payment Required` が返ります。
   - エージェントは Base Sepolia で `0.001 USDC` を自動的に支払います。
   - `200` レスポンスのボディには検証済み属性が、`PAYMENT-RESPONSE` ヘッダには ZK settlement proof が同時に乗ります。
4. **整合性確認**
   - エージェントは `SHA-256(body)` を計算し、`attributes.integrity` と照合します。
   - 一致したときに初めて内部状態を `VERIFIED` に遷移させます。

Phase 3 が、決済と証明が単一のアーティファクトに統合される瞬間です。`@lemmaoracle/x402` が生成する `PAYMENT-RESPONSE` の拡張は次の形をしています。

```typescript
type PaymentResponseExtension = {
  transaction: string;
  network: string;
  payer?: string;
  extensions?: {
    lemma?: {
      proof: string;
      inputs: string[];
      circuitId: string;
      generatedAt: number;
    };
  };
};
```

このレスポンスを保持していれば、誰でもオンチェーン commitment に対して証明を検証できます。Lemma facilitator に再度問い合わせる必要はありません。

---

## 組み込み方

信頼レイヤーは、リソースサーバー側に1層のミドルウェアを足すだけで導入できます。

`@lemmaoracle/x402` パッケージは、Coinbase が提供している `@x402/*` パッケージ群と同じ API シグネチャを持ちます。既存の x402 サーバー実装で `@x402/*` から import している箇所を `@lemmaoracle/x402` に書き換えるだけで、ZK 証明の発行が自動的に有効になります。

```typescript
import {
  paymentMiddleware,
  x402ResourceServer,
  ExactEvmScheme,
  HTTPFacilitatorClient,
} from "@lemmaoracle/x402";
import { createFacilitatorConfig } from "@coinbase/x402";

const resourceServer = new x402ResourceServer(
  new HTTPFacilitatorClient(facilitatorConfig)
).register("eip155:84532", new ExactEvmScheme());

app.use("*", paymentMiddleware(routes, resourceServer));
```

**ルートハンドラ側のコードを書き換える必要はありません。** ビジネスロジックは触らず、`x402ResourceServer` のコンストラクタが `lemma` という名前の拡張を `enrichSettlementResponse` 経由で自動登録します。settle 完了後にこの拡張が ZK 証明を生成し、`PAYMENT-RESPONSE` の `extensions.lemma` ブロックに書き込みます。

エージェント側にも変更は不要です。Coinbase 公式の `@x402/fetch` をそのまま使うことで、レスポンスの `PAYMENT-RESPONSE` ヘッダから `extensions.lemma` を読み出して検証できます。クライアントに Lemma 専用 SDK を入れる必要はありません。

つまり、信頼レイヤーの導入コストは **サーバー側の import 文一行** に閉じ込められています。x402 を運用しているチームは、ビジネスロジックを温存したまま、信頼の層だけを後付けで足すことができます。

---

## 今日のスコープ：資源側の信頼レイヤー

本日のリリースが扱うのは、**資源側（リソース提供者・データ発行者の側）の信頼レイヤー**です。すなわち、「誰が発行したデータを、いくら払って、改ざんなく受け取ったか」をエージェントが暗号学的に確認できる範囲です。

もう一方の **エージェント側（払い手の側）の信頼レイヤー** — 「払ったエージェントは誰で、どの権限で、どのポリシー下で支払ったか」 — は次のマイルストーンとして開発を進めています。今日の段階では払い手はウォレットアドレスとして同定されており、`did:key` への紐付けやロール・spendLimit の証明はまだ含まれません。

2つの層を分けて段階的にシップする判断には理由があります。資源側だけでも、エージェントが既存の x402 経済から検証可能なデータを引き出すには十分機能します。エージェント側の信頼レイヤーは独立に組み合わせ可能な設計にしてあり、後から差し込んでも資源側の証明は無効になりません。

---

## エージェント側の次の一手

エージェント側の信頼レイヤーでは、払い手のエージェントを「匿名のウォレット」から「検証可能なプリンシパル」に格上げすることを目指しています。

具体的には、`did:key` によるプリンシパル同定、ロールとスコープによる権限表現、spendLimit による決済上限の明示、そしてクレデンシャルの発行・失効といったライフサイクルを、ZK 述語として表現し検証可能にします。

これが乗ると、`PAYMENT-RESPONSE` には「この支払いは `did:key:...` のエージェントが、`org:acme` の `payments:read` スコープ下で、spendLimit 以内・有効期間内・未失効の状態で実行した」という証明が同梱されます。そのとき初めて、資源側の信頼レイヤーとエージェント側の信頼レイヤーが組み合わさり、ホワイトペーパーで予告した「自律決済の信頼レイヤー」が完成します。

---

## スキーマを差し替えれば、あらゆるデータに

リファレンス実装はブログ記事の属性スキーマ（著者・掲載日・言語など）で動作していますが、これは「最も読みやすい例」として選んだだけで、他のデータ型にもそのまま適用できます。

「取得 → 支払い → ZK 検証」という 4 フェーズフロー自体は、データの中身に一切依存していません。スキーマ（どの属性を検証するかの定義）と回路（どの論理で検証するかの ZK 証明）を差し替えるだけで、完全に同じ仕組みを別のドメインに載せ替えられます。

| ドメイン | 検証対象の属性例 | 想定ユースケース |
| :--- | :--- | :--- |
| 資格・クレデンシャル | 発行機関 / 有効期限 / スコープ | エージェント権限の検証、自動 KYC/AML |
| IoT センサーデータ | デバイス ID / タイムスタンプ / 測定値整合性 | サプライチェーンの改ざん検知 |
| 金融アテステーション | 監査法人 / 報告日 / 数値整合性 | エージェントによる財務データ自動検証 |
| 研究データ・論文 | 著者 / 機関 / 再現性ハッシュ | 研究成果の出自証明、リトラクション検知 |
| オンチェーンイベント | コントラクト / ブロック高 / イベントデータ | クロスチェーンブリッジ、DeFi オラクル |
| 自律調達（ダークファクトリー） | サプライヤー ID / 規格適合 / 価格契約整合性 | M2M 調達決済の自動検証と監査証跡 |

本質は、「払うエージェントが、自分が支払った対象が誰によって発行されたものかを暗号学的に知り、受け取ったバイト列が支払いの対象と一致することを確認できる」点にあります。この能力は、スキーマと回路が交換可能だからこそ、広いドメインに広がります。

---

## x402 と MCP（α）：同じ信頼レイヤーの2つの入口

本日と並行して、補完的な入口として MCP サーバー（[lemma/packages/mcp](https://github.com/lemmaoracle/lemma/tree/main/packages/mcp)）の **開発途上の α 版デモ** も公開しています。これはローカルインストールして試せるデモで、リモートホストされた本番サービスではありません。両者は同じ信頼レイヤーを下に共有し、職責だけが違います。

- **x402** は決済レールです。Lemma が加える価値は `PAYMENT-RESPONSE` への settlement proof + ZK proof バンドルの同梱。
- **MCP（α デモ、ローカルインストール型）** は MCP-native なエージェント（Claude Desktop / Cursor 等）のための読み取りインターフェースです。検証済み属性のクエリ、スキーマ・回路・ジェネレータ・証明状態を読むツールを提供します。実際に動作しますが、ローカルインストールが前提です。

x402 はすでにプロトコルレベルでエージェントから直接呼べるため、決済に MCP ラッパーは不要です。MCP は「すでに MCP を話せるエージェントが、独自 REST 統合なしに Lemma から読む」ための入口として独立に存在します。

---

## 次回予告

次回のブログでは、エージェント側の信頼レイヤー — DID バインディング、ロール・スコープ・spendLimit の検証 — を取り上げ、資源側と組み合わさったときの全体像をお見せする予定です。

デモは認証不要ですぐに試せます。本番導入を検討している方、価格・プランの詳細やホワイトペーパーをご希望の方は、[**開発者ウェイトリスト**](https://tally.so/r/kd0bZR) への登録をご検討ください。ウェイトリスト登録者にはホワイトペーパー PDF も提供します。

---

## リンク

- リポジトリ: [lemmaoracle/example-x402](https://github.com/lemmaoracle/example-x402)
- MCP サーバー（α デモ、ローカルインストール型）: [lemma/packages/mcp](https://github.com/lemmaoracle/lemma/tree/main/packages/mcp)
- サービスページ: [Trust402](https://lemma.frame00.com/trust402)
- ホワイトペーパー公開記事: [AIの判断根拠を証明する設計書](https://lemma.frame00.com/ja/blog/whitepaper-v1-prove-ai-decisions)
- 開発者ウェイトリスト: [tally.so/r/kd0bZR](https://tally.so/r/kd0bZR)

---

*Built for decisions that matter.*
