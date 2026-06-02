---
title: "Architecture concept"
---


We don't replace your application/review system. We insert one attribute-proof step into the eligibility-check path.

- **Selective disclosure**: BBS+ over BLS12-381 — present only attributes needed for the eligibility decision.
- **Requirement proof**: prove "income tier ≤ threshold" etc. via Groth16 (Circom) range/predicate proofs; commit with Poseidon over BN254.
- **Provenance**: fix application/decision with docHash and issuer signature, leaving a fairness trail.

Raw data stays with the applicant/issuer; only the cryptographic fact "eligible" reaches the granting body.
