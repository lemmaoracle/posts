---
title: "Why existing tools fall short"
---


Three things at once: prove "qualified" without exposing the record; let a prime/auditor verify independently; make it tamper-proof.

| Tool | Prove without the record | Independently verifiable | Tamper-proof |
| :-- | :-: | :-: | :-: |
| Roster / spreadsheet | ✗ | ✗ | ✗ |
| Copy of the license | ✗ | △ | ✗ |
| Signed PDF | ✗ | △ | ✓ |
| **ZK proof (Lemma)** | **✓** | **✓** | **✓** |

Rosters and license copies carry the worker's sensitive data along, and forgeries/expiries slip in. Only a ZK proof does all three. **Only work requiring all three at once is Lemma's territory.**
