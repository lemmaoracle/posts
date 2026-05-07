---
title: "Scenario"
---

# Scenario

A pharmaceutical company's clinical team continuously indexes ongoing trial protocols, SOPs, and regulatory submission documents into an internal RAG. An AI agent responds to healthcare professional inquiries by citing the applicable protocol.

Eighteen months into the trial, a regulatory audit arrives. The auditor demands: "Prove that the AI's response to healthcare professionals on August 18, 2026 was based on the protocol version approved at that time."

Over those 18 months, the protocol has been revised 7 times. The RAG index has been rebuilt with each update — no historical state remains. The document management ledger suggests "v3.2 should have been current on August 18," but there is no cryptographic evidence. Auditors do not accept estimates.

With Lemma, the following would have been recorded at the moment each document was indexed:

- Issuer signature and issuance timestamp
- Original document docHash and CID
- Cryptographic binding between each indexed chunk and the original
- Indexing timestamp

When the AI agent claims "On August 18, I cited Section 4.2 of protocol v3.2," the regulator can independently verify that the citation was bit-for-bit identical to the original at that time. No matter how many times the RAG index is rebuilt, the authoritative historical state remains permanently verifiable.

The auditor sees not estimates, but cryptographically sealed facts.
