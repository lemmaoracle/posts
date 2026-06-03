---
title: "Architecture"
---


We don't replace your HR/labor/training systems. We insert one attribute-proof step into the assignment/fitness decision.

- **Issuer-signed credentials**: medical, training, certification bodies issue attributes with signatures.
- **Selective disclosure**: BBS+ over BLS12-381 — minimal disclosure of "meets fitness."
- **Validity & revocation**: commit with Poseidon over BN254; prove validity/non-revocation via Groth16 (Circom); link via docHash.

Health data stays with the individual/issuer; only "fit" reaches assignment.
