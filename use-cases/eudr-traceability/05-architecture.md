---
title: "Architecture concept"
---


EUDR's multi-tier shape — producer, cooperative, mill, trader, importer — maps cleanly onto Lemma's BBS+ selective-disclosure layer. Each tier holds its own attribute schema and signs against its own source data.

<!-- TODO: replace with a single horizontal diagram suitable for inclusion in stakeholder briefs. -->
<!-- Placeholder:

  [Producer plot] → [Cooperative] → [Trader] → [EU Importer]
        │                │              │             │
   [Plot attest.]   [Aggregated]   [Chain attest.] [Due-diligence proof]
        │                │              │             │
        └─────────────────────────────►  [Single chained ZK proof → EUDR filing]
-->

Cooperatives use BBS+ aggregate composition so they can publish attestations on behalf of members without exposing per-plot data. Permit and harvest-date attributes are bound to the regulation's reference date inside the predicate. Revocation flows downstream as a fresh attestation — no re-issuance of the whole chain.

Commodity-specific schemas, cooperative onboarding patterns, the integration story with trade-management and chain-of-custody systems, and the audit interface for the EU authority live in the whitepaper and the post-Discovery technical materials.
