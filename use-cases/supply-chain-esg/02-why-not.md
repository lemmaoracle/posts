---
title: "Why existing tools fall short"
---


Supply chain ESG / regulatory conformity proof is a job where three things are required at the same time: prove attribute conformity (CBAM, EUDR, DPP) without revealing transaction data or origin information; let buyers, auditors, regulators, or AI verify it independently; and make multi-tier supplier provenance tamper-proof. Every conventional tool is missing one of them.

| Tool | Prove without revealing | Independently verifiable | Tamper-proof |
| :-- | :-: | :-: | :-: |
| Spreadsheet / database | ✗ | ✗ | ✗ |
| Signed PDF | ✗ | △ | ✓ |
| Encryption | ✗ | ✗ | ✗ |
| **ZK proof (Lemma)** | **✓** | **✓** | **✓** |

A spreadsheet or database can be rewritten by an administrator, and sharing it leaks origin and contract terms along with everything else. A signed PDF identifies the signer but exposes transaction data, supplier composition, and contract terms. Encryption hides the data but can't show the other side that "CBAM / EUDR / DPP conformity is met."

Only a ZK proof satisfies all three. Conversely, if even one of the three is dispensable for a given task, conventional tools are enough and Lemma isn't needed. **Only work that requires all three at once is Lemma's territory.**

**Declared ≠ proven.**
