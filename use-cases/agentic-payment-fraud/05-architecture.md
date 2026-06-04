---
title: "Architecture concept"
---


Trust402 sits between the agent and the payment rail as a one-stage ZK verification layer. It does not replace x402, the agent runtime, or the settlement contract — it inserts a precondition.

<!-- TODO: replace with a single horizontal diagram suitable for inclusion in stakeholder briefs. -->
<!-- Placeholder:

  [Agent] → [Trust402 middleware] → [x402 settlement rail]
                ↓
        [Delegation attestation: principal / role / spend cap / jurisdiction]
                ↓
        [ZK proof generated, anchored to principal's on-chain policy]
                ↓
        [Counterparty / contract verifies proof before clearing]
-->

The principal's delegation policy lives on-chain. Revoking a delegation propagates without touching every downstream agent — a single transaction at the principal level invalidates the attestations that depended on it. Authority changes propagate the same way: extend the spend cap, narrow the jurisdiction, swap out the role.

Integration patterns with x402 / MCP / A2A, the reference implementation on Base Sepolia, and the per-call overhead profile live in the whitepaper and the post-Discovery technical materials.
