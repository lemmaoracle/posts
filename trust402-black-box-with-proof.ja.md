---
slug: "trust402-black-box-with-proof"
date: "2026.07.24"
category: "Tech Insight"
section: "Essays"
title: "黒い箱に内容証明書を付ける — Trust402"
cover: "assets/Urrtqa2esfj.jpg"
abstract: "x402 は AI エージェントによる自律的な支払いを実現した。しかし、買おうとしているものが本物かどうかをエージェントに伝える手段がなかった。Trust402 は ZK 証明を使い、コンテンツを公開することなく、購入前の検証を可能にする。"
tags:
  - trust402
  - x402
  - zk-proof
  - agent-payments
  - marketplace
relatedLinks:
  - label: "Trust402 Sell ダッシュボード"
    href: "https://lemma.frame00.com/trust402/sell"
  - label: "Trust402（サービスページ）"
    href: "https://lemma.frame00.com/trust402"
---

## TL;DR

x402 は AI エージェントによる自律的な支払いを実現するプロトコル。しかし、買おうとしているものが本物かどうかをエージェントに伝える手段がなかった。Trust402 は ZK 証明（ゼロ知識証明）を使い、コンテンツを公開することなく、AI による購入前の検証を可能にする。

---

## 黒い箱です。中身は素晴らしいデータです。1ドルでいかがですか？

こんなの誰も買わない。しかし、これが 1 日に 10 億回も起きている。

![黒い箱です。1 ドルでいかがですか？](assets/Urrtqa2esfj.jpg)

AI エージェントがその可用性を拡張する中で、エージェント間のコミュニケーション、ツールの統合、支払い、認証など、あらゆるレイヤーで標準化が進行している。**x402** は支払いを担うプロトコルで、2025 年 5 月に Coinbase が発表した。アイデアはシンプルで、HTTP の 402 ステータスコード（「Payment Required」、名前は常にあったもののほとんど使われてこなかった）を採用し、支払い条件の提示から購入者の支払い可否の確認、決済、レスポンスの返却まで、フロー全体を標準化している。支払いプロセス全体がプレーンな API 呼び出しで機械的に実行できるようになった。

現在では Linux Foundation の下で x402 Foundation が発足し、そこに Coinbase、Cloudflare、Stripe なども参加している。Chainalysis のレポートでは、x402 は **最初の 9 ヶ月で Base 上で 1 億トランザクションを突破**。1 ドルを超えるトランザクションが流通総額の 95% を占めていて、1 年前の 49% からの大幅に増えている。

**しかし、1 億という数字は実際の支払い意図のほんの一部でしかない。**

「**Cloudflare のネットワーク上で毎日最大 10 億件の 402 レスポンスが提供されている**」[^1]という推定がある。402 レスポンスとはサーバーが「これは有料コンテンツなので支払いを」と伝えるシグナルなので、サーバーは **1 日に 10 億回** 支払を要求して、決済に至るのはそのほんの一部ということになる。

エージェントは価格を確認し、ただ...立ち去っている。

「黒い箱」からは何も価値が分からないから、これは仕方がないとも言える。

エージェントは支払うことができるようになったが、買おうとしているものが本物かどうかを判断する手段がないというのが現状だ。「データセット」というラベルの付いた JSON は本当か？ドキュメントは改ざんされていないか、コードにバックドアはないか？人間なら買う前に当然確認するようなことを、プロトコルは何も保証していない。仕様はクリーンだが、実際には何かが欠けている。

ここには構造的なギャップがある。

## 支払う方法はあっても、なぜ支払うべきかを確認する方法がない。

このギャップを埋めるために Trust402 を作った。

## Trust402：「黒い箱ですが、内容証明書があります。こちらです」

Trust402 は、x402 の上に構築された **検証可能なコンテンツマーケットプレイス**。

クリエイターや研究者は、自分の作品（ドキュメント、データセット、コード、モデル）に **ZK 証明（ゼロ知識証明）** を添付して出品できるようになる。購入者（人間でもエージェントでも可）は証明を検証し、x402 の支払いを実行して、コンテンツを取得する。

ZK 証明とは、例えば、

> このデータは少なくとも 1,000 種類の値を保持しており、改ざんされていない。

といった主張を、ファイル自体を見せることなく証明できる暗号技術。購入者エージェントは支払う前に証明を検証し、それが本物であり価値があるかどうかを判断できる。支払いは x402 に委ね、真正性は ZK 証明が担っている。

