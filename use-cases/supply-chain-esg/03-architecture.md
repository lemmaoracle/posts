---
title: "Architecture"
---

# Architecture

Lemma's four cryptographic layers correspond to the ESG attribute lifecycle in multi-tier supply chains.

**1. ENCRYPT — Per-Tier Sealing of Originals**

Each supplier encrypts measurement data originals (production records, energy consumption logs, third-party audit reports) with AES-GCM. Originals remain under the supplier's control. Only attribute values extracted from originals, docHash, and issuer signatures are passed upstream.

**2. PROVE — ZK Proofs Against Regulatory Thresholds**

On a ZK circuit, proofs are generated for each specific CBAM, EUDR, and DPP requirement:

- "Embedded carbon emissions are at most X tonnes CO₂/tonne" (CBAM)
- "Country of origin is not in a deforestation-risk area" (EUDR)
- "Recycled content ratio is at least Y%" (DPP)
- "No forced labor was involved" (labor condition proof)

Supplier identifiers, contract terms, procurement prices, and specific production processes are not included in the proofs.

**3. DISCLOSE — Stakeholder-Specific Selective Disclosure**

Different recipients receive different attributes. EU customs receives an aggregated CBAM compliance proof; end consumers receive DPP-relevant attributes; the procurement department's ordering agent receives per-batch attestations; ESG auditors receive the full provenance chain — all delivered with issuer signatures, tamper-proof.

**4. PROVENANCE — Multi-Tier Cryptographic Chain**

Each tier's attestation is cryptographically bound to the upstream tier's attestation. Starting from the final product's DPP, the provenance chain can be traced back through Tier-1→Tier-2→Tier-3→Tier-4→Tier-5 without disclosing any supplier names. If an attribute is updated or revoked at any tier, downstream proofs automatically recalculate integrity.

```
┌──────────────────────────────────────────────────────────┐
│  Tier-5: Extraction site (power source composition)       │
│  → Fossil/renewable ratio encrypted with signature        │
└───────────────────────┬──────────────────────────────────┘
                        │ Signed emissions attributes
                        ▼
┌──────────────────────────────────────────────────────────┐
│  Tier-4: Mine operator                                   │
│  → Cryptographically binds Tier-5 attributes + own values │
│  → Generates raw-material-lot-level attestations          │
└───────────────────────┬──────────────────────────────────┘
                        │ Chained attestations
                        ▼
┌──────────────────────────────────────────────────────────┐
│  Tier-3 → Tier-2 → Tier-1                                │
│  Each tier: encrypt measurements, bind upstream attributes│
│  → Supplier names, contract terms, prices remain private  │
└───────────────────────┬──────────────────────────────────┘
                        │ Complete provenance chain
                        ▼
┌──────────────────────────────────────────────────────────┐
│  Company S (Exporter)                                    │
│  PROVE: ZK proofs against CBAM/EUDR/DPP thresholds       │
│  DISCLOSE:                                               │
│    EU Customs → Aggregated CBAM compliance proof          │
│    Consumer → DPP-relevant attributes                     │
│    Procurement agent → Per-batch attestations             │
│    ESG auditor → Full provenance chain                    │
└───────────────────────┬──────────────────────────────────┘
                        │ Disclosed proofs
                        ▼
┌──────────────────────────────────────────────────────────┐
│  PROVENANCE (On-chain)                                   │
│  All tier attestations anchored                           │
│  → Update/revocation at any tier triggers downstream      │
│    integrity recalculation                                │
│  → DPP to Tier-5 traceable without supplier name exposure │
└──────────────────────────────────────────────────────────┘
```
