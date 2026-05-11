---
title: "How Lemma approaches it"
---


Lemma adds a cryptographic layer that independently verifies the origin of every cross-chain message **before** the receiving system commits state. We do not replace the DVN layer — we run **a second, independent verification** in parallel. Defense in depth.

If the DVN is compromised, or if a malicious RPC injects a forged message, the commit fails unless the origin proof verifies. Execution stops at the boundary — no waiting 46 minutes for a manual `pauseAll`. If post-incident malware wipes the RPC node and its logs, the on-chain anchored attestations survive; forensic evidence is preserved.

Whether this fits your bridge design and threat model is something we can confirm first through a conversation.
