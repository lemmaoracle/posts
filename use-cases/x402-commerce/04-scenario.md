---
title: "A real-world example: an ephemeral seller agent misrepresenting inventory"
---


A buyer agent on an agent-commerce marketplace is looking for a specific semiconductor in stock. Over x402, offers come in instantly from several seller agents. One offer claims 1,000 units available at the lowest price with a 48-hour delivery SLA. The buyer commits. x402 settles in milliseconds.

48 hours later, nothing has been delivered. On inquiry, that seller agent has already been retired and there is no accountable principal. The marketplace's reputation system applies a negative score — but the buyer is already out the money. In a world of ephemeral, single-purpose agents, this pattern is structurally unavoidable.

With Lemma in place, the inventory, price, SLA, and identity attributes the seller presents are each wrapped in a ZK proof; the buyer agent verifies them before committing. If the inventory issuer isn't trustworthy, or if the SLA commitment lacks the corresponding authority proof, x402 settlement never runs — execution stops at the boundary.

Use-case-specific design, integration patterns with x402 / Stripe Agent SDK / MCP, and issuance schemas for agent identity proofs are shared in the sector-specific kit we send after the consultation call.
