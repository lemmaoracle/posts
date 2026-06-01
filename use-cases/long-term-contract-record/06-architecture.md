---
title: "Architecture concept"
---


We don't replace your ledger or contract system. We insert one provenance-anchor step where a record is finalized.

- **Provenance anchor**: fix the content as a docHash, commit with Poseidon over BN254; record the moment tamper-free.
- **Selective disclosure**: BBS+ over BLS12-381 — present only the needed clauses/attributes.
- **Legitimacy proof**: prove "legitimately existed / amended" via Groth16 (Circom).

The original stays in-house; only the record's cryptographic identity travels.
