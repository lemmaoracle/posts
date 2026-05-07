---
title: "Architecture"
---

# Architecture

Lemma's four cryptographic layers correspond to the component provenance lifecycle in multi-tier supply chains.

**1. ENCRYPT — Per-Process Sealing of Records**

Records generated at each manufacturing and inspection process (production logs, inspection results, material certificates, captured images) are encrypted with AES-GCM at the moment the process step is completed. Originals remain under the process executor's control. Only component ID, docHash, and attribute proofs are passed upstream.

**2. PROVE — ZK Proofs of Component Identity**

On a ZK circuit, the integrity of four elements is sealed as a proof: (a) component ID, (b) binding to upstream lots, (c) passage evidence for each process step, (d) inspection-passed attributes. Confidential information such as supplier identifiers, contract terms, yield rates, and defect rates is not included in the proofs.

**3. DISCLOSE — Stakeholder-Specific Selective Disclosure**

Different recipients receive different attributes. The final customer (airframer) receives component ID plus certificate-equivalent attributes; regulatory authorities (FAA, EASA) receive the full provenance chain; recall response teams receive rapid extraction of impact scope only; third-party auditors receive the complete cryptographic audit trail — all delivered with issuer signatures, tamper-proof.

**4. PROVENANCE — Multi-Tier Permanent Chain**

Each tier's attributes are cryptographically bound to the upstream tier's attributes. Starting from the final component, the provenance chain traces back through Tier-1→Tier-2→Tier-3→Tier-4 as a permanent record. At recall time, downstream impact scope can be instantly extracted starting from the defective lot. If attributes are updated or revoked at any tier, integrity is reflected in downstream proofs.

```
┌──────────────────────────────────────────────────────────┐
│  Tier-4: Nickel and cobalt raw material trading companies │
│  → Raw material lot attributes encrypted with signature   │
└───────────────────────┬──────────────────────────────────┘
                        │ Signed raw material attributes
                        ▼
┌──────────────────────────────────────────────────────────┐
│  Tier-3: Specialty alloy smelters                         │
│  → Cryptographically bind Tier-4 attributes + own records │
│  → Generate alloy-lot-level attribute proofs              │
└───────────────────────┬──────────────────────────────────┘
                        │ Chained attribute proofs
                        ▼
┌──────────────────────────────────────────────────────────┐
│  Tier-2: Steel and specialty alloy manufacturers          │
│  → Bind Tier-3 attributes + own machining/inspection      │
│  → Component ID + docHash + inspection-passed attributes  │
└───────────────────────┬──────────────────────────────────┘
                        │ Chained component attributes
                        ▼
┌──────────────────────────────────────────────────────────┐
│  Tier-1: Company M (final machining, quality assurance)   │
│  PROVE: ZK proof of component identity                    │
│  DISCLOSE:                                               │
│    Airframer → component ID + certificate-equivalent      │
│    FAA/EASA → full provenance chain                       │
│    Recall response → rapid impact scope extraction        │
│    Auditor → complete cryptographic audit trail           │
└───────────────────────┬──────────────────────────────────┘
                        │ Disclosed proofs
                        ▼
┌──────────────────────────────────────────────────────────┐
│  PROVENANCE (On-chain)                                   │
│  All tier attribute anchors                               │
│  → Defective lot as starting point → instant downstream   │
│    impact extraction                                      │
│  → Updates/revocations auto-propagate to downstream proofs│
└──────────────────────────────────────────────────────────┘
```
