---
title: "Proven Facts"
---


When a multi-agent workflow runs with Lemma, the following facts are cryptographically proven at each delegation node — not logged after the fact:

- **Authorization chain (principal → delegate → sub-delegate)** — Who authorized whom, and on what basis. Each link in the chain carries a ZK proof binding the delegator to the delegate.
- **Scope inherited at each step** — What authority was passed down. Scope can only narrow through delegation; any expansion is rejected at verification.
- **Inputs and decisions per node** — What data each agent accessed and what it decided. The proof chain records the inputs that influenced each decision point.
- **End-to-end auditability** — The complete chain from original principal to final output is reconstructable with cryptographic evidence. No gaps, no ambiguities.
