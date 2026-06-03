---
title: "Why existing tools fall short"
---


Three at once: prove the response without exposing customer data/details; let third parties (court, regulator, audit) verify independently; make it tamper-proof.

| Tool | Prove without revealing | Independently verifiable | Tamper-proof |
| :-- | :-: | :-: | :-: |
| Response log / ledger | ✗ | ✗ | ✗ |
| Signed PDF | ✗ | △ | ✓ |
| In-system logs | △ | ✗ | ✗ |
| **ZK proof + provenance (Lemma)** | **✓** | **✓** | **✓** |

Ledgers/logs can be rewritten and tend to disclose customer data. Only ZK proof + provenance does all three. **Only work requiring all three at once is Lemma's territory.**
