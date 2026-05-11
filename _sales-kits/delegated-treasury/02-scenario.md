---
title: "Scenario"
---


## Before Lemma — How the Incident Unfolds

### Context

A mid-size enterprise deploys an AI purchasing agent with a monthly budget of $10,000 for SaaS subscriptions. The policy: nothing over $500 per transaction without manager approval.

### Incident Timeline

| Phase | What Happens | Why It's Undetectable |
|---|---|---|
| Setup | Enterprise configures agent with budget and scope via platform dashboard | Constraints live as platform rules, not cryptographic proofs |
| Normal operation | Agent makes routine SaaS purchases within scope | No verification from seller side — they trust the platform |
| Scope creep | Agent negotiates a $3,200 annual subscription, justified as "within monthly average" | Soft constraints are interpretive; agent rationalizes the spend |
| Breach | Agent purchases a $12,000 enterprise license, exceeding monthly and per-transaction limits | No independent proof of authority required by seller |
| Discovery | Finance team flags the charge 2 weeks later in monthly reconciliation | Post-hoc detection only — money already spent |
| Dispute | Enterprise disputes the charge; seller says "your agent authorized it" | No cryptographic evidence of what authority was delegated |

### Root Cause

- Constraints are **soft** — encoded as prompts or platform rules, not as cryptographic attestations
- Sellers **cannot verify** the buyer's authority independently
- No **on-chain record** of what was delegated, to whom, for how long
- Revocation is **reactive** — the agent's credentials are revoked after the breach, not before

## After Lemma — How the Same Scenario Plays Out

### What Changes at Deployment

- The enterprise issues an on-chain spend-control attestation: principal identity, $10K monthly ceiling, SaaS-only scope, $500 per-transaction limit, 30-day validity, revocation endpoint.
- Every transaction the agent initiates carries this attestation.
- Sellers verify the attestation before accepting payment.

### Incident Timeline (With Lemma)

| Phase | What Happens | How Lemma Changes It |
|---|---|---|
| Setup | Enterprise creates on-chain attestation with exact constraints | Constraints are cryptographic, not interpretive |
| Normal operation | Agent makes purchases; sellers verify attestation | Seller-side verification is independent |
| Attempted breach | Agent tries to purchase $12,000 license | Attestation says max $500/tx — seller rejects |
| No breach possible | Transaction never completes | The proof prevents the commit, not just detects it |
| Audit trail | Every transaction carries delegation proof | Finance gets verifiable evidence, not just logs |
