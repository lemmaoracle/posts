---
title: "店舗コンプライアンス照合 ── 許認可・衛生・保険"
abstract: "各店舗を、許認可・衛生・保険の有効条件と照合し、「適合」という結果だけを本部が受け取ります。証書の原本は各店・発行者の管理下に残るので、本部が全店の書類を集めて保管する負担と、更新のたびの回収を、構造的に取り除けます。"
thesis: "各店を、許認可・衛生・保険の有効条件と照合する。結果だけを受け取り、証書は渡さない。"
pillar: regulatory-attribute-proof
industries:
  - svc
cardSummary: "各店を許認可・衛生・保険の有効条件と照合し、結果だけを受け取ります。証書は渡りません。"
targetVerticals:
  - チェーン店・フランチャイズ
  - 飲食
  - 小売
  - 宿泊
tags:
  - franchise-compliance
  - selective-disclosure
  - revocation
  - attribute-proof
  - zk-proof
relatedUseCases:
  - supplier-credential-verification
  - counterparty-screening
  - qualified-worker-attestation
---

# ユースケース: 店舗コンプライアンス照合 ── 許認可・衛生・保険

## テーゼ

**各店を、許認可・衛生・保険の有効条件と照合する。結果だけを受け取り、証書は渡さない。**

店舗網の適合確認は「本部が全店の書類を集めれば保管と更新管理を抱える、店舗任せにすれば失効を見落とす」というジレンマを抱えています。Lemma は照合の**結果だけ**を ZK 証明として渡し、営業許可・衛生・保険の証書原本は出しません。受け取った側（本部・監査・行政）は証書を見ずに「店舗が適合しているか」を独立に検証でき、失効も追えるため「提出時は有効だったが今は失効」も検知できます。
