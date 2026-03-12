---
slug: "faq"
title: "Lemma FAQ — AI Trust & Verifiability"
date: "2026-03-12"
category: "FAQ"
section: "FAQ"
abstract: "Frequently asked questions about Lemma and Verifiable AI"
categoryColor: "#3B82F6"
---

# Lemma FAQ — AI Trust & Verifiability

About this page: A comprehensive Q&A covering how Lemma enables trustworthy AI — from technical foundations to practical implementation questions.

***

## 🔷 Section 1: What is Lemma?

**What is Lemma, in one sentence?**

Lemma is infrastructure that attaches cryptographically proven provenance to every piece of data an AI reads.

For every attribute an AI references — age, revenue, location, etc. — Lemma permanently guarantees: who issued it, which schema defined it, how it was proven, and where that proof is recorded.

***

**What does "AI Trust" mean?**

AI Trust means that the data an AI system references and reasons over can be verified as coming from a trusted source and has not been tampered with.

Traditional AI systems rely on the assumption that "the data is correct." Lemma makes that assumption itself cryptographically provable — a critical requirement in Retrieval-Augmented Generation (RAG) systems.

***

**What is Verifiable AI, and how is it different from traditional AI?**

Verifiable AI is an AI system where the basis for every decision — "who proved it, when, and how" — can be verified after the fact.

Traditional AI outputs results, but the origin and accuracy of the data used in reasoning remain a black box. With Lemma, the provenance of every attribute used in inference becomes cryptographically traceable, giving AI decisions genuine accountability.

***

**What is wrong with existing RAG and AI systems?**

Existing RAG systems have no mechanism to verify whether retrieved data is accurate, who issued it, or whether it meets required conditions — before feeding it to the AI.

Lemma adds a provenance and compliance verification layer before data ingestion, ensuring the integrity of the entire AI agent pipeline.

***

**How does Lemma relate to AI agents? Can it work with chat AI?**

Lemma operates at the RAG layer and is independent of any specific AI model or framework. It can be integrated into chat AI, AI agents, and automated workflows — any AI system that retrieves and reasons over data.

By inserting Lemma's verification layer into the process where an AI agent fetches and acts on external data, the basis for every agent decision becomes provable.

***

**What industries and use cases is Lemma best suited for?**

Lemma is relevant to any operation where AI references personal information or sensitive data to make decisions or recommendations.

**Regulated & high-risk domains (legal accountability required)**

* **Finance & payments**: Proof of basis for lending decisions, fraud detection, credit scoring; PCI DSS compliance
* **Healthcare**: Provenance assurance for patient-attribute-based recommendations and diagnostic support
* **Legal & compliance**: Permanent audit logs for contract review AI decisions
* **Government & public sector**: Transparency of data used in policy decisions and subsidy screening

**Everyday personal data domains (risk reduction through provenance)**

* **E-commerce & retail**: Traceability of attributes used by recommendation AI
* **HR & recruitment**: Proving fairness and absence of bias in attribute-based decisions
* **Marketing**: Provenance management for attributes referenced by personalization AI
* **Education**: Transparency of learner data used in adaptive AI systems

The common denominator is any situation where AI references data related to individuals to make a decision or take an action. Lemma's provenance logs serve as verifiable evidence of AI accountability under GDPR, Japan's Act on the Protection of Personal Information, the EU AI Act, and similar regulations — **reducing compliance costs** across the board.

***

**How is Lemma different from AI Gateways and data masking tools?**

| Solution     | Primary Role                                          | Difference from Lemma                            |
| ------------ | ----------------------------------------------------- | ------------------------------------------------ |
| AI Gateway   | API access control & filtering                        | Manages inputs. Does not prove data origin       |
| Data masking | Obfuscation of sensitive data                         | Protects data. Cannot prove "who issued it"      |
| **Lemma**    | Cryptographic proof of data origin + permanent record | End-to-end proof, traceability, and auditability |

Lemma does not compete with existing security tools. It functions as a complementary layer that generates and stores evidence that data has been verified.

***

## 🔷 Section 2: Security & Privacy

**How is personally identifiable information (PII) protected?**

Lemma never passes raw PII to the AI. What the AI handles is only an AES-GCM encrypted `docHash` and `CID` (content identifier).

The shared encryption key is generated via ECDH (Elliptic Curve Diffie-Hellman) and derived securely using HKDF (HMAC-based Key Derivation Function). The key itself never travels over the network — it is safely agreed upon only between the parties involved.

