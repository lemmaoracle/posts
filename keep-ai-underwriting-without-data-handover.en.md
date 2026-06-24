---
slug: "keep-ai-underwriting-without-data-handover"
date: "2026.05.28"
category: "Solutions"
section: "Essays"
title: "Underwriting without the handover: keep AI credit decisions without holding the data"
cover: "assets/cover-solutions.png"
abstract: "How to keep AI underwriting running without handing the raw data to the AI. We compare masking, selective disclosure, and ZK proofs, walk through how responsibility and assurance shift for five stakeholders — applicants, loan officers, compliance leads, executives, and regulators — and show the verification flow you can experience in Lemma's demo."
tags:
  - zk-proof
  - financial-services
  - lending
  - privacy-preserving-ai
  - selective-disclosure
  - compliance
---

**TL;DR** — In loan underwriting today, identity documents, income statements, AML hits, and credit-bureau records all flow as raw data from the applicant to the bank, and onward into AI credit-scoring and document-analysis engines. Masking is an operation that hides values, but since the AI has to reconstruct the original to decide, the AI ends up seeing the content anyway.

Lemma is the trust foundation for the AI era — a way to make a loan underwriting decision without handing the applicant's personal data to AI. Inside the applicant's browser, only "the conditions are met" is proven as fact, and what reaches the AI is not raw data but a set of verifiable facts. You can keep running the underwriting you do today, without holding the data.

**No handover, no leak.**

**Hand it to AI, and the evidence still stays.**

**Cryptographically verifiable ≠ disclosed.**

## ▸ What's actually happening in loan underwriting today

Loan underwriting is one of the business domains that moves the largest volumes of raw personal data. The applicant submits ID, residence certificate, withholding statement, employment verification, and tax filings; the bank transcribes those into internal systems, credit-decision models, AML check APIs, and credit-bureau queries. Duplicate copies persist across multiple systems, and the responsibility for retention windows and deletion cuts across organizational lines.

In recent years, AI-driven automation has joined the workflow. OCR reads the documents; an AI credit-scoring model derives a repayment probability from income and attribute data; an AI document-analysis engine judges identity consistency. What gets fed into these AIs is, almost without exception, **raw data**. A masking layer is sometimes inserted in front, but since the decision model has to reconstruct the original, masking is ultimately a temporary screen.

The EU AI Act puts lending decisions under its high-risk AI classification, and in Japan the FSA's AI governance review is ongoing. Voices asking "how is AI handling my data?" have grown louder. Instead of layering on more masking, the question of whether a design where **the AI never touches the original data** is feasible is starting to surface as a practical option.

## ▸ Masking vs. selective disclosure vs. ZK proofs

Three distinct approaches; first, draw the line clearly between them.

| Approach | Where the original data lives | What the AI / receiver sees | Risk if leaked |
|---|---|---|---|
| Masking | Copied to the receiver | Masked raw data (must be reconstructed at decision time) | Reconstructed / leaks along the path before reconstruction |
| Selective disclosure | At the issuer | Only the necessary attributes (issuer-signed) | The disclosed attributes themselves can leak |
| ZK proof | Inside the applicant's browser | Only facts about the attributes (attribute values are invisible) | The leak-able attribute values don't exist in the first place |

Masking is an operation that *controls the visibility of data*. Selective disclosure is an operation that *controls the disclosure scope of data*. ZK proofs are an operation that *proves a fact without disclosing the data* — a fundamentally different premise.

ZK proofs are backed by proof schemes like Groth16, ZK-friendly hash functions like Poseidon over BN254, and selectively-disclosable signature schemes like BBS+ over BLS12-381. Compose these and you can hand over only **boolean facts** — "the applicant has an annual income of at least X yen," "the applicant is not present in the AML database" — without anything ever leaving the applicant's device.

## ▸ The underwriting data flow, before / after

Today's underwriting data flow is a one-way transfer of raw data from applicant to bank, plus internal replication across multiple downstream paths. Document bundle → intake system → credit-decision model / AML API / credit-bureau query / AI scoring: the applicant's personal data is transcribed into at least four to five systems.

In a ZK-proof-based design, attribute proofs are generated inside the applicant's browser, and what reaches the bank is only the proof and the public signals. The AI scoring model receives only the facts — "income condition met," "residence condition met," "not present in the AML database," "credit score above threshold" — and never touches the raw data. The underwriting decision itself is also recorded as a ZK proof, so a later audit can cryptographically verify that "this applicant was judged to meet the specified conditions."

