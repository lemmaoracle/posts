---
title: "Why existing tools fall short"
---


Three things at once: pass needed facts without exposing the raw data; let a third party / AI verify independently; keep a tamper-proof record of what was used.

| Tool | Pass without revealing | Independently verify what was used | Tamper-proof |
| :-- | :-: | :-: | :-: |
| Access control only | △ | ✗ | ✗ |
| Masking / anonymization | △ | ✗ | ✗ |
| Encryption only | ✓ | ✗ | ✗ |
| **ZK proof (Lemma)** | **✓** | **✓** | **✓** |

Access control decides "who can enter," not "what the AI used" provably after the fact. Only a ZK proof does all three. **Only work requiring all three at once is Lemma's territory.**
