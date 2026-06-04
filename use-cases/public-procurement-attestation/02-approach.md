---
title: "How Lemma approaches it"
---


The bidder collects per-attribute attestations against its own records: ISO certifications from the certifying body, financial-eligibility attestations from its auditor, track-record attestations against its own work history (or aggregated through an industry registry). Each attribute is signed by the issuer the procurement policy already trusts.

The bid carries a composed ZK proof against the tender's eligibility schema. The evaluator verifies the predicate — "the bidder holds an active ISO 14001 certification, meets the financial threshold for this tender class, and has at least N similar references in the last 5 years" — without ever holding the underlying certificates, balance sheets, or reference letters.

For consortia bids, prime contractors compose sub-supplier attestations the same way they compose their own. The evaluator sees one chained proof and verifies the consortium-level eligibility predicate without scanning sub-supplier records.
