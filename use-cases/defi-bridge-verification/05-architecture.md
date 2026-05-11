---
title: "Architecture in concept"
---


Lemma does not replace your DVN or your receiving adapter. We add a single verification gate immediately before incoming messages are processed.

<!-- TODO: replace with a Mermaid or SVG diagram. Horizontal layout: source chain → messaging layer → Lemma origin-verification gate → receiving adapter. -->
<!-- Temporary placeholder:

  [Source chain] → [Messaging layer (LayerZero / Wormhole)] → [DVN approval]
                                                                  │
                                                                  ▼
                                                  [Lemma origin-verification gate]
                                                                  │
                                                                  ▼
                                                  [Receiving adapter: commit]
-->

The gate demands a ZK proof that the message was emitted under verifiable conditions on the source chain. If the proof does not verify, the receiving adapter refuses to commit state — no matter how many DVNs approved. The verification outcome and the attestation hash are anchored on-chain, so they remain available for audit and forensics.

The integration is messaging-layer-agnostic: the same origin-verification pattern fits LayerZero, Wormhole, IBC, and beyond. Per-stack integration patterns, parallel operation with DVNs, and emergency-pause flow design are detailed in the whitepaper and the post-call technical kit.
