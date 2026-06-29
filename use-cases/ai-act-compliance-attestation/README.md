---
title: "Prove EU AI Act compliance without sharing originals."
abstract: "Complying with the EU AI Act means proving operational accountability for a \"high-risk AI system.\" Lemma lets you prove compliance with each provision — risk assessment performed, human oversight, transparency disclosure — as attributes, so you can demonstrate it without disclosing the original data."
thesis: "Having audit logs ≠ being able to prove compliance"
pillar: verifiable-ai
industries:
  - fin
  - pub
  - ai
cardSummary: "Show a high-risk AI's AI Act compliance as verifiable proofs, without surfacing training data or model details."
targetVerticals:
  - Finance / FinTech
  - Public sector
  - All industries
  - Regulatory
tags:
  - eu-ai-act
  - ai-governance
  - compliance
  - verifiable-ai
relatedUseCases:
  - ai-audit-log-proof
  - prompt-injection-detection
  - kyc-aml-selective-disclosure
---

# Use Case: Prove EU AI Act compliance without sharing originals.

## Thesis

**Having audit logs ≠ being able to prove compliance.**

The EU AI Act requires high-risk AI systems to carry operational accountability — risk assessment, human oversight, transparency. Holding millions of log rows and a huge training set is not proof of compliance. You need to show compliance per provision, without surfacing the originals.

## What Lemma Proves

- Compliance with the applicable policy — each AI Act provision (satisfiesPolicy)
- The attributes disclosed (risk assessment performed, human oversight, transparency)
- What stays hidden (training data, model internals, customer data)
- The jurisdiction and issuing principal

## Problem

AI Act compliance is typically pursued in one of two ways:

1. **Documentation-first** — assemble model cards and risk assessments. But you can't show, continuously and verifiably, that the AI actually met the provisions at the moment of a given decision.
2. **Disclosing originals** — submit training data and model details for each audit. That carries trade-secret and customer-data leakage risk and doesn't scale to frequent verification.

Neither gives you a way to show compliance continuously and third-party-verifiable, without surfacing the originals.

## How Lemma Changes It

Lemma has the AI system issue **per-provision compliance proofs**:

1. **Policy design:** each AI Act provision is implemented as a policyHash.
2. **In operation:** every decision issues a satisfiesPolicy proof. What is disclosed is only attributes like "risk assessment done," "human oversight" and "transparency," while training data, model internals and customer data stay hidden and protected.
3. **At audit:** compliance can be presented to regulators and third-party auditors without surfacing the originals. Because it's issued continuously, you show compliance across operations, not at a single point in time.

Compliance is proven as attributes, and the originals never leave. Regulatory-breach risk is minimized technically.
