---
title: "How Lemma approaches it"
---


At each delegation step in a multi-agent workflow, Lemma generates a ZK proof that binds delegator, delegatee, scope, and timestamp. Proofs are anchored on-chain; each agent operation carries the delegation proof downstream. Tools and APIs verify the cryptographic authority of the caller, not the agent's self-attestation, before responding.

The final output carries a complete proof chain — from the original principal, through every re-delegation node, down to each tool result. When something goes wrong, you don't reconstruct it from logs; each step's authority and data access is already a cryptographic fact you can reference directly.

Where the delegation-proof layer fits into your current agent orchestration and MCP integration is what we map out in a first conversation.
