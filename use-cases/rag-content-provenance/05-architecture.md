---
title: "Architecture in concept"
---


Lemma does not replace your RAG pipeline (vector DB, embedding model, retrieval layer). We add a provenance layer on the ingest and retrieval paths.

<!-- TODO: replace with a Mermaid or SVG diagram. Horizontal layout: Source → Lemma Ingest → Index → Retrieval → AI. -->
<!-- Temporary placeholder:

  [Source: Confluence / Box / SharePoint / etc.]
       │
       ▼
  [Lemma ingest gate]
       │ (AES-GCM encryption + docHash + CID + issuer signature)
       ▼
  [Existing vector DB / search index: docHash + metadata only]
       │
       ▼
  [AI retrieval / answer generation]
       │ (citations carry docHash)
       ▼
  [Answer + verifiable provenance chain]
-->

Source documents are encrypted with AES-GCM. Only the docHash, CID, issuer signature, and active-version metadata enter the vector DB and search index. Retrieved chunks emerge with their provenance attached, so each citation is cryptographically traceable. Document revisions are handled by issuing a new docHash; references to prior-version chunks are structurally detectable.

Integration patterns with vector DBs (Pinecone, Weaviate, Qdrant, etc.), retrieval frameworks (LangChain, LlamaIndex, etc.), and knowledge platforms are detailed in the whitepaper and the post-call technical kit.
