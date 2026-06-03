---
title: "Why existing tools fall short"
---


Three at once: prove "valid" without the original; let HQ/auditors verify independently; make it forgery/tamper-proof.

| Tool | Prove without the original | Independently verifiable | Forgery/tamper-proof |
| :-- | :-: | :-: | :-: |
| Collecting certificate PDFs | ✗ | ✗ | ✗ |
| Signed PDF | ✗ | △ | ✓ |
| Ad-hoc issuer query | △ | △ | ✓ |
| **ZK proof (Lemma)** | **✓** | **✓** | **✓** |

PDFs hide expiry/reuse; queries are laborious. Only a ZK proof does all three. **Only work requiring all three at once is Lemma's territory.**
