---
title: "Why existing tools fall short"
---


Three things at once: prove "legitimacy" without exposing the approval contents; let auditors verify independently; make it tamper-proof.

| Tool | Prove without revealing | Independently verifiable | Tamper-proof |
| :-- | :-: | :-: | :-: |
| Workflow / ledger DB | ✗ | ✗ | ✗ |
| Signed PDF | ✗ | △ | ✓ |
| Audit log (in-system) | △ | ✗ | ✗ |
| **ZK proof + provenance (Lemma)** | **✓** | **✓** | **✓** |

Workflows and audit logs can be rewritten by an admin and can't independently show "was this legitimate authority." Only ZK proof + provenance does all three. **Only work requiring all three at once is Lemma's territory.**
