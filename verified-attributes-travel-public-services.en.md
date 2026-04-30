---
slug: "verified-attributes-travel-public-services"
date: "2026.04.07"
category: "Human Impact"
section: "Essays"
title: "Verified Attributes in Travel and Public Services"
abstract: "Travel and public services suffer from an inefficient structure where the same personal information is repeatedly submitted and stored across multiple organizations. Passport copies, income certificates, and medical records spread across systems, increasing breach exposure. Lemma proposes a third option: 'Do not share raw data — circulate only verified facts.' This article explores a practical approach to streamlining hotel KYC, visa processing, and public benefit eligibility checks using ZK proofs, all while protecting privacy."
cover: "assets/qDu2w2RUZXs.jpg"
tags:
  - attribute-verification
  - zero-knowledge-proof
  - privacy
  - compliance
---

## Introduction

Standing in line at an airport check-in counter, many people have probably wondered: "Why do I have to keep showing the same documents over and over?" Passport, visa, hotel reservation confirmation, vaccination certificate — each checkpoint demands data on its own terms. Information is copied repeatedly, stored in multiple locations. Yet if a breach occurs in any one of them, you have no way of knowing.

The public services sector faces a similar problem. Applying for benefits, processing administrative procedures, verifying social security eligibility — to prove that you 'actually qualify,' applicants must bundle together income certificates, residency records, and medical information and bring them to the counter. Staff copy them, transcribe them into separate systems. This inefficiency, along with the risk of spreading data, is structurally baked in.

Lemma proposes a third option — an approach that says: "Do not share identity data; circulate only verified facts."

---

## The Reality of the Travel Experience: How Many Times Is KYC Repeated?

Modern travelers face multiple identity checks during a single trip.

Airline check-in, immigration control, hotel front desks, rental car companies, visa application agencies — each runs its own independent KYC (Know Your Customer) process and collects the same information separately. According to a 2023 survey by the European Union, the average business traveler submits identical personal information to 6–9 different organizations during a single trip.

This redundancy creates both cost and risk.

- **Operator cost**: KYC operations cost financial institutions approximately $100 billion annually, and the hospitality industry faces comparable overhead.
- **User burden**: Time and effort spent preparing and submitting documents.
- **Data breach risk**: More storage locations mean a larger attack surface.

Hotels are sometimes required by regulation to retain copies of guest passports for several years — even though the actual information needed boils down to a single question: "Is this person eligible to stay?"

---

## The Eligibility Verification Problem in Public Benefits

The public administration sector faces another structural issue: verifying whether someone "truly qualifies" for a given benefit.

Low-income household support, medical cost subsidies, child allowances — these programs require mechanisms for continuously confirming recipient eligibility. But current approaches demand excessive information disclosure from applicants. Income certificates may include detailed bank account information. Household composition certificates may contain personal data on the entire family.

For government agencies, storing, managing, and updating this information is a significant operational burden. In Japan, the 2025 My Number Portal digitalization initiative is advancing the digitization of various certificates, but the risk inherent in the "consolidating information" approach itself has not changed. Consolidated data carries the risk of a large-scale breach from a single compromise.

---

## Verified Attribute Flow with Lemma

Lemma's approach separates "what needs to be proven" from "the raw data that serves as evidence."

Consider the case of a travel visa.

**Conventional flow:**

Applicant → Passport copy + financial proof + travel history → Embassy → Review → Visa issuance
(Raw data is stored in multiple locations.)

**Lemma flow:**

Issuer of the applicant's attributes (bank, government agency)
→ Generates a ZK proof: "Balance exceeds threshold," "No illegal overstay in the past 5 years," "Valid passport holder"
→ Applicant submits this proof to the embassy
→ Embassy verifies only the authenticity of the proof (does not receive actual balance or travel history)
→ Visa issued if the proof satisfies all conditions
(Raw data stays on the applicant's device and never leaves it.)

The same principle applies to hotel KYC. With a verified attribute showing that "this person is the legitimate holder of this passport and is an adult," there is no need to photocopy every page of the passport. Front desk staff no longer need to handle passports and run them through copy machines.

---

## Implementation Scenario: A Day in the City

It is 2028. Person A is an overseas visitor from Tokyo.

In the morning, at hotel check-in, the front desk points a "Verifiable Credentials–compatible" tablet toward Person A. On Person A's smartphone, verified attributes issued by their home government are stored, set up before departure. "Adult status," "No criminal record," "Valid travel insurance" — these ZK proofs are sent to the hotel system with a single tap.

The hotel verifies the proofs through the Lemma Oracle. The result is simple: "All conditions met." Check-in is complete in two minutes. Person A's passport number, nationality, and exact date of birth do not exist in the hotel's database.

At midday, Person A tries to claim a senior discount at a city art museum. There is no need to dig through a wallet for ID. They simply present an attribute proof from the same smartphone showing "Age is 65 or older." The receptionist does not see a number. They only confirm whether the proof is "valid" or "invalid."

In the evening, heading to the airport, Person A encounters the same mechanism at departure screening. Attributes are automatically reconciled, and the gate opens if there are no issues. There is no need to spread out documents and wait for a stamp.

What makes this experience possible is that attributes are designed as "provable units."

---

## Balancing Privacy and Convenience

The feeling that "convenience comes at the cost of your data" is a trade-off that many digital services have created. Sign up for a loyalty card and purchase data is collected. Use an app and location data is gathered. Privacy in exchange for convenience — we have accepted this structure as inevitable.

Lemma's verified attributes dismantle this trade-off.

| Scenario               | Conventional Approach                                                  | Verified Attributes Approach                               |
| ---------------------- | ---------------------------------------------------------------------- | ---------------------------------------------------------- |
| Hotel KYC              | Passport copy stored in database                                       | Proof of "adult, no criminal record" only — no raw data    |
| Visa application       | Financial info, travel history submitted to embassy                    | Proof of eligibility only — specific figures not disclosed |
| Public benefit review  | Income certificate, household details submitted each time              | "Income below threshold" attribute proof auto-updated      |
| Age verification       | Date of birth disclosed                                                | Binary proof of "is adult / is senior"                     |
| Medical cost subsidies | Medical history, diagnosis certificates submitted to multiple agencies | Proof of "has the qualifying condition" only               |

Convenience improves. Verification gets faster. But what circulates is not raw data — only the fact that conditions are met.

For regulators, this model is equally effective. An on-chain log proving that "review was conducted" makes audit compliance easier than before. Detecting fraudulent benefit claims can also be automated as a proof integrity check.

---

## Summary

The structure where travelers keep showing the same documents every time is not a technological inevitability. It is a design choice. The same is true of the current system where public benefit applicants must disclose income details to authorities just to prove eligibility.

What Lemma's verified attributes propose is this: "Trust can be built without handing over data." It is a technology with the potential to go beyond streamlining the travel experience and serve as a catalyst for rethinking the very design philosophy of administrative digitalization and public services.

Separating "the fact you want to verify" from "the raw data behind it" — this shift in perspective is the starting point for realizing both privacy and convenience at the same time.

---

For travel, public services, and administrative digitalization professionals interested in Lemma's verified attributes architecture —
Whitepapers and demos are coming soon. We are currently offering early access to select partner companies and local governments.

[Register as a partner candidate (1 minute)](https://429bpd.share-na2.hsforms.com/2E6_TsCd2RUSdP4fKsuhxzw)
