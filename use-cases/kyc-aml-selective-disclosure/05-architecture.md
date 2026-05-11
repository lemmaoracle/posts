---
title: "Architecture in concept"
---


Lemma does not replace your KYC vendor or your customer management system. We add one cryptographic layer between the issuer (the institution that already completed KYC) and the verifier (the new institution or regulator).

<!-- TODO: replace with a Mermaid or SVG diagram. Horizontal three-party layout (Issuer / Holder / Verifier) recommended. -->
<!-- Temporary placeholder:

  [Issuer: institution that already completed KYC]
       │ (issues a ZK proof per attribute)
       ▼
  [Customer's proof wallet]
       │ (consent + selective disclosure)
       ▼
  [Verifier: new counterparty / regulator]
-->

Each attribute is issued as an independent proof carrying the issuer's signature, expiry, and a cryptographic record of customer consent. Verifiers check those proofs directly — they never receive the source data. Revocation and refresh status propagate in real time.

The attribute catalog design, FATF Travel Rule integration, and patterns for plugging into existing KYC vendors (Onfido, Persona, etc.) are detailed in the whitepaper and the post-call technical kit.
