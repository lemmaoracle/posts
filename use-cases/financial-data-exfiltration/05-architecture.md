---
title: "Architecture in concept"
---


Lemma does not replace your existing systems. We place an attestation gateway between users and your CRM/database — one extra layer.

<!-- TODO: replace with a Mermaid or SVG diagram. Horizontal layout, recommended size that fits an internal proposal deck. -->
<!-- Temporary placeholder:

  [User] → [Attestation Gateway] → [CRM / DB]
                    │
                    ▼
              [ZK proof generated]
                    │
                    ▼
            [Commitment tree]
                    │
                    ▼
              [On-chain anchored]
-->

Data itself is never touched. Only the *fact* of access is cryptographically attested. Because the proof is zero-knowledge, the access content (customer information) is never exposed. Anchoring the commitment on-chain makes any post-hoc modification detectable. Origin, receiver, and regulator can each verify independently.

The four implementation layers (ENCRYPT / PROVE / DISCLOSE / PROVENANCE) and integration patterns with existing stacks are detailed in the whitepaper and in the post-call technical kit.
