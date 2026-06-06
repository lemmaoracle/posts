---
title: "Delegate procurement to AI agents."
abstract: "You want an AI agent to handle routine ordering. With Lemma, \"spend ceiling,\" \"budget category\" and \"approved vendors\" become a scoped delegation — inside scope the agent orders autonomously, outside it stops."
thesis: "Hand over scope, not the whole decision"
pillar: agent-authority-proof
industries:
  - mfg
  - dev
cardSummary: "Issue ordering authority as a delegation scoped by spend ceiling, category and approved vendors — and let the AI order autonomously only inside that scope."
targetVerticals:
  - Manufacturing / critical infra
  - Procurement / supply chain
tags:
  - spend-control
  - delegated-authority
  - procurement
  - enterprise
relatedUseCases:
  - agent-expense-approval
  - supplier-credential-verification
  - supply-chain-component-provenance
---

# Use Case: Delegate procurement to AI agents.

## Thesis

**Hand over scope, not the whole decision.**

You want to delegate routine ordering to an AI agent. But you can't hand vendor selection over wholesale. "Approved vendors only," "category: supplies," "up to ¥500k per order" must be fixed as a verifiable delegation.

## What Lemma Proves

- The delegating principal (procurement / purchasing) and the ordering agent
- The order spend ceiling (spendLimitUSDC)
- The approved-vendor and category scope (scope)
- Validity period and revocation status

## Problem

When ordering is automated, the constraints are typically enforced in one of two ways:

1. **Soft prompts** — the agent is told "only order from approved vendors." Adversarial input or context overflow drops the rule, and an unexpected vendor or amount slips through.
2. **Rules inside the purchasing system** — a ceiling is embedded in the core system. But auditors can't later verify, independently, which delegation a given order rested on.

Neither leaves proof that an AI order stayed within an authorized delegation.

## How Lemma Changes It

Lemma gives the ordering agent **scoped, verifiable authority**:

1. **At delegation:** procurement issues a signed delegation carrying a spend ceiling, approved vendors, categories and validity.
2. **At ordering:** the agent verifies the scope at runtime before confirming an order. Inside scope it orders autonomously; outside it stops.
3. **At audit:** every order carries proof of the delegation that authorized it. Without disclosing supplier data or budgets, you can show it "ordered within an authorized delegation."

The delegation is the proof; the scope is the contract. Out-of-scope orders stop before they confirm.
