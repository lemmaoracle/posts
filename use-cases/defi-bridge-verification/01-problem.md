---
title: "Problem"
---


Cross-chain bridge exploits in 2026 share one structural pattern.

Transactions are cryptographically valid, but cross-system assumptions about origin are not verified before execution. The receiving system commits based on trust assumptions that can be — and have been — subverted.

Three structural failures enable bridge attacks:

- **Single point of verification failure:** When a DVN (Decentralized Verifier Network) operates with a 1-of-1 or low-threshold configuration, compromising its keys breaks the entire bridge.
- **RPC trust without verification:** Verifiers trust RPC nodes as the source of truth. If an attacker compromises multiple RPCs and forces failover, the verifier approves a fake message as legitimate.
- **Absence of pre-execution origin checks:** The receiving OFT adapter executes messages with valid signatures "as designed." There is no cryptographic path to independently ask whether the message was truly issued on the source chain.

The Kelp DAO attack (April 2026) is the clearest embodiment of this pattern. A 1-of-1 DVN approved a fake message, draining $292M. Post-incident malware on compromised RPC nodes deleted itself and the logs, eliminating forensic evidence.

The signature was valid. The message was forged. This gap must be closed with a cryptographic layer.
