---
title: "How Lemma approaches it"
---


Lemma wraps the attributes a seller agent presents — identity, authority, inventory, price, SLA, issuer signature — in ZK proofs. The buyer agent verifies them cryptographically before committing, with no need to trust plaintext claims. Transactions without verifiable proofs stop at the boundary before settlement runs.

x402 carries the settlement rail; Lemma carries the pre-settlement trust rail. The two run in parallel: each agent transaction completes in three stages — pre-settlement verification → x402 settlement → post-settlement cryptographic evidence. Even if an ephemeral seller agent lies, the buyer-side verification rejects it structurally — no reliance on reputation systems.

Where the pre-settlement verification layer fits into your x402 / agent-commerce stack is what we map out in a first conversation.
