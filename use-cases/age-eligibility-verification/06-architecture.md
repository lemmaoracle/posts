---
title: "Architecture"
---


We don't replace your POS / e-commerce / identity stack. We insert one proof step into the sale decision.

- **Selective disclosure**: BBS+ over BLS12-381 — present "age condition met," not the birth date.
- **Range proof**: prove "age ≥ threshold" via Groth16 (Circom); commit with Poseidon over BN254.
- **Provenance**: fix attribute authenticity with issuer signature and docHash.

The birth date/document stays with the individual/issuer; only "condition met" reaches the seller.
