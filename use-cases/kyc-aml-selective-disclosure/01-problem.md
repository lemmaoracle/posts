---
title: "Problem"
---

# Problem: Declared ≠ Verified

Financial institutions must verify customer identity, jurisdiction, age, and sanctions‑list status—the core of Know‑Your‑Customer (KYC) and Anti‑Money‑Laundering (AML) compliance. Today, this verification relies on **full document sharing**: a bank sends a customer’s passport, residence certificate, and tax ID to another bank or regulator, exposing the entire identity document.

## The Privacy‑Compliance Trade‑Off

1. **Privacy risk** – Every time a KYC document is shared, the customer’s sensitive personal data (date of birth, passport number, address) is copied and stored in another organization’s systems, multiplying breach surfaces.
2. **Data‑residency violations** – Cross‑border transfers of full identity documents often conflict with GDPR, CCPA, and local data‑sovereignty laws.
3. **Self‑declared attributes are untrustworthy** – A customer can claim “I am over 18” or “I am not on a sanctions list,” but without cryptographic proof, the bank cannot rely on such assertions.
4. **No selective disclosure** – There is no standardized way to prove *only* that a customer is a Japanese resident over 18 and not on a sanctions list, *without* revealing the underlying passport number, issue date, or full name.

## The Compliance Burden

- **Banks** spend millions on KYC utilities that still exchange full documents, because there is no alternative that satisfies both privacy and regulatory requirements.
- **Regulators** receive mountains of personal data they don’t need for supervision, creating unnecessary data‑protection liabilities.
- **Customers** lose control over their identity data after it leaves their bank; they cannot limit how it is used or who else sees it.

## The Gap

**Declared ≠ verified.** A customer can *declare* they are a Japanese resident over 18, but the bank cannot *verify* that claim without seeing the entire passport. And even after seeing the passport, the bank cannot *prove* to a regulator that the claim is true without handing over the passport copy—again violating privacy.

Lemma closes this gap by enabling **cryptographic selective disclosure**: a customer can prove specific KYC/AML attributes (jurisdiction, age threshold, sanctions status) with a zero‑knowledge proof, revealing nothing else about their identity documents.