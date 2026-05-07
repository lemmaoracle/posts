---
title: "Architecture"
---


Lemma's four cryptographic layers correspond to the AI decision lifecycle.

**1. ENCRYPT — Sealing at Decision Time**

Input attributes, model state, and applied guidelines are encrypted with AES-GCM at the moment the AI decision is produced. Originals remain under the issuer's control; only docHash and CID are exposed externally.

**2. PROVE — ZK Proof Generation**

The proposition "this model identifier, against this input attribute hash, following this guideline, produced this decision" is sealed as a proof on a ZK circuit. Weights and input values are excluded; only decision integrity is proven.

**3. DISCLOSE — Selective Attribute Disclosure**

At audit time, attributes are selectively disclosed according to the verifier's authority. The regulator sees "guideline G-2026-04 applied" and "input attribute category"; the appellant sees only "final decision" — all enforced with issuer signatures.

**4. PROVENANCE — Permanent Record**

docHash, CID, ZK proof, and model identifier are anchored on-chain. Even if RAG indexes, models, and operational infrastructure are entirely replaced, the cryptographic identity of the decision remains permanently verifiable.

```
┌──────────────────────────────────────────────────────────┐
│  AI Inference Engine                                      │
│  Input → Model(v2.3) → Decision (deny / approve / score) │
└───────────────────────┬──────────────────────────────────┘
                        │ Decision event
                        ▼
┌──────────────────────────────────────────────────────────┐
│  ENCRYPT (AES-GCM)                                       │
│  • Encrypt input attributes                               │
│  • Hash model state                                       │
│  • Seal guideline with signature                          │
│  → Only docHash + CID exposed externally                  │
└───────────────────────┬──────────────────────────────────┘
                        │ Encrypted attributes
                        ▼
┌──────────────────────────────────────────────────────────┐
│  PROVE (ZK Circuit)                                      │
│  Proposition: "This model ID, against this input hash,    │
│  following this guideline, produced this decision"        │
│  → Weights/input values excluded; integrity proven only   │
└───────────────────────┬──────────────────────────────────┘
                        │ ZK proof
                        ▼
┌──────────────────────────────────────────────────────────┐
│  DISCLOSE (Selective Disclosure)                          │
│  Regulator → guideline applied + input attribute category  │
│  Audit firm → decision path + model identifier            │
│  Appellant → final decision only                          │
└───────────────────────┬──────────────────────────────────┘
                        │ Disclosed attributes
                        ▼
┌──────────────────────────────────────────────────────────┐
│  PROVENANCE (On-chain)                                   │
│  docHash / CID / ZK proof / model identifier              │
│  → Immutable even if model/RAG/infra are replaced         │
└──────────────────────────────────────────────────────────┘
```
