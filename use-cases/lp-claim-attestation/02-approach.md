---
title: "How Lemma approaches it"
---


An LP completes identity verification with an issuer that already operates under a relevant regulatory regime — a regulated KYC provider, an attribute authority, a banking partner. The issuer signs a BBS+ credential against the LP's identity record. The LP holds the credential in its own wallet.

When the LP deposits into a Lemma-integrated protocol, the deposit path verifies a ZK attestation against the credential: "this LP passed KYC with an issuer accepted by this protocol, is resident in a region permitted by this pool's policy, and falls into the risk tier this pool requires." Nothing else crosses to the protocol. The protocol never holds, indexes, or retains the identity record itself.

Revocation lives at the issuer. If the LP's KYC lapses or region changes, the issuer publishes an update and the next deposit path verification fails — without the protocol ever scanning its own LP set.
