---
title: "Architecture"
---


We don't replace your customer management (PMS/CRM). We insert one proof-and-provenance step into the flag's set/read path.

- **Selective disclosure**: BBS+ over BLS12-381 — split disclosure: handling category to the front line, basis to authorized staff.
- **Legitimacy proof**: prove "set by a legitimate authority" via Groth16 (Circom); commit with Poseidon over BN254.
- **Provenance**: fix the setter, time, and basis docHash tamper-free.

The reason/history stays in-house (with authorized staff); only the "handling category" reaches the front terminal.
