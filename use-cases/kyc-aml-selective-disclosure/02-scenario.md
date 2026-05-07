---
title: "Scenario"
---

# Scenario

The representative of mid-size manufacturer Company A opened a personal account at regional Bank B two years ago, submitting identity documents, residence certificates, tax records, employment history, and shareholder structure through rigorous procedures.

In September 2026, Company A begins opening a corporate account at City Bank C to streamline remittances from an overseas subsidiary. C requires enhanced KYC and continuous AML monitoring. The representative's personal KYC must also be completed anew with full document submission.

The representative prepares the same documents again. C independently verifies information that B has already verified. B's PII does not cross borders, but C stores duplicate PII in its internal systems, responsible for protecting it under its own mandate. In the event of a breach, the impact scope widens with each institution.

With Lemma, the flow changes.

At the point of completing the representative's KYC, B issues cryptographic proofs for each attribute. The individual stores these proofs in their proof wallet. When proceeding with the corporate account at C, the representative selectively discloses only the following attributes:

- Identity verified (Issuer: B, Date: Month X, 2024)
- Residence: Japan
- Age: Over 18
- Not on sanctions list
- Not a PEP (Politically Exposed Person)
- Source of funds legitimacy confirmed

What C receives is B's signed ZK proof and cryptographic evidence of the representative's consent. The original address, date of birth, tax amounts, and specific shareholder structure remain under B's control. Only verified attributes and their proofs enter C's internal systems.

One year later, the FSA audits C's KYC framework. Without disclosing original PII, C can prove that each attribute arrived from the authentic issuer (B), within the validity period, without tampering. The auditor independently verifies.

The customer never submits the same documents twice. B does not transfer PII across borders. C holds less data under its own responsibility. The regulator verifies the audit trail cryptographically.

Compliance is established without data sharing.
