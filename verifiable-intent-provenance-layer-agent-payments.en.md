---
slug: "verifiable-intent-provenance-layer-agent-payments"
date: "2026.06.22"
category: "Industry"
section: "Essays"
title: "From Proof of Intent to Verification of Grounding — Verifiable AI for the Agent-Payments Era"
cover: "assets/cover-industry.png"
abstract: "With AP2 and Mastercard's Verifiable Intent, the standards for proving the authenticity of intent in AI-agent payments are falling into place. In real-world operation, you need an additional layer on top — one that independently verifies what an agent's output is grounded in, i.e. its provenance. This essay distinguishes the authenticity of intent (the consent layer) from the verifiability of grounding (the provenance layer), and explains, from a verifiable-AI standpoint, the provenance-proof layer that sits on top of the standards."
tags:
  - verifiable-ai
  - agent-payments
  - ai-provenance
  - verifiable-intent
  - ap2
  - zk-proof
  - agent-authority
---

**TL;DR**
Standards that let an AI agent prove "what it was authorized to do" as a tamper-resistant credential are coming together fast in the payments space. With AP2 and Mastercard's Verifiable Intent, the authenticity of intent is now an industry assumption. Add one more layer on top, and AI-agent payments become far easier to put into real-world use. That layer independently verifies "what an agent's output — produced on the basis of authentic intent — is actually grounded in." It is **AI provenance proof**. This essay maps out the layers now in place and the verifiable-AI layer that sits on top of them.

> Pays ≠ trustworthy. — Being able to pay (authorization) and being trustworthy (grounding) are different layers.

**Built for decisions that matter.**

---

## ▸ What's happening in payments now: AI-agent payments and the "proof of intent" standards (AP2 / Verifiable Intent)

As 2026 began, standards for "proving intent" around AI-agent payments came together rapidly.

Google's **Agent Payments Protocol (AP2)** expresses agent-driven purchasing as three signed Mandates — the Intent Mandate (what the user wanted), the Cart Mandate (what the agent assembled), and the Payment Mandate (how payment is made). Each is signed as a W3C Verifiable Credential and passes between parties as verifiable JSON.

In contrast, **Verifiable Intent**, which Mastercard co-developed with Google, is a cryptographic credential framework that bundles "the user's identity, the original instruction, and the outcome of the transaction" into a single tamper-resistant record and converts it into evidence that a third party can verify. Built on top of FIDO Alliance, EMVCo, IETF, and W3C standards, it uses Selective Disclosure to reveal only the minimum necessary. On April 28, 2026, it was contributed to the FIDO Alliance together with AP2, with more than 60 organizations signing on.

If AP2 defines "how intent is created and shared," then Verifiable Intent defines "how intent is proven" — that is how the industry is beginning to frame it. The foundation for AI-agent payments is now moving toward standardization all at once.

## ▸ What gets proven is the "authenticity of intent" — so what do you layer on top?

What all of these standards guarantee is the layer of "the authenticity of intent."

"Did the user truly authorize this payment?" "Has the agent stayed within the signed scope?" These are indeed protected by the fact that the three Mandates are asymmetrically signed as W3C Verifiable Credentials. If the agent later rewrites the price or items, the entire credential becomes invalid — it is a tamper-evident design.

In real-world operation, one more requirement joins this picture: "what the output the agent returned — and the facts it referenced — on the basis of that intent are actually grounded in." The better this requirement is met, the easier it becomes to put an AI agent's judgments into real-world use. That a payment is correctly signed and that the source of the material behind a judgment can be traced are problems on different layers.

> Cryptographically valid ≠ semantically right. — Being cryptographically valid and having sound grounding for a judgment are two different things.

In current operating models for security and payments, this "verifiability of grounding" has not yet been standardized as an independent layer. The further payments standardization advances, the clearer the requirements become for the **verifiable-AI** layer that needs to sit on top of it.

## ▸ How far is it proven, and where do you add the verification layer? (consent layer and provenance layer)

Viewed at the level of technical primitives, the boundary becomes clearer.

**The consent layer (authenticity of intent)** — Verifiable Intent and AP2's Mandates are built on W3C Verifiable Credentials, and selective disclosure uses signature schemes such as BBS+ over BLS12-381. What it secures is "who authorized what, and whether that record has been tampered with." The value lies in turning the authenticity of identity and consent into portable evidence.

