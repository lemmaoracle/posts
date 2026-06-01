---
title: "Architecture concept"
---


Lemma does not replace existing credit / sanctions data providers or your in-house decision pipeline. It drops a per-predicate ZK proof layer in one slot between the issuer (the compliance team / data provider / bank that ran the decision) and the verifier (the receiving counterparty / group company / auditor / AI agent).

<!-- TODO: replace with a single Mermaid or SVG diagram. Issuer (decider) / Holder (screened party) / Verifier (receiving side) laid out horizontally. -->
<!-- Provisional placeholder:

  [Issuer: deciding institution / data provider]
       │ (issues a ZK proof per predicate)
       ▼
  [Holder: the screened party's proof wallet]
       │ (consent + selective disclosure)
       ▼
  [Verifier: counterparty / group company / auditor / AI agent]
-->

Cryptographic constituents: BBS+ over BLS12-381 for attribute-level selective disclosure; Poseidon over BN254 list commitments with Groth16 (Circom circuits) for "(non-)membership" decisions that keep the full list private; docHash (P1 Verifiable Origin) to fix the decision's issue time and tamper-freeness. The verifier checks only the received proofs — never the underlying data, never the full list, never the query history.

Predicate-category design, connector patterns against existing credit / sanctions data sources, and the handling of list revocation and decision withdrawal live in the whitepaper and the post-call technical materials.
