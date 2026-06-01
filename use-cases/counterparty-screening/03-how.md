---
title: "How Lemma does it"
---

The decision is proven as a predicate — e.g., "not on the sanctions list," "credit score above threshold" — with no disclosure of the underlying data.

- **Selective disclosure**: BBS+ over BLS12-381 — present only the one attribute the other side needs.
- **(Non-)membership proof**: commit the list with Poseidon over BN254; prove "not present / present" via Groth16 (Circom circuit). Query history and the full list stay private.
- **Provenance anchor**: fix when, by whom, and tamper-free the decision was issued, as a docHash (P1 Verifiable Origin) — reconstructable in later audits or disputes.

**Models change. Proofs remain.**
