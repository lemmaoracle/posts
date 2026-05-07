---
title: "Problem"
---


AI agents have developed a habit of citing sources in their responses. "Pursuant to Internal Policy Article 3, Section 2," "According to National Tax Agency Notice X," "Article 7 of the contract states" — such citations underpin the persuasiveness of responses and are prerequisites for operational use.

But recipients have no means to verify whether a citation is authentic. From the response text alone, three categories are indistinguishable:

- **Correctly cited from an existing original**
- **Paraphrased from an existing original, with subtly different wording**
- **Entirely fabricated citation (hallucination)**

Additional structural problems:

- There is no cryptographic proof mapping which part of the response text corresponds to which retrieved chunk
- Whether the retrieved chunks match the originals is ambiguous unless guaranteed by a separate layer
- As the RAG index is rebuilt over time, the chunks that past responses referenced disappear

Legal, tax, healthcare, financial, and consulting — in every one of these domains, decisions based on incorrect citations escalate into serious liability issues. Retrospective verifiability of responses is not an operational option; it is a precondition for business continuity.
