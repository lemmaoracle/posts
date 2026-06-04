---
title: "Architecture concept"
---


The eligibility schema is the protocol. Each tender publishes a predicate over a fixed set of attributes — certifications, financial thresholds, reference counts, jurisdiction-specific requirements. Bidders compose attestations against the predicate and submit a single ZK proof.

<!-- TODO: replace with a single horizontal diagram suitable for inclusion in stakeholder briefs. -->
<!-- Placeholder:

  [Certifying body]  [Auditor]   [Reference org]
         │              │             │
   [Attribute A]   [Attribute B]  [Attribute C]
         │              │             │
         └────────► [Bidder wallet] ◄──┘
                          │
                  [Composed ZK proof]
                          │
                          ▼
            [Evaluator verifies against tender predicate]
-->

Issuer trust is configured per tender. Public-sector issuers (certifying bodies, regulators, oversight authorities) can be expressed as a default issuer set inherited across tenders, with category-specific overrides. Consortium bids compose along the same axis as the bidder-side attestations.

Tender-category eligibility schemas, default issuer sets for public-sector procurement, the evaluator-side workflow integration, and the audit-trail interface live in the whitepaper and the post-Discovery technical materials.
