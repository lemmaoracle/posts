---
title: "Architecture concept"
---


We don't replace your worker/qualification system. We insert one attribute-proof step into the assignment path.

- **Issuer-signed credentials**: certifiers/training bodies issue qualifications with issuer signatures.
- **Selective disclosure**: BBS+ over BLS12-381 — minimal disclosure of "holds the required qualification."
- **Validity & revocation**: commit with Poseidon over BN254; prove validity/non-revocation via Groth16 (Circom); link to the original via docHash.

The worker's record is not handed over; only the cryptographic fact "qualified" travels.