## ▸ Who changes how — what assurance means, by stakeholder

When this configuration holds, the responsibilities and anxieties of the people involved in loan underwriting shift in distinct ways.

**For the applicant**, the worry about where their income, AML, and credit-bureau records end up inside AI disappears. The concern about personal data being absorbed into AI training data is gone; even if an incident occurs at the bank or an AI vendor, there is no underlying raw data to leak, so harm cannot materialize. What gets submitted is only "proof that the conditions are met" — the substance of what was met never leaves the applicant's hands.

**For the loan officer**, the responsibility for opening the document bundle, handing it to AI, and managing retention windows disappears. Their underwriting decision is preserved as a ZK proof that can be verified later; when asked to explain years afterward in an audit, the basis is backed cryptographically. "Why did I make that call then?" — memory-based explanation gives way to a verifiable record.

**For the compliance lead**, the scope of AI-related personal-data-protection compliance shrinks fundamentally. The intractable problem of "what data did we feed the AI?" structurally stops occurring. Explainable-management requirements are met mechanically by cryptographic proofs; report-based explanation gives way to a verifiable record.

**For executives**, AI-originated leak incidents become structurally impossible by design. A shift from a reactive post-incident posture to a proactive one that eliminates leak risk structurally at the design stage. The total cost of reputation, regulatory, and litigation response goes down.

**For regulators**, whether AI is judging properly can be checked mechanically against verifiable ZK proofs. Instead of decoding the inside of the AI black box, a new supervisory model holds: cryptographically verifying the fact that "the AI judged this attribute condition to be met."

## ▸ What gets streamlined and automated

Beyond the change in how assurance is structured, business efficiency moves concretely too. Document-bundle copy-transcription disappears, and the scope of personal-data-protection compliance shrinks. Minimum disclosure of AML and credit-bureau data lowers the legal risk tied to unnecessary information contact. Detection of tampered applications stops being a matter of human eyeballing or multi-path reconciliation and becomes automated via cryptographic verification.

And most importantly, because the trail of the AI's decision itself is preserved as a ZK proof, accountability for AI decision-making is supported mechanically by cryptographic proof. The long-running "how do you show AI's decision basis?" question of explainable management is answered in the direction of making *the fact of the decision* verifiable without disclosing the data.

**Models change. Proofs remain.**

## ▸ Try the demo

The actual verification flow for loan underwriting is available in the Lemma demo. Under the Finance scenario at `demo-lemma.frame00.com`, two samples — "Loan approval (valid)" and "Loan approval (tampered)" — let you see, in-browser, the valid case verifying as a clean ZK proof and the tampered case being cryptographically detected and rejected. As the page footer notes, "the file never leaves your device" — the verification runs inside the applicant's browser, exactly as advertised.

## ▸ For builders / lenders

Lemma is at the PoC stage, grounded in a reference architecture and a verifiable demo.

For lenders, financial-infrastructure integrators, or compliance leads exploring privacy-preserving credit underwriting — taking AI deeper into underwriting while looking for a design where AI doesn't hold personal data; building or integrating credit-decision models, LOS / CRM stacks; or standing up AI governance in the explainable-management context — we'd love to hear from you.

- Enterprise discovery (whitepaper / PoC discussion): [tally.so/r/Pd2Rl5](https://tally.so/r/Pd2Rl5)
- Developer waitlist (Lemma API): [tally.so/r/kd0bZR](https://tally.so/r/kd0bZR)

**Built for decisions that matter.**

## ▸ Resources

- Demo: [demo-lemma.frame00.com](https://demo-lemma.frame00.com/) (Finance / Loan approval)
- Whitepaper: [Prove What Your AI Decided On.](https://lemma.frame00.com/blog/whitepaper-v1-prove-ai-decisions)
- Related: [AI agents in financial operations: the case for the judgment-trail layer](https://lemma.frame00.com/blog/verifiable-ai-financial-agents-2026)
- Related: [Verifiable AI KYC/AML Without Data Sharing: Lemma's Practical Approach](https://lemma.frame00.com/blog/lemma-verifiable-ai-kycaml-without-data-sharing)
- Related: ["Explainable Management" Powered by Cryptographic Proofs](https://lemma.frame00.com/blog/ai-explainability-management-crypto-proof)
