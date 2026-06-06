---
title: "Prove agent-to-agent transactions with delegation chains."
abstract: "In an Agent2Agent (A2A) environment where autonomous AI agents transact with each other, record each agent's delegation as a chain. Lemma makes it verifiable whether the final transaction rests on a legitimate chain of delegations."
thesis: "A transaction settled ≠ a chain of delegations made it legitimate"
pillar: agent-authority-proof
industries:
  - dev
  - fin
cardSummary: "Record agent-to-agent delegations as a chain, making it verifiable whether the final transaction rests on a legitimate chain of authority."
targetVerticals:
  - Developers
  - AI adoption (cross-industry)
tags:
  - delegated-authority
  - delegation-chain
  - agent-to-agent
  - settlement
relatedUseCases:
  - multi-agent-workflows
  - delegated-treasury
  - x402-commerce
---

# Use Case: Prove agent-to-agent transactions with delegation chains.

## Thesis

**A transaction settled ≠ a chain of delegations made it legitimate.**

In an A2A environment where autonomous agents transact with each other, several delegations sit behind a single transaction. You need to trace, as a chain, which organization delegated to which agent, and how far, behind the final payment.

## What Lemma Proves

- The chain of delegation from the root org to each agent (chain)
- Each agent's role and scope
- Validity period and revocation status
- That the final transaction stays within the chain's scope

## Problem

In A2A transactions, the chain of authority is typically handled in one of two ways:

1. **Delegation stays implicit** — which agent acts under whose authority is unclear without reconciling logs. A third party can't independently verify the transaction's legitimacy.
2. **Confined to a platform** — delegation is managed only inside one platform. It can't be carried across platforms or to an external audit.

Neither leaves proof that the final transaction rested on a legitimate chain of delegations.

## How Lemma Changes It

Lemma gives A2A transactions a **traceable chain of delegation**:

1. **At delegation:** hierarchical delegations — root org → agent → sub-agent — are each issued as a signed proof.
2. **At transaction:** before the transaction, the delegation chain is verified to be in scope at runtime. If the chain is broken, the transaction does not settle.
3. **At audit:** the chain can be traced from the final transaction back to the originating org. Without disclosing the delegation relationships or transaction contents, you can show "who delegated to whom" and the transaction's legitimacy.

The chain of delegation is the proof; each step's scope is the contract. A transaction whose chain can't be traced does not settle.
