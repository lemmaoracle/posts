---
title: "Architecture concept"
---


We don't replace your certificate-issuance or student-records system. We insert one proof step into the issuance and presentation paths.

- **Issuer-signed credentials**: universities/certification bodies issue records with issuer signatures (W3C VC 2.0 aligned).
- **Selective disclosure**: BBS+ over BLS12-381 — minimal disclosure of "legitimately held."
- **Authenticity & provenance**: fix with docHash and issuer signature; verify via Groth16 (Circom).

The original stays with the individual/issuer; only the cryptographic fact "legitimately held" travels.
