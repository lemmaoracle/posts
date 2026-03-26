---
slug: "verifiable-ai-cryptographic-rag-design"
date: "2026.03.04"
category: "Tech Insight"
section: "Essays"
title: "Verifiable AI: A New RAG Design that Demonstrates Trust Origins with Cryptography"
abstract: "Addressing the trust challenges of AI agents and RAG systems, we propose a novel design that leverages cryptographic technologies (zero-knowledge proofs, selective disclosure, and provenance tracking). This approach builds a verification layer for data origins and condition compliance before AI reads the data, ensuring the overall reliability of the system."
cover: "assets/fjcJFETT2r8.jpg"
---

AI agents and RAG (Retrieval-Augmented Generation) systems are already beginning to play the role of "research, summarize, and answer" in many practical scenarios.  
However, they still cannot sufficiently answer the question, "Can we really trust this answer?"

Lemma addresses this gap by inserting a cryptographic verification layer for "data before AI reads it," thereby raising the overall reliability of AI systems.
This article aims to organize this approach in the context of RAG and agents, sharing the technical foundation for the entire 10-part series.

---

## Why "Verifiable AI" is Needed Now

Generative AI can rapidly ingest vast amounts of information—text, knowledge bases, logs, etc.—and return answers in natural language.  
However, in practical settings, it remains unclear "what information was used as the basis, and how was the decision made?" leaving concerns from the perspectives of accountability and compliance.

Particularly in enterprise use cases, requirements like the following always follow:

- We want to confirm who issued the information and its source
- We want to mechanically verify whether the information satisfies specific business rules (age restrictions, amount thresholds, etc.)
- We want to leave evidence in a form that allows for auditing and reproduction later

These cannot be satisfied by the intelligence of the LLM alone.  
We need to prepare a layer of cryptography, verification, and provenance "outside the AI."

---

## Weaknesses of RAG and Agents: The "Origin" of Data Remains Gray

In a typical RAG configuration, the flow is generally as follows:

1. Collect and index documents from inside and outside the company
2. In response to the user's question, retrieve relevant documents via vector search
3. Embed the retrieved text into the prompt and generate an answer using the LLM

Even in this configuration, showing citation URLs or document titles allows for a certain degree of "visualization of evidence."
However, there are limitations:

- We cannot know who approved the document and under what rules
- Whether business-critical attributes (e.g., region, qualifications, certifications, validity period) truly satisfy the conditions tends to rely on manual checks
- Over time, it becomes ambiguous under what premises the decision was made at which point, making reproduction and auditing difficult

In other words, while we can see the "text" of the document, the **verification logic and proof** behind it are missing.

---

## Lemma's Approach: Cryptographically Verify Attributes Before Passing to AI

Lemma provides a layer that shapes target data as cryptographically verified attributes at the stage "before AI reads it."

There are four main points:

1. All original documents are encrypted with AES-GCM, preventing AI from directly accessing raw data
2. Only hash values (docHash) and content IDs (CID) are exposed for documents
3. Business rules are converted into machine-readable facts via zero-knowledge proofs
4. The verification results and their provenance (who, with which schema, using which circuit proved) are permanently recorded

## Encrypt Everything, Expose Nothing

Lemma's documents are encrypted with AES-GCM, designed so that plaintext personal information or confidential data does not directly flow into the AI execution environment.
What AI sees is only the docHash and CID pointing to the encrypted document, which serve as anchors for provenance, as described later.

## Prove Facts with Zero Knowledge

Business rules such as "age 18 or older," "sales above a certain threshold," or "ISO certification obtained" are merely metadata of text as they stand.  
Lemma implements such rules as zero-knowledge proofs, enabling mechanical verification that "conditions are satisfied."

- Specific numerical values or detailed attributes are not disclosed
- Yet, only the fact that specified conditions are met is cryptographically guaranteed

At this point, the ZK circuits and generators used remain as metadata, allowing tracking of what logic was proven, even afterward.

---

## Selective Disclosure: "Disclose Only Necessary Attributes"

In real business, there are many situations where we think, "We don't want to show everything to the other party, but we want them to trust that the conditions are satisfied."  
Here, the mechanism of **Selective Disclosure** becomes crucial.

