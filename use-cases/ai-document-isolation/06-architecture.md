---
title: "Architecture concept"
---


We don't replace your document platform or AI/RAG pipeline. We insert one fact-extraction-and-proof step ahead of inference.

- **Selective disclosure**: BBS+ over BLS12-381 — present needed attributes/facts to the AI, not the original.
- **Decision-time attestation**: commit referenced facts' docHash and attribution with Poseidon over BN254; prove via Groth16 (Circom).
- **Provenance**: fix the source document version with docHash and issuer signature.

The original stays encrypted in-house; only the reference's cryptographic facts travel.
