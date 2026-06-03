---
title: "Architecture"
---


We don't replace your incident/complaint management. We insert one provenance-anchor step where a response is finalized.

- **Provenance anchor**: commit the response's docHash with Poseidon over BN254; fix the moment tamper-free.
- **Selective disclosure**: BBS+ over BLS12-381 — disclose the response facts/procedure, not customer data.
- **Legitimacy proof**: prove "handled by a legitimate procedure/authority" via Groth16 (Circom).

The record's contents stay in-house; only "handled legitimately" travels.
