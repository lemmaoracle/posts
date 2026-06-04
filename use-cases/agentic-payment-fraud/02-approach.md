---
title: "How Lemma approaches it"
---


Lemma attaches a Trust402 attestation to every payment an agent issues. Inside the attestation: the principal that delegated the action, the role and scope of the delegation, a per-call spend limit, and any jurisdiction attribute the counterparty needs to verify (e.g. "this agent acts on behalf of a JP-registered entity").

The attestation is a ZK proof, not a bearer credential. The agent never carries the principal's keys. The receiving side — be it a settlement contract, an x402 middleware, or a counterparty's risk engine — verifies the proof before clearing the payment, against an on-chain registry of the principal's delegation policy. Revocation propagates the same way: a single transaction at the principal's level invalidates every downstream attestation that depended on it.

The result is that "who delegated, within what scope, against which jurisdiction" stops being an after-the-fact reconstruction problem and becomes a precondition for settlement.
