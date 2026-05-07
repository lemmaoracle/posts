---
title: "Architecture"
---


Lemma's four cryptographic layers cover the entire RAG document lifecycle.

**1. ENCRYPT — Sealing at Ingestion Time**

At the moment a document enters the indexing pipeline, the original is encrypted with AES-GCM. The original remains under the issuer's control; only docHash and CID flow into the RAG infrastructure. The indexing platform never holds the original content in plaintext.

**2. PROVE — Cryptographic Binding of Index to Original**

On a ZK circuit, the integrity of four elements is sealed as a proof: (a) issuer signature, (b) docHash, (c) generated embedding vectors, (d) indexed chunk set. Retrospectively, "which original did this chunk come from, and where" can be verified without disclosing the original.

**3. DISCLOSE — Selective Disclosure per Verifier**

At audit time, disclosure scope is controlled by verifier authority. The regulator receives full chunks and issuer signatures; internal auditors receive metadata only; AI response viewers receive only a proof-of-existence for the cited source — all enforced with issuer signatures.

**4. PROVENANCE — Permanent Historical Record**

docHash, CID, issuer signature, indexing timestamp, and chunk bindings are anchored on-chain. Even if RAG indexes, vector stores, and LLM backends are entirely replaced, "what was an authoritative document at a given point" remains permanently verifiable.

```
┌──────────────────────────────────────────────────────────┐
│  Document Sources (policies, contracts, protocols, SOPs)  │
└───────────────────────┬──────────────────────────────────┘
                        │ Indexing pipeline input
                        ▼
┌──────────────────────────────────────────────────────────┐
│  ENCRYPT (AES-GCM)                                       │
│  • Encrypt original document                              │
│  • Seal issuer signature                                  │
│  → Only docHash + CID flow into RAG infrastructure        │
│  → Original content never held in plaintext               │
└───────────────────────┬──────────────────────────────────┘
                        │ docHash + CID + chunks
                        ▼
┌──────────────────────────────────────────────────────────┐
│  PROVE (ZK Circuit)                                      │
│  Binding: (a) issuer signature (b) docHash                │
│           (c) embedding vectors (d) chunk set              │
│  → Proves "which original this chunk came from, where"    │
│  → Verifiable without disclosing original                 │
└───────────────────────┬──────────────────────────────────┘
                        │ ZK proof + chunk binding
                        ▼
┌──────────────────────────────────────────────────────────┐
│  DISCLOSE (Selective Disclosure)                          │
│  Regulator → full chunks + issuer signature               │
│  Internal auditor → metadata only                         │
│  Viewer → proof-of-existence for cited source             │
└───────────────────────┬──────────────────────────────────┘
                        │ Disclosed attributes
                        ▼
┌──────────────────────────────────────────────────────────┐
│  PROVENANCE (On-chain)                                   │
│  docHash / CID / issuer signature / indexing timestamp    │
│  / chunk bindings                                         │
│  → Immutable even if index/vector store/LLM are replaced  │
└──────────────────────────────────────────────────────────┘
```
