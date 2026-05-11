---
title: "Architecture in concept"
---


Lemma does not replace the x402 protocol or your existing agent-commerce implementation. We add a single ZK-proof verification gate between the seller's attribute presentation and the buyer's settlement commit.

<!-- TODO: replace with a Mermaid or SVG diagram. Horizontal layout: Seller → Lemma Attribute Proof → Buyer Verification → x402 Settle. -->
<!-- Temporary placeholder:

  [Seller agent: attribute presentation]
       │ (identity / inventory / price / SLA + issuer signature → ZK proof)
       ▼
  [Lemma verification gate (buyer side)]
       │ (proof check / authority scope check)
       ▼ (only if proofs pass)
  [x402 settlement rail]
       │
       ▼
  [On-chain anchored transaction record]
-->

The seller side issues each attribute as a ZK proof at listing time; the buyer side verifies on receipt. Issuer, validity window, and scope are cryptographically fixed per attribute, so impersonation, inventory misrepresentation, and SLA forgery are structurally caught — never reaching x402 settlement. The post-transaction record persists for both sides as cryptographic evidence in any later dispute.

Integration patterns with x402 / Stripe Agent SDK / MCP, attribute-proof issuance schemas, and embed patterns for marketplace operators are detailed in the whitepaper and the post-call technical kit.
