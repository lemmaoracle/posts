---
title: "Proven Facts"
---

# Proven Facts

Lemma cryptographically guarantees the following facts in RAG source attestation:

- Response generation timestamp and AI agent identifier
- Hash of the retrieved chunk set
- Cryptographic binding between cited original (docHash) and citation spans in the response
- Character-level consistency between citation text and original
- Absence of hallucinations (citations that do not exist in the original)
- Citation integrity of past responses, immutable after RAG rebuilds
- Citation authenticity independently verifiable by third parties without disclosing originals
