---
title: "Detect prompt injection without exposing content."
abstract: "Prompt injection uses invisible Unicode and hidden commands to make \"what the human saw\" diverge from \"what the AI reads.\" Lemma hashes the normalized form of the input and verifies, at runtime, the visible_eq between the input the human intended and the input the AI received — detecting tampering without disclosing the content."
thesis: "What the AI read ≠ what the human saw"
pillar: verifiable-ai
industries:
  - ai
  - dev
cardSummary: "Commit the normalized form of the input and verify \"human-saw = AI-read\" integrity — without exposing the content."
targetVerticals:
  - AI adoption (cross-industry)
  - Security
tags:
  - prompt-injection
  - input-integrity
  - ai-security
  - verifiable-ai
relatedUseCases:
  - ai-document-isolation
  - ai-audit-log-proof
  - model-version-attestation
---

# Use Case: Detect prompt injection without exposing content.

## Thesis

**What the AI read ≠ what the human saw.**

Invisible Unicode and hidden commands make "the input the human saw on screen" diverge from "the input the AI actually reads." That divergence is the essence of prompt injection. You need to verify the two match — without inspecting the content itself.

## What Lemma Proves

- A commitment to the normalized form of the input
- The integrity that "what the human saw = what the AI reads" (visibleEq)
- Compliance with the applied policy (satisfiesPolicy)
- A halt on execution when tampering is detected

## Problem

Defenses against prompt injection are typically implemented in one of two ways:

1. **Input filters** — block known attack patterns. But invisible characters and novel encodings slip through, and an input that "looks normal" arrives at the AI as something else.
2. **Output monitoring** — detect a bad output after the fact. But the attack has already executed, and the input tampering itself can't be proven.

Neither structurally guarantees that the input the AI processed matched what the user intended.

## How Lemma Changes It

Lemma inserts **input-integrity verification** ahead of AI inference:

1. **Normalize:** the input is converted to a normalized form (Unicode NFC, with whitespace and invisible-character handling defined) and its fingerprint is committed.
2. **Before inference:** the visibleEq between "what the human intended" and "what the AI receives" is verified at runtime. If they differ, inference stops before it runs.
3. **At audit:** every inference carries proof that the input was untampered. Without disclosing the input content, the integrity can be independently verified.

Input integrity is the proof; visibleEq is the boundary. A diverging input stops before it reaches the AI.
