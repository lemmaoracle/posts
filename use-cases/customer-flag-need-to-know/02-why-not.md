---
title: "Why existing tools fall short"
---


Three things at once: pass the handling action without exposing the reason; let authorized staff/auditors verify independently; make who-set-it-when tamper-proof.

| Tool | Pass action without the reason | Independently verifiable | Tamper-proof |
| :-- | :-: | :-: | :-: |
| Customer ledger / notes | △ | ✗ | ✗ |
| Access control only | ✓ | ✗ | ✗ |
| Hand-keyed flag | ✓ | ✗ | ✗ |
| **ZK proof (Lemma)** | **✓** | **✓** | **✓** |

Access control can hide the reason from the front line, but can't independently prove the flag is legitimate and untampered. Only a ZK proof does all three. **Only work requiring all three at once is Lemma's territory.**