Symmetric encryption (AES-GCM) and key derivation (HKDF) are considered secure against quantum computers. For key exchange (ECDH), the system is designed with a migration path to a hybrid configuration with ML-KEM, standardized by NIST in 2024.

| Component  | Role                                                                                |
| ---------- | ----------------------------------------------------------------------------------- |
| No Raw PII | Ensures AI and systems never "see" personal data directly                           |
| AES-GCM    | Industry-standard authenticated encryption; also detects tampering                  |
| ECDH       | Public-key-based key agreement; shared key established without transmitting secrets |
| HKDF       | Derives a safe working key rather than using the raw shared secret                  |

***

**What is Zero-Knowledge Proof (ZKP) and why is it necessary?**

A zero-knowledge proof is a cryptographic technique that proves a condition is satisfied — without revealing the underlying data.

For example, "is the user 18 or older?" or "does revenue exceed a threshold?" can be conveyed to the AI without exposing the actual values. Each proof is permanently recorded alongside its circuit (verification logic) and generator, making it possible to later audit exactly how the proof was constructed.

***

**What is Selective Disclosure?**

Selective Disclosure means presenting only the attributes the AI needs — and nothing more.

Critically, even after partial disclosure, the linkage to the issuer's signature is preserved. Narrowing what is shared does not break the chain of provenance.

***

## 🔷 Section 3: On-Chain Provenance

**What does "recording on-chain" mean, and why does it matter?**

Document commitments, schemas, issuer information, and ZK verification results are recorded on a blockchain.

This means that even if the RAG index is rebuilt or embeddings are recalculated, provenance persists permanently. The evidence of what data was used never disappears.

***

**What are the business benefits of "permanent" provenance?**

In audit responses, compliance reporting, and incident investigations, you can prove after the fact exactly which data an AI used as the basis for its decision.

This is foundational for meeting Explainability requirements in regulated industries such as finance, healthcare, and law. It directly supports compliance with AI governance frameworks including the EU AI Act and ISO 42001.

***

## 🔷 Section 4: Developer Implementation

**What is a schema and how is it used?**

A schema models how AI retrieves and clusters knowledge — expressing attributes like age group, risk score, or region using typed definitions and normalization.

By registering ZK circuits and reproducible generators, every fact becomes traceable back to its original source.

***

**How does querying verified attributes work?**

For example, querying "users in Japan aged 18 or older" returns attribute data with full provenance attached — including proof status, schema, issuer, generator, and verification method.

The response is in a format ready to be used directly in a RAG policy layer.

***

## 🔷 Section 5: Trust & Adoption

**What exactly becomes "provable" with Lemma?**

The following four elements become cryptographically provable:

1. **Who issued it** (issuer identity)
2. **Which schema defined it** (data structure integrity)
3. **How the fact was proven** (ZK proof circuit and generation method)
4. **Where that proof is recorded** (permanent on-chain record)

***

**Do I need blockchain expertise to use Lemma?**

No. Lemma uses blockchain as a backend recording infrastructure, but developers do not need to interact with smart contracts directly. Integration with existing AI systems and RAG pipelines is done via API.

***

**What deployment options are available — SaaS, on-premise, etc.?**

Please contact us for details. We work with enterprise requirements around data residency, compliance constraints, and integration with existing infrastructure to determine the right configuration.

***

**What should a security team check first?**

We recommend starting with these three areas:

1. **Encryption & key management**: Confirm that AES-GCM encryption + ECDH/HKDF key derivation keeps PII private throughout
2. **ZKP auditability**: Verify that each ZK proof is recorded alongside its circuit and generator
3. **Provenance permanence**: Confirm that verification results are immutably recorded on-chain

***

**Where can I find the technical documentation?**

* [Encrypt Everything, Expose Nothing](/blog/encrypt-everything-expose-nothing)
* [Prove Facts with Zero Knowledge](/blog/prove-facts-with-zero-knowledge)
* [Disclose Only What AI Needs](/disclose-only-what-ai-needs)
* [Query Verified Attributes](/query-verified-attributes)
* [Define Your Domain as a Schema](/define-your-domain-as-a-schema)
* [Provenance That Never Disappears](/blog/provenance-that-never-disappears)
* [Lemma Oracle Specs (Specification)](/blog/lemma-oracle-specs/)