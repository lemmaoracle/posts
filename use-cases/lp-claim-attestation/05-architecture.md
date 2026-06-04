---
title: "Architecture concept"
---


The deposit path is the verifier. The credential lives in the LP's wallet, signed by an issuer accepted by the protocol. The ZK predicate is evaluated against the credential without revealing any of its fields.

<!-- TODO: replace with a single horizontal diagram suitable for inclusion in stakeholder briefs. -->
<!-- Placeholder:

  [Issuer (regulated KYC provider)] → [LP wallet holds BBS+ credential]
                                                │
                                          [LP initiates deposit]
                                                │
                                          [Pool contract verifies ZK attestation]
                                                │
                                  [Per-attribute predicate: KYC / region / risk tier]
                                                │
                                          [Deposit cleared or rejected]
-->

Multiple issuers can be accepted simultaneously — the predicate composes against an issuer registry the protocol maintains as configuration. Revocation flows from the issuer downstream so the next deposit verification picks it up without the protocol scanning its own LP set.

Pool-side policy DSL, issuer-side integration patterns, the on-chain enforcement reference, and the cost profile per deposit live in the whitepaper and the post-Discovery technical materials.
