---
title: "Scenario: a regulated cross-border LP pool"
---


A protocol team launches a permissioned-by-attribute liquidity pool. The pool's mandate excludes LPs resident in US states the protocol has not registered with, requires KYC completed against an issuer the protocol's legal counsel accepts, and restricts certain tranches to LPs at or below a configured risk tier.

In the off-chain KYC pattern, the protocol stands up a custodial database of LP records. It now owns a privacy promise it doesn't want to be enforcing, a custodian-class operational burden, and a target. A breach on that database harms LPs the protocol is trying to serve.

With per-attribute attestation, the protocol verifies on deposit: "the LP holds a credential issued by an accepted KYC provider; the credential carries a region attribute not in the protocol's excluded set; the LP's risk-tier attribute falls within this tranche's policy." Source identity records stay with the LP and the issuer. The protocol's compliance posture is auditable — any regulator can verify the protocol enforced its stated policy on every deposit — without the protocol ever being a custodian of identity data.

Pool-side policy configuration, jurisdiction taxonomies, the integration story with KYC issuers, and the on-chain enforcement reference live in the post-Discovery materials.
