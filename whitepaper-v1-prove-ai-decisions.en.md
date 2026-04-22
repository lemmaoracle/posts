---
slug: "whitepaper-v1-prove-ai-decisions"
date: "2026.04.23"
category: "Announcement"
section: "Essays"
title: "Whitepaper: Prove What Your AI Decided On."
abstract: "As AI agents start making real operational decisions, most systems cannot cryptographically prove what those decisions were based on. Lemma Oracle publishes its whitepaper (v1.0), introducing a trust infrastructure that proves facts without disclosing the underlying data. The paper details three guarantees — authenticity, privacy, and auditability — alongside five CORE use cases and two ADVANCED agent-economy scenarios, framed for the EU AI Act era."
cover: "assets/nv1onQJsNJm.jpg"
---

## Introduction

AI is entering operations.

Credit screening, procurement decisions, ad placement — work that humans used to handle case by case is being taken on by AI agents. Many organizations are still moving through proof-of-concept phases, but in some domains agents are already in production, and their decisions are shaping real transactions and outcomes.

When that happens, a question surfaces.

**What exactly is the AI basing its decision on?**

The documents a RAG reads carry real risks: unclear provenance, potential tampering. Whether conditions like "revenue above threshold" or "KYC complete" were actually satisfied cannot be cryptographically verified in most current systems. Logs can be reformatted after the fact, and when the index is rebuilt, the basis for past decisions disappears. The EU AI Act (full enforcement August 2026) is turning this question into a regulatory obligation.

Today, Lemma Oracle publishes its whitepaper (v1.0).

---

## 2. The gap in today's stack

Three structural gaps exist in current AI infrastructure.

|  | Conventional | Lemma Oracle |
| :---- | :---- | :---- |
| **Data verification** | Manual fact-checking. Does not scale. | ZK proofs cryptographically assure the fact-check result itself. |
| **KYC / AML** | Share raw data with third parties. Major privacy risk. | Share only threshold-pass proofs. Raw data never leaves. |
| **Audit trail** | Logs collected and shaped after the fact. Tamperable. | Auto-generated on-chain at the moment of verification and settlement. |

Each gap traces back to the same missing layer: the ability to prove a fact without sharing the underlying data. Existing tools specialize in either verification or payment — no layer unified both and auto-generated an audit trail, until Lemma.

---

## 3. Lemma's answer — prove the fact, keep the data

Lemma Oracle provides trust infrastructure that verifies data before the AI reads it, and keeps the proof permanently.

The RAG index can change. The model can be updated. The agent framework can move to a new generation. The proof stays.

Models change. Proofs remain.

Three guarantees make this possible.

**Authenticity — attributes proved cryptographically.** Business rules like "revenue above threshold," "over 18," or "KYC complete" are converted into machine-verifiable facts as ZK proofs — without exposing the underlying values. Each proof is permanently recorded alongside its ZK circuit and generator, so the logic behind any past decision remains traceable by third parties.

**Privacy — only what AI needs is disclosed.** BBS+ selective disclosure reveals only the attributes the AI requires. AES-GCM encryption protects the full document; the AI sees only the docHash and CID. Even after partial disclosure, the link to the original issuer signature remains intact.

**Auditability — proof anchored on-chain.** Verification results, schemas, and issuer information are persisted on-chain. Rebuild the RAG index, recompute the embeddings — provenance stays. Audit trails for EU AI Act, financial regulation, and ISO compliance are auto-generated at the moment of verification.

---

## 4. Seven use cases, and a blueprint for what comes next

The whitepaper covers five CORE use cases and two ADVANCED scenarios.

CORE is trust infrastructure you can integrate into existing operations today: audit and provenance, financial KYC/AML, supply chain trust, media and content trust, and IP protection. Each is shown with a conventional-vs-Lemma comparison and an implementable design.

ADVANCED is a near-future blueprint for an economy where agents handle production, procurement, and payment autonomously. One of these scenarios — agents that carry proofs and settle payments in a single flow — we'll be announcing in detail next week.

Which use case fits your situation is a conversation we'd like to have.

---

## Request the whitepaper

16 pages. Executive summary through five-layer architecture, TypeScript SDK reference, pricing overview, and FAQ — the complete design and philosophy behind Lemma Oracle.

We're currently prioritizing access for companies and engineers considering our closed partner program.

[Register as a partner candidate (1 min) →](https://lemma.frame00.com/services)

Next week, we'll be making a formal announcement on one of the designs previewed in this whitepaper.

---

*Built for decisions that matter.*
