---
title: "Architecture concept"
---


We don't replace your approval workflow or ERP. We insert one authority-proof-and-provenance-anchor step where an approval is finalized.

- **Authority proof**: prove "a legitimately authorized person approved" via Groth16 (Circom); bind segregation of duties (who may approve).
- **Provenance anchor**: fix the approval's moment as a docHash, commit with Poseidon over BN254.
- **Selective disclosure**: BBS+ over BLS12-381 — present only the metadata (legitimacy), not the approval contents.

The approval contents stay in-house; only the cryptographic fact "approved under legitimate authority and process" travels.
