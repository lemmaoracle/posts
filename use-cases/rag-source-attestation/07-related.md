---
title: "Related Use Cases"
---


## 1. **[AI Audit Log Proof (P2)](/use-cases/ai-audit-log-proof)**
**Thesis:** "Logged ≠ immutable."

### Why It's Related
Both use cases address **verifiability of AI system outputs**. While *rag‑source‑attestation* proves that a specific citation matches a source document, *ai‑audit‑log‑proof* ensures that the entire audit trail of AI decisions—including queries, responses, model versions, and inference parameters—is tamper‑evident and anchored on‑chain.

### Complementary Focus
- **Citation‑level proof** vs. **log‑level proof**.
- *RAG‑source‑attestation* provides fine‑grained, per‑citation binding; *AI‑audit‑log‑proof* provides coarse‑grained, holistic integrity of the entire interaction log.
- Together, they enable **end‑to‑end verifiability**: from the user query, through the AI's reasoning, down to the exact document fragments cited.

### Overlap in Technology
- Both use ZK proofs and on‑chain anchoring (LemmaRegistry).
- Both require issuer signatures and timestamp immutability.
- Both are designed to be independent of the underlying AI infrastructure's mutable state.

## 2. **[RAG Content Provenance (P1)](/use-cases/rag-content-provenance)**
**Thesis:** "Indexed ≠ trustworthy."

### Why It's Related
*RAG‑source‑attestation* assumes the document being cited already has a verifiable provenance. **RAG‑content‑provenance** provides that foundation: it ensures each document ingested into the RAG index is cryptographically bound to its source, issuer, and ingestion timestamp.

### Upstream/Downstream Relationship
- **Upstream:** *RAG‑content‑provenance* guarantees that documents entering the index have known, trusted origins.
- **Downstream:** *RAG‑source‑attestation* guarantees that citations pulled from the index refer to those verified documents.

### Layered Assurance
Without provenance at ingestion, citations could be bound to documents of unknown or malicious origin. With both layers:
1. **Ingestion‑time proof:** Document X comes from Legal Department, signed at time T₁.
2. **Citation‑time proof:** AI’s citation of paragraph Y is bound to Document X’s hash at time T₂.
3. **Chain of trust:** The citation inherits the provenance of the document it references.

## Cross‑Use‑Case Scenarios

### Scenario A: Compliance Audit of AI‑Driven Policy Advice
- **RAG‑content‑provenance** ensures the policy documents in the index are authentic and up‑to‑date.
- **RAG‑source‑attestation** proves that each piece of advice cites the correct policy version.
- **AI‑audit‑log‑proof** provides an immutable record of which employee asked which question, when, and what the AI answered.

### Scenario B: Litigation Discovery
- A court requests evidence that an AI’s response relied on a specific version of a regulatory document.
- *RAG‑source‑attestation* supplies the citation proof.
- *RAG‑content‑provenance* supplies the proof that the regulatory document was issued by the competent authority.
- *AI‑audit‑log‑proof* supplies the proof that the query and response were not altered after the fact.

### Scenario C: Supply‑Chain Due Diligence
- An AI reviews supplier certifications and flags non‑compliant suppliers.
- *RAG‑content‑provenance* verifies the authenticity of the certification documents.
- *RAG‑source‑attestation* verifies that the AI’s “non‑compliant” verdict cites the exact clause that was violated.
- *AI‑audit‑log‑proof* keeps a tamper‑proof record of the due‑diligence process.