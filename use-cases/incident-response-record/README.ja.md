---
title: "クレーム・事故対応記録の非改ざん証明"
abstract: "食中毒・けが等のクレーム対応で「いつ・どう対応したか」を、顧客情報や詳細を出さず後から証明する。記録は改ざんできず、第三者が独立検証できる。"
thesis: "対応は証明する。顧客情報は出さない。"
pillar: verifiable-origin
industries:
  - svc
cardSummary: "事故・クレーム対応を「いつ・どう対応したか」、顧客情報や詳細を出さず後から証明できます。"
targetVerticals:
  - 宿泊・飲食
  - 小売・サービス
  - 商業施設
tags:
  - incident-response
  - provenance-anchor
  - tamper-proof
  - selective-disclosure
  - zk-proof
relatedUseCases:
  - long-term-contract-record
  - internal-control-approval-proof
  - customer-flag-need-to-know
---

# ユースケース: クレーム・事故対応記録の非改ざん証明

## テーゼ

**対応は証明する。顧客情報は出さない。**

食中毒・けが・クレームなどの対応記録は、残っていても改ざんされていない保証は別の話です。Lemma は対応が発生した時点を暗号的にアンカーし、「いつ・どの手順で・誰が対応したか」を改ざん不能に固定します。記録の中身（顧客情報・詳細）は出さず、必要な範囲だけ根拠を開きながら「正当に対応した」ことを示せます。
