---
title: "Why existing tools fall short"
---


Three things at once: prove "valid" without handing over the original; let the assembler/auditor verify independently; make it forgery- and tamper-proof.

| Tool | Prove without the original | Independently verifiable | Forgery/tamper-proof |
| :-- | :-: | :-: | :-: |
| Certificate PDF exchange | ✗ | ✗ | ✗ |
| Signed PDF | ✗ | △ | ✓ |
| Ad-hoc query to the issuer | △ | △ | ✓ |
| **ZK proof (Lemma)** | **✓** | **✓** | **✓** |

PDFs can be forged and altered; even signed, the contents are exposed. Only a ZK proof does all three. **Only work requiring all three at once is Lemma's territory.**
