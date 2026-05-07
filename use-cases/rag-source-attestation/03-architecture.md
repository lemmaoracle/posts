---
title: "Architecture"
---

# Architecture

Lemma's four cryptographic layers correspond to the AI response lifecycle.

**1. ENCRYPT — Sealing Citation Elements at Response Generation**

At the moment the AI agent generates a response, the retrieved chunks, the generated response text, and the citation spans within the response are encrypted with AES-GCM. Correspondences between elements are recorded without ever exposing the originals in plaintext.

**2. PROVE — Cryptographic Binding of Citations**

On a ZK circuit, the integrity of four elements is sealed as a proof: (a) AI agent identifier, (b) hash of retrieved chunks, (c) original docHash (already fixed by RAG Content Provenance), (d) citation spans in the response text. "What the AI saw and where it cited from" is proven end-to-end.

**3. DISCLOSE — Context-Specific Selective Disclosure**

Disclosure scope is controlled by the recipient's authority. The client receives citation locations and proof-of-existence for the original; the regulator receives full chunks and retrieval history; a third-party auditor receives only a proof of citation integrity — all enforced with issuer signatures.

**4. PROVENANCE — Permanent Record of the Response Itself**

Response timestamp, AI agent identifier, cited source docHash, and response hash are anchored on-chain. Even if the RAG index is rebuilt or the AI agent is replaced, the citation integrity of past responses remains permanently verifiable.

```
┌──────────────────────────────────────────────────────────┐
│  AI Agent Response Generation                             │
│  Query → retrieve → chunk selection → response text       │
└───────────────────────┬──────────────────────────────────┘
                        │ Response + citation spans + retrieved chunks
                        ▼
┌──────────────────────────────────────────────────────────┐
│  ENCRYPT (AES-GCM)                                       │
│  • Encrypt retrieved chunk set                            │
│  • Seal citation spans in response text                   │
│  → Record element correspondences without plaintext       │
└───────────────────────┬──────────────────────────────────┘
                        │ Encrypted citation elements
                        ▼
┌──────────────────────────────────────────────────────────┐
│  PROVE (ZK Circuit)                                      │
│  Binding: (a) AI agent identifier (b) chunk hashes        │
│           (c) original docHash (fixed by provenance)       │
│           (d) citation spans in response text              │
│  → Proves end-to-end "what AI saw and where it cited"     │
└───────────────────────┬──────────────────────────────────┘
                        │ ZK proof + citation binding
                        ▼
┌──────────────────────────────────────────────────────────┐
│  DISCLOSE (Selective Disclosure)                          │
│  Client → citation location + proof-of-existence          │
│  Regulator → full chunks + retrieval history              │
│  Third-party auditor → citation integrity proof only      │
└───────────────────────┬──────────────────────────────────┘
                        │ Disclosed attributes
                        ▼
┌──────────────────────────────────────────────────────────┐
│  PROVENANCE (On-chain)                                   │
│  Response timestamp / AI identifier / source docHash      │
│  / response hash                                         │
│  → Citation integrity immutable after RAG rebuild/agent   │
│    replacement                                           │
└──────────────────────────────────────────────────────────┘
```
