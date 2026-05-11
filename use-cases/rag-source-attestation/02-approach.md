---
title: "How Lemma approaches it"
---


For every source the AI cites in an answer, Lemma generates a ZK proof binding the citation to the docHash of the precise document version it references. A citation stops being a label and becomes a cryptographically bound reference. Source documents themselves never enter the index or the answer; what crosses to the verifier is only the cryptographic fact "this citation traces to paragraph B of policy v3, which was live at answer time."

When the vector DB is rebuilt and the policy is revised, the citation proofs attached to past AI answers remain intact. There's no reconstructing "what was that answer based on" after the fact — you reference the citation proof directly.

Where the citation-proof layer fits between your answer generation and citation step is what we map out in a first conversation.
