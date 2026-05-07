---
title: "Problem"
---

# Problem: Valid Crypto, Wrong Semantics

Cross-chain bridge exploits in 2026 share one structural pattern: the transactions are cryptographically valid, but cross-system assumptions about origin are not verified before execution. The receiving system commits based on trust assumptions that can be — and have been — subverted.

The Kelp DAO attack (April 2026) made this concrete: $292M drained because a 1-of-1 DVN approved a fake cross-chain message. The signature was valid. The message was forged. Post-incident malware deleted logs on compromised RPC nodes, eliminating forensic evidence.

**Cryptographically valid ≠ semantically right.** That is the structural gap Lemma closes.