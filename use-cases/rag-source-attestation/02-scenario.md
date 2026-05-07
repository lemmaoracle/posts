---
title: "Scenario"
---


A major tax advisory firm's AI agent provides tax guidance to corporate clients, referencing tax law, notices, and past cases. The AI responds with explicit citations: "According to National Tax Agency Notice X, Article 3, the expenditure is deductible as a business expense."

In August 2026, a corporate client proceeds with accounting treatment based on the AI's response. During a post-settlement tax audit, the auditor states: "That notice contains no such provision." The client demands an explanation from the tax firm.

The firm checks the AI response logs. What remains is the response text and a source attribution "From Notice X." Whether the chunks the AI actually retrieved at the time matched the original Notice X, whether the response text accurately cited those chunks — none of this is retrospectively verifiable. The RAG index has been rebuilt since then due to additional notices; the historical state is irreproducible.

With Lemma, the following would have been sealed at the moment the response was generated:

- AI agent identifier and response timestamp
- Hash of the retrieved chunk set
- Cryptographic binding between those chunks and the originals (already fixed by RAG Content Provenance)
- Cryptographic mapping between citation spans in the response text and the corresponding chunks

The client and the tax firm can independently verify: "The Notice X, Article 3 cited in the August 18, 2026 AI response corresponds to this passage in the original at that time, and the response text is a verbatim citation from it." Or conversely: "The citation in the log does not exist in the original at that time" — this, too, can be cryptographically established.

Accountability is determined by proof, not speculation.
