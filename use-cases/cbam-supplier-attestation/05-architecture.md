---
title: "Architecture concept"
---


Lemma issues attestations against BBS+ selective-disclosure credentials at each tier of the supply chain. Each supplier holds its own attribute schema, signs against its production reference, and exposes only the per-attribute proof downstream.

<!-- TODO: replace with a single horizontal diagram suitable for inclusion in stakeholder briefs. -->
<!-- Placeholder:

  [Producer]   [Trader]    [Importer]
       │           │            │
  [Attest. A] [Attest. B]  [Attest. C]
       │           │            │
       └──────────────►  [Chained ZK proof]
                                │
                                ▼
                       [CBAM filing — verifiable by authority]
-->

Chained composition uses BBS+ predicate proofs so that the importer's final filing carries a single proof object — not a pile of upstream PDFs. Revocation propagates the same way: a supplier-side update to its production reference flows downstream as a fresh attestation. Existing CBAM-specific attribute taxonomies map cleanly onto the schema.

Tier-specific schemas, composition flow, the integration story with ERP and trade-management systems, and the audit interface for the EU authority live in the whitepaper and the post-Discovery technical materials.
