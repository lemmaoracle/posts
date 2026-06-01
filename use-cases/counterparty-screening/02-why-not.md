---
title: "Why existing tools fall short"
---


Counterparty credit and sanctions screening is a job where three things are required at the same time: prove the decision without revealing its contents; let a third party, auditor, or AI verify it independently; and make it impossible to alter after the fact. Every conventional tool is missing one of them.

| Tool | Prove without revealing | Independently verifiable | Tamper-proof |
| :-- | :-: | :-: | :-: |
| Spreadsheet / database | ✗ | ✗ | ✗ |
| Signed PDF | ✗ | △ | ✓ |
| Encryption | ✗ | ✗ | ✗ |
| **ZK proof (Lemma)** | **✓** | **✓** | **✓** |

A spreadsheet or database can be rewritten by an administrator, and sharing it leaks the contents. A signed PDF identifies the signer but exposes the reasons and scores. Encryption hides the data but can't show the other side that the decision is correct.

Only a ZK proof satisfies all three. Conversely, if even one of the three is dispensable for a given task, conventional tools are enough and Lemma isn't needed. **Only work that requires all three at once is Lemma's territory.**

**The result travels. The contents don't.**
