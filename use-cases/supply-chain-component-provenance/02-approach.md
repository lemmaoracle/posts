---
title: "How Lemma approaches it"
---


Lemma lets every supplier tier issue component attributes (country of origin, manufacturing lot, modification history, quality test results) as issuer-signed attestations, with per-component cryptographic links to upstream tiers. Supplier names, contract terms, and cost data stay under the issuer's control. What crosses to the receiving side is only a ZK proof: "this part was produced in a certified Tier-3 facility," "this lot passed the specified test threshold."

Autonomous procurement agents can verify the per-component provenance chain as a ZK proof before confirming an order. Tampering attempts, counterfeit lot injection, and gray-market entry are structurally detectable as chain-integrity breaks — execution stops at the boundary without tracing all the way upstream manually.

How this provenance chain fits your procurement structure and DPP / traceability requirements is what we map out in a first conversation.