ZK 証明の生成、ドキュメントの登録、ストアフロントへの公開など、すべてを SDK とダッシュボードから実行できる。ETHGlobal で最初のバージョンを構築して、現在は Cloudflare Workers + Base 上で本番稼働している。先日ダッシュボードを [lemma.frame00.com/trust402/sell](https://lemma.frame00.com/trust402/sell) で公開した。

## 試してみる

ダッシュボードが最も手軽な入り口と思う。

1. [lemma.frame00.com/trust402/sell](https://lemma.frame00.com/trust402/sell) を開き、
2. 「dashboard」をクリックしてウォレットを接続。
3. ファイルをリストフォームにドラッグし、カテゴリと価格（USDC）を入力して
4. 「Publish」を押すだけ。

裏側では `@trust402/sdk` の `publish()` が、証明の生成からストアフロントへのアップロードまですべてを実行している。

![trust402 dashboard](assets/dEngBult6d3.webp)

ダッシュボードでできることはすべて SDK からも使える。ダッシュボードはあらゆるファイルタイプで機能するハッシュベースの証明（改ざんなしの証明）を使用するが、SDK を使えば特定のフォーマットの実際のコンテンツに踏み込んだ証明の構築も可能になっている。例として、「著者、文字数、言語」を証明に組み込める `blogArticle (blog-article-v1.2)` という構築済みの ZK 回路を使った場合、ブログ記事を出品するための最小限のコードは次のとおり。

```typescript
import { create, publish, blogArticle } from "@trust402/sdk";

// 1. クライアントを作成
const client = create({
  apiKey: "your-lemma-api-key",
  getSigner: async () => ({
    provider: window.ethereum,
    address: account.address,
  }),
  onPayment: (info) => console.log(`paid ${info.amount} micro-USDC`),
});

// 2. 記事からwitnessとcommitmentを構築
const { witness, commitment } = blogArticle({
  author: "aggre",
  published: Date.now(),
  body: "# My Article\n\nHello, Trust402.",
  words: 7,
  lang: "en",
});

// 3. 証明を生成し、ドキュメントを登録し、出品を公開
const listing = await publish(client, {
  circuitId: "blog-article-v1.2",
  witness,
  commitment,
  price: { amount: 5000, currency: "USDC" },
  did: `did:pkh:eip155:84532:${account.address}`,
  environment: "sandbox",
  file: new File(["# My Article\n\nHello, Trust402."], "article.md", {
    type: "text/markdown",
  }),
  category: "document",
  payoutAddress: account.address,
});
```

裏側で `publish()` が行っているのは:

1. **ZK 証明の生成**: `circuitId` (Groth16、ZK-SNARKの一種) の回路を取得し、witness (証明の構築元となる入力値) から証明を構築。
2. **ドキュメントの登録**: Lemma API にドキュメントを登録し、証明を送信 (ここで x402 の支払いが自動実行 ... Trust402 自体の利用料)。
3. **listingRootの計算**: 出品を一意に識別する ID を決定論的に生成。
4. **ストアフロントへの公開**: ファイルを渡した場合、ストアフロントに直接アップロード。

3 つの呼び出し (`create` → `blogArticle` → `publish`) だけで、ZK 証明からストアフロントへの出品まで接続している。

## カスタム回路

ここまでは `blog-article-v1.2` のような、ダッシュボードや SDK に同梱されている回路を使用した。しかし、Trust402 では **登録したあらゆる回路を使用できる**ようにしている。これによって例えば、こういうことができるようになる:

- データセットを出品 → 「このデータセットは 1,000 以上のレコードを保持している」
- コードを出品 → 「このコードは React を使って 30 以上のコンポーネントを持っている」
- モデルを出品 → 「このモデルのパラメータ数は 7B 以上である」

これらを検証可能にし、Trust402 の出品フローに組み込んでみる方法を書こうと思うが少し長くなってきたので、これについては別の記事でしようと思う。`research_dataset` のようなカスタム回路をコードで解説し、エージェントが購入前にどのように確認できるかを示す予定。

Trust402 は [lemma.frame00.com/trust402/sell](https://lemma.frame00.com/trust402/sell) でプレビュー版を公開中。x402 が支払いレイヤーを標準化したように、Trust402 は検証可能なコンテンツ取引の標準パターンを目指している。

---

[^1]: CoinDesk, "AI Agents Are Breaking Web Economics, but Cloudflare Says x402 Can Help" (May 5, 2026). Cloudflare CSO Stephanie Cohen, Consensus Miami 2026.
