---
title: "X402 Commerce"
abstract: "Verify seller attributes as ZK proofs before x402 settlement runs. Buyer agents transact without relying on plaintext claims."
thesis: "Paid ≠ verified"
pillar: verifiable-origin
industries:
  - dev
cardSummary: "Verify a seller's attributes before payment — no reliance on plaintext claims."
targetVerticals:
  - x402 protocol participants
  - MCP-enabled agent platforms
  - Digital commerce infrastructure
tags:
  - x402
  - agent-commerce
  - micropayment
  - ZK-proof
relatedUseCases:
  - delegated-treasury
  - multi-agent-workflows
---

# Use Case: X402 Commerce

## Thesis

**Settles ≠ verified.**

On the x402 rail, buyer and seller agents meet for the first time and complete a micropayment in milliseconds. Lemma wraps each seller-stated attribute — price, inventory, SLA, identity — in a ZK proof. The buyer agent never trusts plain text claims; it verifies cryptographically before it pays.

## What Lemma Proves

- Seller identity and role
- Stated inventory and price
- Service-level commitments
- Issuer signature provenance

## Problem

In agent-to-agent commerce, transactions settle in milliseconds — but verification is either absent or outsourced to reputation systems. A seller agent claims "I have inventory, my price is X, I will deliver within SLA Y." The buyer agent pays. If any of those claims were false, the payment already happened.

Reputation systems penalize after the fact. They don't prevent the commit. And in a world of ephemeral agents — spun up for one transaction, then discarded — reputation is meaningless. The seller agent you transacted with may not exist by the time you discover the lie.

## How Lemma Changes It

Lemma provides **pre-transaction verification** for agent commerce:

1. **At listing:** The seller agent's attributes (identity, role, inventory, price, SLA) are wrapped in ZK proofs issued by a verifiable authority.
2. **At negotiation:** The buyer agent verifies proofs before committing to the transaction. No proof = no payment.
3. **At settlement:** Both parties hold cryptographic evidence of what was claimed and what was verified.

The x402 protocol handles the payment rail. Lemma handles the trust rail.