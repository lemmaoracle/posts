---
title: "What Lemma cryptographically guarantees"
---


- The ingest time, issuer signature, docHash, and CID of every document chunk
- Identity with the source — with the source itself kept encrypted under AES-GCM
- A new docHash on revision, with references to prior-version chunks structurally detectable
- A cryptographic binding between citations in AI answers and the underlying source version, verifiable by third parties
