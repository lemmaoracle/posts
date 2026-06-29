---
title: "Prove AI model integrity across version changes, retroactively."
abstract: "When you update an AI model (v3.5 → v4.0, etc.), you often need to verify later whether past decisions are reproducible or whether the results change. Lemma commits each point-in-time modelId and policyHash at decision time, so past decision logic stays cryptographically traceable even after the model updates."
thesis: "The model updates ≠ past decisions become irreproducible"
pillar: verifiable-ai
industries:
  - fin
  - ai
cardSummary: "Pin the model version and policy at decision time so past AI decisions stay retroactively verifiable after a model update."
targetVerticals:
  - Finance / FinTech
  - AI adoption (cross-industry)
  - AI governance
tags:
  - model-versioning
  - ai-governance
  - reproducibility
  - verifiable-ai
relatedUseCases:
  - ai-audit-log-proof
  - prompt-injection-detection
  - internal-control-approval-proof
---

# Use Case: Prove AI model integrity across version changes, retroactively.

## Thesis

**The model updates ≠ past decisions become irreproducible.**

When you update an AI model, "which model, under which policy, decided what" becomes blurry. If you can't reproduce and verify past decisions retroactively, AI-governance accountability is left hanging. The decision point in time needs to be pinned.

## What Lemma Proves

- The decision-time model (modelId@timestamp)
- The decision-time policy (policyHash)
- The input/output commitments and compliance
- The time the decision was recorded (recordedAt)

## Problem

Integrity across model updates is typically handled in one of two ways:

1. **Logging version numbers** — record which model was used. But you can't cryptographically pin "on that version, under that policy, this decision was made for this input."
2. **Archiving the model** — keep the old model. But reproducing the runtime environment, policy and surrounding context is hard, and a third party can't verify the result independently.

Neither gives you a way to reproduce past decisions point-in-time-pinned, retroactively, and verifiably.

## How Lemma Changes It

Lemma issues a **point-in-time commitment** for each AI decision:

1. **Version pinning:** modelId@timestamp and policyHash are committed at the moment of decision.
2. **At decision time:** the input/output commitments and satisfiesPolicy are fixed at the same time. This record stays immutable even after the model updates.
3. **Retroactive verification:** even after a model update, past decisions can be verified against "that model, that policy." Without disclosing model internals or parameters, the integrity of a decision can be independently shown.

The decision point in time is the proof; modelId@timestamp is the fixed point. The model can change, but past decisions remain traceable.
