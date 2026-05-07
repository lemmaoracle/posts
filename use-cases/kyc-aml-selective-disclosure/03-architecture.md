---
title: "Architecture"
---

# Architecture

Lemma's four cryptographic layers correspond to the KYC attribute lifecycle.

**1. ENCRYPT — Sealing Originals at Issuance**

Bank B encrypts the customer's KYC original data (documents, captured images, transcribed information) with AES-GCM at the point of KYC completion. Originals remain under issuer B's control. Attributes eligible for sharing are extracted from originals and converted to docHash-plus-issuer-signature form.

**2. PROVE — Per-Attribute ZK Proofs**

"Residence: Japan," "Age: over 18," "Not on sanctions list," "Not a PEP," "Source of funds: legitimate" — each attribute is individually proven on a ZK circuit. Independent proofs per attribute enable fine-grained disclosure control later.

**3. DISCLOSE — Customer-Driven Selective Disclosure**

From their proof wallet, the customer controls the disclosure recipient (financial institution, regulator, counterparty) and disclosure granularity (attribute category, specific value, proof-of-existence only). The issuer (B) signature reaches the recipient without gaps. **Disclosure is executed with cryptographic evidence of the customer's consent** — enabling post-hoc audit proof of agreement.

**4. PROVENANCE — Permanent Issuance and Revocation History**

Issuer identifier, issuance timestamp, attribute schema, and revocation status are anchored on-chain. If B updates KYC information or the customer is placed on a sanctions list, past attribute proofs are recorded as revoked. Verifiers can always independently confirm the latest status.

```
┌──────────────────────────────────────────────────────────┐
│  Bank B (Issuer)                                         │
│  Verifies original data at KYC completion                 │
└───────────────────────┬──────────────────────────────────┘
                        │ KYC complete
                        ▼
┌──────────────────────────────────────────────────────────┐
│  ENCRYPT (AES-GCM)                                       │
│  • Encrypt original PII (documents, images, transcriptions)│
│  • Extract attributes from originals                      │
│  → Generate attribute proofs with docHash + issuer sig    │
└───────────────────────┬──────────────────────────────────┘
                        │ Encrypted attribute proofs
                        ▼
┌──────────────────────────────────────────────────────────┐
│  PROVE (ZK Circuit)                                      │
│  Independent proofs per attribute:                        │
│  • Residence = Japan • Over 18 • Not on sanctions list    │
│  • Not a PEP • Source of funds legitimate                │
│  → Fine-grained disclosure control at verification time   │
└───────────────────────┬──────────────────────────────────┘
                        │ Per-attribute ZK proofs
                        ▼
┌──────────────────────────────────────────────────────────┐
│  DISCLOSE (Customer-Driven Selective Disclosure)          │
│  Customer proof wallet → control recipient and granularity │
│  Disclosure includes cryptographic evidence of consent     │
│  Issuer signature delivered intact to recipient            │
└───────────────────────┬──────────────────────────────────┘
                        │ Disclosed attributes + consent evidence
                        ▼
┌──────────────────────────────────────────────────────────┐
│  PROVENANCE (On-chain)                                   │
│  Issuer identifier / issuance timestamp / attribute schema │
│  / revocation status                                     │
│  → Revocation recorded on update / sanctions listing      │
│  → Verifiers always confirm latest status independently   │
└──────────────────────────────────────────────────────────┘
```
