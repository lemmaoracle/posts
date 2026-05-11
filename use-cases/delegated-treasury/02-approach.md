---
title: "How Lemma approaches it"
---


Lemma issues the spend authority you delegate to an AI agent as an on-chain spend-control attestation — not as a soft prompt. Each attestation carries, signed by the issuing organization, the spend limit, the eligible category scope, the validity window, and a revocation endpoint.

Counterparties — sellers, payment facilitators — verify the attestation independently before accepting payment. No platform trust required. Because it crosses as a ZK proof, only the constraint conditions cross to the verifier; your internal budget structure and approval policy stay inside. At audit, every transaction is paired with cryptographic evidence of the delegation that authorized it.

Where the spend-control attestation slots into your AI agent operations and treasury controls is what we map out in a first conversation.
