---
title: "What Lemma cryptographically guarantees"
---


- KYC completed against an issuer the protocol accepts — without exposing the LP's identity record
- Region attribute checked against the pool's jurisdiction policy
- Risk-tier attribute checked against the tranche's risk policy
- The protocol never holds, indexes, or retains the underlying identity data
- Revocation propagation: an issuer-side update invalidates the LP's next attestation without the protocol scanning its set
- Regulator-grade auditability: the protocol enforced its stated policy on every deposit, verifiable independently
