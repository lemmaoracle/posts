---
title: "Delegated Treasury"
abstract: "Issue agent spend authority as an on-chain spend-control attestation — not as a soft prompt."
thesis: "Authorized ≠ attested"
pillar: agent-authority-proof
industries:
  - fin
  - dev
cardSummary: "Issue an AI agent's spending authority as an on-chain spend-control attestation."
targetVerticals:
  - Enterprise treasury management
  - SaaS procurement
  - Agent-based purchasing
tags:
  - spend-control
  - delegated-authority
  - on-chain-attestation
  - enterprise
relatedUseCases:
  - x402-commerce
  - multi-agent-workflows
---

# Use Case: Delegated Treasury

## Thesis

**Authorized ≠ attested.**

An enterprise hands its agent a budget and a scope: $10K monthly, SaaS subscriptions only, nothing over $500 without approval. These constraints cannot live as soft prompts. Lemma encodes them as on-chain spend-control attestations, signed by the issuing organization. Counterparties verify before accepting the payment.

## What Lemma Proves

- Issuing principal (org / department)
- Spend ceiling and scope
- Time-bounded validity
- Revocation status

## Problem

When an enterprise delegates spending authority to an AI agent, the constraints are typically enforced in one of two ways:

1. **Soft prompts** — The agent is instructed "don't spend more than $500." This is a suggestion, not a guarantee. Prompt injection, context overflow, or adversarial negotiation can override it.

2. **Platform guardrails** — The agent platform enforces limits. But these are centralized, opaque, and not portable. A seller has no way to independently verify the buyer's authority without trusting the platform.

Neither approach provides cryptographic proof that the agent's spend authority is real, current, and bounded. Without that proof, sellers must either trust the agent's claim or reject the transaction — both are suboptimal.

## How Lemma Changes It

Lemma provides **verifiable spend authority** for delegated agents:

1. **At delegation:** The issuing organization creates an on-chain spend-control attestation: principal identity, spend ceiling, category scope, time validity, revocation endpoint.
2. **At transaction:** The seller agent verifies the attestation before accepting payment. The attestation is signed by the issuing org — not by the agent, not by the platform.
3. **At audit:** Every transaction carries a cryptographic proof of the delegation that authorized it. Compliance teams get verifiable evidence, not log entries.

The delegation is the proof. The attestation is the contract. The on-chain anchor is the guarantee.