---
title: "Delegate expense approval to AI agents."
abstract: "You want to automate expense approvals, but you can't hand an AI unlimited authority. With Lemma, \"limit ¥100k,\" \"budget category\" and \"team scope\" become a scoped delegation verified at runtime — inside scope the agent approves autonomously, outside it escalates to a human."
thesis: "Authorized ≠ scope-proven"
pillar: agent-authority-proof
industries:
  - fin
cardSummary: "Issue expense-approval authority as a delegation with a spend ceiling, categories and validity — and let the AI approve autonomously only inside that scope."
targetVerticals:
  - Finance / FinTech
  - All industries
  - Accounting / Finance
tags:
  - spend-control
  - delegated-authority
  - expense-approval
  - enterprise
relatedUseCases:
  - delegated-treasury
  - multi-agent-workflows
  - agent-procurement
---

# Use Case: Delegate expense approval to AI agents.

## Thesis

**Authorized ≠ scope-proven.**

You want to delegate expense approval to an AI agent. But constraints like "up to ¥100k," "travel and supplies only," "members of this team only" are not guarantees when written as prompts. The scope needs to be fixed outside the model, as a verifiable delegation.

## What Lemma Proves

- The delegating principal (org / department) and the delegated agent
- The approval spend ceiling (spendLimitUSDC)
- The category and team scope (scope)
- Validity period and revocation status

## Problem

When expense approval is automated, the constraints are typically enforced in one of two ways:

1. **Soft prompts** — the agent is told "don't approve over ¥100k." Prompt injection or context overflow drops the rule, and an over-limit approval slips through.
2. **Rules inside the approval system** — a ceiling is embedded in the central workflow. But auditors can't later verify, independently, which delegation a given approval rested on.

Neither leaves cryptographic proof that an AI approval stayed within an authorized delegation. Without that proof, internal control and audit have only logs to trust.

## How Lemma Changes It

Lemma gives the approval agent **scoped, verifiable authority**:

1. **At delegation:** the finance team issues a delegation carrying a spend ceiling, categories, team scope and validity — signed by the issuing org, not the agent or the platform.
2. **At approval:** the agent verifies the scope at runtime before approving. Inside scope it approves autonomously; outside it escalates to a human and stops.
3. **At audit:** every approval carries proof of the delegation that authorized it. Without disclosing the approval authority itself, you can show it "approved within an authorized delegation."

The delegation is the proof; the scope is the contract. Out-of-scope approvals stop before they happen.
