---
title: "Architecture in concept"
---


Lemma does not replace your AI answer generation (LLM, prompts, RAG retrieval). We add a citation-attestation step between answer generation and citation emission.

<!-- TODO: replace with a Mermaid or SVG diagram. Horizontal layout: Question → Retrieval → LLM Answer → Citation Attestation → Output. -->
<!-- Temporary placeholder:

  [Question] → [RAG retrieval] → [LLM answer generation]
                                       │
                                       ▼
                          [Citation attestation gate]
                                       │ (docHash binding + ZK proof)
                                       ▼
                          [Answer + citation proof chain]
                                       │
                                       ▼
                          [On-chain anchored]
-->

When the LLM produces an answer carrying citations, Lemma generates a ZK proof that bundles each citation's docHash, the answer timestamp, and the RAG index version in use. The source documents themselves do not appear in the citation or in the proof. Downstream audit and compliance checks simply reference the citation proof chain to cryptographically reproduce the original mapping between citation and source content.

Integration patterns with LLM and RAG frameworks (Anthropic Claude, OpenAI, LangChain, LlamaIndex, etc.), citation-format design (footnotes, sidenotes, JSON metadata), and evidence-trail design for EU AI Act / ISO 42001 are detailed in the whitepaper and the post-call technical kit.
