---
title: "顧客対応フラグの最小開示"
abstract: "要注意客の取扱いフラグを、理由を現場に出さず「必要な対応区分」だけ証明として渡します。根拠は権限者だけが参照でき、誰がいつ付与したかを改ざんなく残したまま運用できます。"
thesis: "対応は伝わる。理由は伝わらない。"
pillar: regulatory-attribute-proof
industries:
  - svc
  - fin
cardSummary: "要注意フラグの理由を現場に出さず、必要な対応区分だけを証明。根拠は権限者だけが見られ、改ざんなく残せます。"
targetVerticals:
  - ホテル・宿泊
  - 小売・サービス
  - 会員制事業
  - 金融（要注意顧客）
tags:
  - need-to-know
  - selective-disclosure
  - customer-flag
  - attribute-proof
  - zk-proof
relatedUseCases:
  - counterparty-screening
  - kyc-aml-selective-disclosure
  - ai-audit-log-proof
---

# ユースケース: 顧客対応フラグの最小開示

## テーゼ

**対応は伝わる。理由は伝わらない。**

過去にトラブルのあった顧客に「取扱い注意」の内部フラグを付けて運用すると、理由を現場に見せるか／誰も追えない運用にするかの二択になりがちです。Lemma は、現場には「対応区分」だけを証明として渡し、理由・履歴は権限者が選択的開示で参照する構造を提供します。誰が・いつ・どの根拠でフラグを付けたかは改ざんなく来歴として残り、苦情・本人開示請求・監査に対し、根拠を限定的に開きながら説明できます。
