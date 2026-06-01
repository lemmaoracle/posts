---
title: "Why existing tools fall short"
---


Three things are required at once: prove without revealing the contents; let a third party (court, auditor) verify independently; make it tamper-proof.

| Tool | Prove without revealing | Independently verifiable | Tamper-proof |
| :-- | :-: | :-: | :-: |
| Spreadsheet / ledger DB | ✗ | ✗ | ✗ |
| Signed PDF | ✗ | △ | ✓ |
| Blockchain alone | ✗ (fully public) | ✓ | ✓ |
| **ZK proof + provenance (Lemma)** | **✓** | **✓** | **✓** |

A ledger DB can be rewritten by an admin; a bare blockchain exposes everything. Only ZK proof + provenance does all three. If one is dispensable, existing tools suffice. **Only work requiring all three at once is Lemma's territory.**
