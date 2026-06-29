---
title: "Agentic Payment Fraud"
abstract: "Attach a ZK proof to every AI-agent payment call — who delegated, within what scope, and up to what limit — so the receiving side can verify the delegation cryptographically before settlement, instead of trusting an API key."
thesis: "Paid ≠ delegated"
pillar: agent-authority-proof
industries:
  - fin
  - ai
cardSummary: "Bind every agent payment to a verifiable delegation, spend limit, and jurisdiction attribute — before settlement."
targetVerticals:
  - AI agent operations
  - Financial services
  - Crypto exchanges
  - API-based payment platforms
tags:
  - agentic-payments
  - x402
  - trust402
  - zk-proof
  - agent-authority
relatedUseCases:
  - delegated-treasury
  - multi-agent-workflows
  - x402-commerce
---

# Use Case: Agentic Payment Fraud

## Thesis

**Paid ≠ delegated**

The x402 rail and Stripe Agent SDK make it trivial for an AI agent to push a payment. None of them prove that the agent was actually authorized to push it — by whom, within what spending limit, against which jurisdiction. Lemma's Agent Authority Proof attaches a ZK attestation to every call, so the receiving side can verify the delegation cryptographically before settlement, instead of trusting an API key.