**The provenance layer (verifiability of grounding)** — What Lemma addresses is the question right next door: "Do the facts an agent referenced and the model's output have verifiable provenance?" Here, **provenance-proof** primitives come into play — for example, using Poseidon over BN254 as the hash, proving circuits written in Circom with Groth16. Rather than guaranteeing the meaning of the output itself, it is an approach that cryptographically demonstrates that the output's grounding can be traced.

The authenticity of intent (the consent layer) and the verifiability of grounding (the provenance layer). The two are complementary, and only by layering both do you reach "a judgment you can trust to delegate." The arrival of Verifiable Intent is proof that the former has become an industry standard — and, at the same time, a prime opportunity to layer on the latter.

> Models change. Proofs remain. — Models get swapped out. Proofs remain.

That is precisely why the verification layer must be independent of the model — this is Lemma's starting point. Among Lemma's [four pillars](https://lemma.frame00.com/pillars), the ones that matter here are [Pillar 01 · Verifiable Origin](https://lemma.frame00.com/pillars/verifiable-origin/) and [Pillar 03 · Agent Authority Proof](https://lemma.frame00.com/pillars/agent-authority-proof/).

## ▸ Lemma's strengths (three points)

- **Stands alongside the standards (interoperability)**: In addition to the W3C Verifiable Credentials that AP2 / Verifiable Intent rely on, it is designed to work alongside **MCP, A2A, x402, and C2PA**. It does not replace the consent layer; it meshes a provenance layer on top of it.
- **Verify without revealing (prove ≠ reveal)**: The original text is never handed over, and Lemma never sees the plaintext. All it receives is a proof that "the grounding can be traced." This is not probabilistic detection but deterministic proof.
- **Model-independent**: Models change. Proofs remain. The verification layer is kept independent of the model, so even when models are swapped out, the grounding remains.

## ▸ Raising the baseline of "trust" toward agents

The scope you can delegate to an AI agent is determined by "how far you can trust that agent." Lemma's [Agent Authority Proof (Pillar 03)](https://lemma.frame00.com/pillars/agent-authority-proof/) makes both an agent's authority and the grounding (provenance) of its judgments verifiable.

You confirm "whose agent, and with what authority," and you can trace after the fact "on what basis it acted." This is not about counting threats; it is about raising the baseline of trust so you can confidently put agents into real-world use. In an era where intent is proven, you can also layer on the soundness of the actions taken on the basis of that intent.

## ▸ Where it pays off (use cases)

Provenance verification fits right on top of work you already do.

- **Finance / FinTech**: Complete KYC/AML with only a proof that "the conditions are met," without handing over documents. You can confirm in milliseconds whether the counterparty in an agent payment is "an AML-screened organization," and lower the cost of explaining credit and screening decisions. [→ KYC/AML selective disclosure](https://lemma.frame00.com/solutions/use-cases/kyc-aml-selective-disclosure/)
- **Audit / Compliance**: Keep a record of "what the AI based its judgment on" in a form that can later be reproduced and submitted. What you can present for audits and regulatory reporting is not a detection score but a record established in advance. [→ AI audit-log proof](https://lemma.frame00.com/solutions/use-cases/ai-audit-log-proof/)
- **Manufacturing / Critical infrastructure / Procurement**: Confirm quality and inspection records, and supplier credentials, without holding the originals. You can verify supplier certification and creditworthiness without taking custody of the data. [→ Supply-chain ESG](https://lemma.frame00.com/solutions/use-cases/supply-chain-esg/)
- **Government / Public sector**: Advance procedures by verifying only the necessary attributes, without seeing personal data. You can run benefits, counter services, collections, and the like without retaining raw personal information.
- **AI-agent operations (cross-industry)**: Verify a connected agent's authority, policy, and provenance not just once at connection time but per task. You avoid "authority granted and then forgotten."

See all use cases across industries and tasks [here](https://lemma.frame00.com/solutions/use-cases).

## ▸ Developers can try it in a few lines

Verifiable AI can be started without replacing your foundation.

- **[Seal](https://lemma.frame00.com/seal/)**: A ZK sign-in SDK that sends a proof rather than a key. It embeds into an app or AI-agent stack in a few lines.
- **[Trust402](https://lemma.frame00.com/trust402/)**: Keyless authorization that runs AI without holding a key. You can use per-task delegation of authority in combination with x402 / MCP.
- Start with the [Dashboard](https://dashboard.lemma.workers.dev) and the [specifications](https://lemma.frame00.com/guides).

---

### ▸ Confirm it with the demo: can your agent's "grounding" be traced?

The consent layer is handled by the standards. **The provenance layer can be confirmed with a demo that runs today.**

- ▶ **See the provenance-proof demo**: [demo.lemma.frame00.com](https://demo.lemma.frame00.com)
- ▶ **Discuss it for your own use case (30 minutes, no disclosure of sensitive data required)**: [Request a demo / Discovery Call](https://tally.so/r/Pd2Rl5?utm_source=blog&utm_medium=cta_mid&utm_campaign=verifiable_intent)

In an era where intent is proven, let's design together a way to make the grounding of those judgments verifiable too.

---

## ▸ Where we're headed

- Design the connection point between the consent layer (Verifiable Intent / AP2) and the provenance layer (provenance proof), and provide a reference implementation for interoperability.
- Provide agent provenance ([Agent Authority Proof / Pillar 03](https://lemma.frame00.com/pillars/agent-authority-proof/)) in a verifiable form.
- Connect the convergence point of regulatory requirements (such as logging obligations for high-risk AI) and industry standards as a Regulatory Attribute Proof (Pillar 04).
- Publish cross-industry (manufacturing, critical infrastructure, finance, and more) provenance-proof use cases as demos, one after another.

Enterprise offerings are provided through custom deployment. For a verifiable-AI configuration tailored to your requirements, please get in touch via a demo request / Discovery Call.

## ▸ FAQ

**Q. What is the difference between Verifiable Intent and AP2?**
AP2 (Agent Payments Protocol) defines "how intent is created and shared" as three signed Mandates. Verifiable Intent defines "how that intent is proven" as a tamper-resistant record bundling the user's identity, instruction, and outcome. The two are complementary, and both were contributed to the FIDO Alliance in April 2026.

**Q. Once "proof of intent" is in place, can an AI agent's judgments be trusted?**
The authenticity of intent (who authorized what) can be proven. On the other hand, **whether the grounding (provenance) of the facts an agent referenced and the model output can be traced** is a separate layer. Only by layering both does it become easier to put judgments into real-world use.

**Q. What, specifically, does verifiable AI (AI provenance proof) prove?**
Not the "semantic correctness" of the output itself, but **what the output was grounded in — its provenance** — verified cryptographically. With primitives such as Groth16 / Circom / Poseidon over BN254, Lemma demonstrates that "the grounding can be traced" without disclosing the original text.

**Q. How do I try it?**
You can confirm provenance proof in the [demo environment](https://demo.lemma.frame00.com). For verification aligned to your own use case, start from a [demo request / Discovery Call](https://tally.so/r/Pd2Rl5?utm_source=blog&utm_medium=faq&utm_campaign=verifiable_intent) (no disclosure of sensitive data required).

## ▸ Let's design it together (CTA)

For x402 builders, MCP developers, teams operating AI agents, and enterprises building trust workflows that span systems. Precisely because we are in an era where intent is proven, there is value in layering on a **layer that verifies the grounding of the judgments** made on the basis of that intent.

- ▶ **Request a demo / Discovery Call (30 minutes)**: [here](https://tally.so/r/Pd2Rl5?utm_source=blog&utm_medium=cta_tail&utm_campaign=verifiable_intent)
- ▶ **See the provenance-proof demo**: [demo.lemma.frame00.com](https://demo.lemma.frame00.com)

**Built for decisions that matter.**

## ▸ Related links

- [Cryptographic chains of trust between agents: how A2A interoperability transforms the API economy](https://lemma.frame00.com/blog/agent-cryptographic-trust-chain-a2a-api-economy/)
- [A trust layer for x402](https://lemma.frame00.com/blog/x402-trust-layer-for-autonomous-agent-payments)
- [Verifiable AI: a new RAG design that proves the source of trust with cryptography](https://lemma.frame00.com/blog/verifiable-ai-cryptographic-rag-design)
- [Lemma's four pillars](https://lemma.frame00.com/pillars) / [Critical Brief (structural analysis of real cases)](https://lemma.frame00.com/critical/briefs)

— The Lemma team
