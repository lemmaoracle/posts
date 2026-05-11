---
title: "A real-world example: a legal AI citing a pre-revision policy"
---


An internal AI at a legal-tech firm reviews contracts and replies, "Clause 4.2 aligns with internal guideline GL-2025-08, section C." Six months later, after a regulator's reinterpretation, the internal guideline is revised to GL-2026-02. The RAG index has been rebuilt since.

A problem surfaces on one of those contracts, and the team needs to re-examine the past reviews. Which version of which guideline did the AI rely on, in which paragraph? The answer log says "GL-2025-08, section C," but the vector DB has been rebuilt and embeddings have shifted. There is no cryptographic evidence that the paragraph quoted at the time and the same-named paragraph in the current index are the same content.

With Lemma in place, every citation in an AI answer ships with a docHash proof. Differences between the paragraph C of the guideline as it was cited and paragraph C in the current revision are structurally detectable. No need to dig through answer logs — you reference the citation proof to cryptographically present "the answer relied on this exact docHash of section C in GL-2025-08."

Sector-specific citation requirement design, integration patterns with RAG and retrieval frameworks (LangChain, LlamaIndex, etc.), and evidence-trail design for EU AI Act / ISO 42001 are shared in the sector-specific kit we send after the consultation call.
