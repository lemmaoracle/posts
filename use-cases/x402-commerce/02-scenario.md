---
title: "Scenario"
---

# Scenario: x402 Commerce with False Inventory Claims

## Before Lemma — How the Incident Unfolds

### Context

An autonomous buyer agent needs to purchase 100 licenses for a software product. It discovers a seller agent advertising the licenses at a competitive price on the x402 payment rail. The seller agent:

1. Claims to hold 100 valid licenses in its inventory
2. Provides a price quote and SLA (delivery within 2 seconds)
3. Appears to have a high reputation score from recent transactions

The buyer agent has no way to cryptographically verify the seller's inventory or SLA commitments before committing funds.

### Incident Timeline

| Phase | What Happens | Why It's Undetectable |
|---|---|---|
| Listing | Seller advertises 100 licenses at $5 each | No proof of inventory required; reputation system relies on past transactions |
| Negotiation | Buyer agrees to purchase all 100 licenses | Buyer cannot verify seller's claims; must trust reputation and public history |
| Settlement | Buyer sends 500 USD over x402 rail | Settlement is atomic; funds are transferred before delivery verification |
| Delivery | Seller sends invalid license keys (or none) | No cryptographic link between settlement and delivery; buyer cannot prove breach |
| Dispute | Buyer reports fraud, but seller agent has already shut down | Ephemeral agent identity disappears; reputation system cannot penalize nonexistent entity |
| Loss | Buyer loses $500 with no recourse | No tamper-evident proof of seller's false claims; dispute resolution relies on mutable logs |

### Root Cause Analysis

1. **Settlement before verification** — x402 rail ensures payment finality but does not attest to seller's ability to deliver
2. **Ephemeral agent identities** — Reputation systems fail when agents can be created and destroyed faster than feedback cycles
3. **No cryptographic attestation of claims** — Seller's inventory, price, and SLA statements are not bound to a verifiable proof
4. **Trust based on mutable reputation** — Reputation scores can be manipulated or are irrelevant for new/transient agents

---

## After Lemma — How the Same Scenario Plays Out

### What Changes at Deployment

1. **Lemma attestation gateway** sits between the seller agent and the x402 rail, wrapping seller claims in ZK proofs
2. Before listing, the seller must generate a **ZK attestation** of inventory, price, and SLA — signed by the issuer (e.g., software vendor)
3. The buyer verifies the attestation during negotiation, ensuring claims are cryptographically proven before settlement
4. Attestations are anchored on-chain, creating a **tamper-evident record** of the seller's commitments

### Incident Timeline (With Lemma)

| Phase | What Happens | How Lemma Changes It |
|---|---|---|
| Listing | Seller advertises 100 licenses with Lemma attestation | Buyer can verify the attestation matches issuer's signature and current validity |
| Negotiation | Buyer requests proof of inventory and SLA | Seller provides verifiable ZK proof; buyer confirms before proceeding |
| Settlement | Buyer sends payment only after proof validation | Settlement is conditional on verified claims; funds move with cryptographic guarantee |
| Delivery | Seller delivers valid license keys (attested by issuer) | Delivery is cryptographically linked to the attestation; buyer can confirm receipt |
| Dispute prevention | Seller cannot falsify inventory without detection | Lemma proof exposes any mismatch between attestation and actual inventory |
| Loss | Zero — buyer never commits to unverified claims | Tamper-evident proof prevents fraud; ephemeral agents cannot escape accountability |

### Quantified Impact

| Metric | Without Lemma | With Lemma |
|---|---|---|
| Time to detect fraudulent listing | After settlement (loss incurred) | Before settlement (during negotiation) |
| Ability to dispute false claims | Low — no cryptographic evidence | High — ZK attestation provides undeniable proof |
| Reputation system reliance | High — must trust past behavior | Low — trust shifts to cryptographic verification |
| Protection against ephemeral agents | None — reputation meaningless | Full — attestations bound to issuer, not agent identity |
| Auditability of transactions | Manual log review, mutable | Automated proof verification, tamper-evident |

---

## Key Scenarios for Sales Narrative

### Scenario A: "The High-Frequency Trading Agent"

> A trading agent needs to purchase API calls from a provider with strict latency SLAs. Without Lemma: pays for 10,000 calls, receives degraded performance, no proof of SLA violation. With Lemma: verifies SLA attestation before each payment, rejects non‑compliant providers instantly.

### Scenario B: "The Digital Goods Marketplace"

> A marketplace aggregates thousands of seller agents. Without Lemma: fraud rate escalates as ephemeral agents exploit settlement finality. With Lemma: every listing includes Lemma attestation; marketplace can filter unverified sellers, reducing fraud to near‑zero.

### Scenario C: "The Enterprise Procurement Bot"

> An enterprise procurement bot buys cloud resources from spot markets. Without Lemma: commits funds to sellers that fail to allocate resources, causing operational delays. With Lemma: verifies resource availability attestations before payment, ensuring continuous operation.