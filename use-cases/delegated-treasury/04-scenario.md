---
title: "A real-world example: SaaS renewal agent exceeds authority"
---


Imagine an AI agent handling SaaS procurement under the constraints: up to $10,000/month, subscription renewals only, anything over $500 requires approval. One month, a new vendor pitches "the same capabilities, 30% cheaper" and the agent processes it inside the conversation as "extension of an existing renewal." The soft-prompt guardrail is breached. Payment goes through.

Internal policy says the spend was out of scope — but the seller has no way to know. After the fact, finance tries to reconstruct "who authorized what under which delegation," cobbling evidence from platform logs and prompt history. Producing a verifiable trail for J-SOX or SOX audit takes weeks.

With Lemma in place, the seller verifies the agent's spend-control attestation before settlement. New vendor is outside the subscription-renewal category. Anything over $500 requires approval. Both conditions are readable as ZK proofs — the payment stops at the boundary. At post-incident audit, each transaction is paired with cryptographic evidence of the delegation that authorized it.

Sector-specific control mapping, integration patterns with existing ERPs and procurement systems (NetSuite, Coupa, SAP Ariba, etc.), and SOX / J-SOX evidence-trail design are shared in the sector-specific kit we send after the consultation call.
