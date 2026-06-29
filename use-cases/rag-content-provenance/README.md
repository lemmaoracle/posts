---
title: "RAG Content Provenance"
abstract: "Anchor each RAG document to verifiable provenance at ingest — docHash, CID, and issuer signature — so the authenticity of every AI citation becomes cryptographically traceable."
thesis: "Indexed ≠ trustworthy"
pillar: verifiable-origin
industries:
  - ai
cardSummary: "Fix document provenance at RAG ingestion so AI citations stay traceable."
targetVerticals:
  - Enterprise RAG platforms
  - Knowledge management
  - AI-native companies
tags:
  - rag
  - content-provenance
  - doc-hash
  - data-integrity
relatedUseCases:
  - rag-source-attestation
  - supply-chain-component-provenance
---

# Use Case: RAG Content Provenance

## Thesis

**Indexed ≠ trustworthy**

Enterprise RAG systems index internal documents daily, but document identity and issuer signatures are lost the moment content is ingested. Lemma encrypts originals with AES-GCM and stamps only docHash and CID into the index. AI only accesses facts with verifiable provenance.
