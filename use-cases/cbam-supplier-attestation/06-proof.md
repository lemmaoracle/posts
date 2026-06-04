---
title: "What Lemma cryptographically guarantees"
---


- Country of production, carbon intensity, and CBAM-required measurement attributes — bound to each supplier's reference data
- Selective disclosure: only the per-attribute proof crosses each tier, never the underlying production data
- Multi-tier composition: an importer's CBAM filing is a single chained ZK proof composed from supplier-side attestations
- Independent verification by the EU authority, third-party auditors, and any downstream party — without supplier data exposure
- Revocation propagation: a supplier-side update flows downstream as a fresh attestation, invalidating any stale chained proof
