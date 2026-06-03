---
title: "Architecture"
---


We don't replace your store-management/compliance ledger. We insert one attribute-proof step into the verification path.

- **Issuer-signed credentials**: government, insurers, hygiene managers issue attributes with signatures.
- **Selective disclosure**: BBS+ over BLS12-381 — minimal disclosure of "validly holds."
- **Revocation & validity**: commit with Poseidon over BN254; prove validity/non-revocation via Groth16 (Circom); link via docHash.

Originals aren't handed over; only "it's valid" reaches HQ.
