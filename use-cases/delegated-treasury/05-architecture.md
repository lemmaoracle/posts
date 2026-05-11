---
title: "Architecture in concept"
---


Lemma does not replace your agent platform, ERP, or procurement system. We add one issuance step at delegation time and one verification gate on the seller's side at settlement.

<!-- TODO: replace with a Mermaid or SVG diagram. Horizontal layout: issuing organization → attestation → agent → seller verification. -->
<!-- Temporary placeholder:

  [Issuing organization: CFO / finance]
       │ (spend limit, scope, validity window + signature)
       ▼
  [Spend-control attestation (anchored on-chain)]
       │
       ▼
  [AI agent: presents transaction]
       │ (ZK proof — only constraint conditions revealed)
       ▼
  [Seller / payment facilitator: independent verification]
       │
       ▼
  [Settle, or stop at the boundary]
-->

The attestation carries the issuer's signature and a revocation endpoint, and is verifiable on-chain throughout its validity window. Payments to out-of-scope categories, over-limit transactions, and post-revocation activity are refused by construction. Issuance, delegation, transaction, and audit all stay tied together cryptographically.

Integration patterns with existing ERPs and procurement systems (NetSuite, Coupa, SAP Ariba, etc.), plug-in patterns for agent platforms (Anthropic Computer Use, Stripe Agent SDK, etc.), and SOX / J-SOX evidence-trail design are detailed in the whitepaper and the post-call technical kit.
