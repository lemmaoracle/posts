---
title: "Delegate API billing to AI agents."
abstract: "You want an AI agent to call external APIs (SaaS, payments, data), without the risk of handing it the API key. With Lemma, \"billing ceiling,\" \"allowed APIs\" and \"validity\" become a scoped delegation — via x402, inside scope the agent runs autonomously, outside it stops."
thesis: "Handing over the key ≠ handing over scope"
pillar: agent-authority-proof
industries:
  - dev
cardSummary: "Without handing over the API key, verify a billing-ceiling / allowed-API / validity delegation at runtime — and let the AI call only inside that scope."
targetVerticals:
  - Developers
  - All industries
  - IT / DevOps
tags:
  - spend-control
  - delegated-authority
  - api-billing
  - x402
relatedUseCases:
  - delegated-treasury
  - multi-agent-workflows
  - x402-commerce
---

# Use Case: Delegate API billing to AI agents.

## Thesis

**Handing over the key ≠ handing over scope.**

You want an AI agent to call external APIs. But handing it the raw API key constrains nothing — not the billing ceiling, not the callee, not the validity. "Up to $100 a month," "this endpoint only," "until month-end" must be fixed as a verifiable delegation.

## What Lemma Proves

- The delegating principal (org / developer) and the calling agent
- The billing ceiling (spendLimitUSDC)
- The allowed API-endpoint scope (scope)
- Validity period and revocation status

## Problem

When an agent is allowed to call APIs, the constraints are typically enforced in one of two ways:

1. **Delegating the API key directly** — if the key leaks, neither ceiling nor callee is bounded. Leakage can't be prevented structurally.
2. **App-side rate limits** — a self-built wrapper enforces a ceiling. But a third party has no way to independently verify the callee or the legitimacy of the billing.

Neither leaves proof that an agent's API call stayed within an authorized delegation.

## How Lemma Changes It

Lemma gives the calling agent **scoped, verifiable authority**:

1. **At delegation:** an org or developer issues a signed delegation carrying a billing ceiling, allowed APIs and validity — without handing the agent the API key itself.
2. **At call time:** x402 middleware checks authority via Trust402 before each call. Inside scope it runs autonomously; outside it stops before execution.
3. **At audit:** every call carries proof of the delegation that authorized it. Without disclosing keys or billing authority, you can show it "called within scope."

The delegation is the proof; the scope is the contract. The risk of API-key leakage is structurally removed.
