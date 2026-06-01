---
title: "Architecture concept"
---


We don't replace your procurement or supplier-management system. We insert one attribute-proof step into the certificate-verification path.

- **Issuer-signed credentials**: certifiers / suppliers issue attributes with issuer signatures.
- **Selective disclosure**: BBS+ over BLS12-381 — minimal disclosure of "holds a valid certification."
- **Revocation & validity**: commit with Poseidon over BN254; prove validity / non-revocation via Groth16 (Circom); link to the original via docHash.

The original is not handed over; only the cryptographic fact of "it's valid" travels.