With schemes like BBS+ signatures, we can select only the necessary attributes from a set of attributes collectively signed by the issuer and include them in the proof.  
The verifier can, using links to the original signatures, confirm that "the partially disclosed attributes are indeed part of the correct original data."

Lemma's layer assumes such selective disclosure, allowing flexible control over the attribute sets handled by AI.

- Only the minimal attributes necessary for business logic are passed to AI
- Yet, links to the original issuer, schema, and verification methods are preserved

This enables "minimizing the information shown while maximizing the retention of trust foundations."

---

## Querying Verified Attributes: The "Attribute Layer" Before RAG

A distinctive point of Lemma is its design where "AI can query only verified attributes before reading the data."

Consider the following query, for example:

"Retrieve FAQs about users who reside in Japan, are aged 18 or older, and have purchased a specific product."

In traditional RAG, user records or logs would need to be directly indexed, with filtering applied at search time or manual preprocessing required.  
Lemma transforms this into the following form in advance:

- Attributes like "residence country," "age," "purchase history" are maintained as verified attributes
- Conditions like "aged 18 or older" and "residing in Japan" are registered as proven facts via ZK circuits
- RAG and agents only need to query "attributes that satisfy the conditions"

What returns is not mere records but "attributes with proof," including:

- Issuer
- Schema
- Verification status
- ZK circuit and generator used
- Related CID and docHash

By only passing data that has gone through this layer to RAG, the premise that "all information AI reads is verified" holds.

---

## Define Domains with Schemas: Fixing AI's "Unit of Interpretation"

Verified attributes and ZK alone are still fragmented.  
Lemma also emphasizes **defining schemas** for "which attributes are handled with what types, ranges, and categories."

For example, schemas can be designed at granularities like:

- Bucketizing age as "18 or older," "18–25," "26–40"
- Defining regions in business-appropriate hierarchies like country, region block, business area
- Expressing risk scores as numerical ranges or stages (Low/Medium/High)

This gives AI agents the following benefits:

- Since attribute meanings and ranges are clarified in advance, "interpretation" becomes stable
- Verification logic (ZK circuits) are tied to schemas, enabling later reproduction
- Embeddings and clustering can also be performed assuming the same schema

By placing a "schema + verification" layer on top of AI, the consistency and reproducibility of the entire RAG system improve.

---

## Provenance Persists: "Proof" Remains Even if Indexes Change

In RAG implementations, it is common to improve by changing index structures, vector databases, and model configurations.  
However, if "the premises of past decisions are lost" each time, auditing and re-evaluation become difficult.

Lemma treats the following information as a persistent provenance layer:

- Document commitments (docHash and CID)
- Schema definitions
- Issuer information
- ZK verification results and their methods

These are anchored in forms like on-chain, so even if RAG indexes or embeddings are rebuilt, we can later trace "what facts satisfied which premises at which point in time."

In other words, **while AI infrastructure may change, the proofs that form the trust foundation continue to persist**.

---

## **Conclusion: AI as the "Inference Engine," Trust Guaranteed by the "Cryptographic Layer"**

When trying to realize verifiable AI, we tend to think about "more rigorous models" or "safer prompts."  
However, instead of placing all responsibility on the AI itself, by placing **cryptographic verification layers and provenance layers** before it, we can significantly raise the overall system reliability.

Lemma:

- Protects documents by encrypting them while anchoring provenance with docHash and CID
- Transforms business rules into machine-readable facts via zero-knowledge proofs
- Enables selective disclosure of only the minimum necessary attributes
- Provides an environment where AI can "query only verified attributes before reading"

The design philosophy demonstrated in this article is the technical premise common to all business, governance, and social implementation use cases covered in the 10-part series.
In the next article, we will delve into how, building on this verification layer, cryptographic trust chains between agents impact the API economy and B2B collaborations.

---

**Partner with Lemma Oracle**

Technical details and demo environments for Lemma Oracle’s “verifiable AI” and cryptographic RAG design are currently available in a closed, partner-only phase.
If you are interested in this approach and would like to receive priority access as a potential partner organization, please apply below.

[Register as a partner candidate (1 min)](https://429bpd.share-na2.hsforms.com/2E6_TsCd2RUSdP4fKsuhxzw)

