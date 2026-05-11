---
title: "What Lemma cryptographically guarantees"
---


- The delegator, delegatee, scope, and timestamp of every delegation step
- The monotonic narrowing of scope (re-delegation can tighten but never widen) and the cryptographic binding of the delegation chain
- Cryptographic verification of the caller's authority at every tool / API invocation — no reliance on agent self-attestation
- A continuous, verifiable path from the original principal to the final action, with post-hoc tampering structurally detectable
