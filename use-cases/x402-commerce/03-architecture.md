---
title: "Architecture"
---


## Design Principle

**Lemma provides cryptographic proof of seller claims before settlement, turning trust into verification.**

Existing reputation systems and payment rails handle transaction execution. Lemma adds a layer of verifiable attestations about inventory, price, SLA, and issuer provenance, enabling buyers to verify before committing funds.

```
┌─────────────────────────────────────────────────────────────┐
│                   x402 Commerce Network                      │
│                                                              │
│  ┌────────────┐     ┌──────────────┐     ┌──────────────┐   │
│  │   Seller   │────▶│   Lemma      │────▶│   x402       │   │
│  │   Agent    │     │  Attestation │     │   Payment    │   │
│  │            │     │  Gateway     │     │   Rail       │   │
│  └────────────┘     └──────┬───────┘     └──────┬───────┘   │
│                             │                     │          │
│                             ▼                     ▼          │
│                     ┌──────────────┐     ┌──────────────┐   │
│                     │  Commitment  │     │   Buyer      │   │
│                     │  Root /      │     │   Agent      │   │
│                     │  On-chain    │     │   (verifier) │   │
│                     │  Anchor      │     │              │   │
│                     └──────────────┘     └──────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

## Component Breakdown

### 1. Lemma Attestation Gateway

Sits between the seller agent and the x402 payment rail. At listing time, it:

- Wraps seller claims (inventory, price, SLA, issuer signature) into a **ZK attestation**
- Aggregates the attestation into a local commitment tree
- Periodically anchors the commitment root on-chain
- Does **not** require disclosure of sensitive business data (privacy-preserving)

### 2. Seller Agent (with Lemma)

- Publishes a listing that includes the Lemma attestation
- The attestation is signed by the issuer (e.g., software vendor, inventory custodian)
- Seller can prove inventory ownership and SLA commitments without revealing underlying data

### 3. x402 Payment Rail (Existing)

- Executes micropayments between agents
- **New integration:** Can condition settlement on verification of Lemma attestations
- Supports atomic swaps where payment is released only after attestation validation

### 4. Buyer Agent (Verifier)

- During negotiation, requests Lemma attestation from seller
- Verifies the ZK proof against the on‑chain commitment root
- Confirms that the attestation is signed by a trusted issuer
- Proceeds with settlement only after successful verification

### 5. Commitment Root / On-chain Anchor

- Commitment root of all attestations, anchored at regular intervals
- Enables any buyer (or marketplace) to verify that a seller's claims were attested at a specific time and have not been altered
- No sensitive inventory data on-chain — only commitments

## Data Flow: Listing Creation

```
Seller Agent → Creates listing (inventory=100, price=$5, SLA=2s)
         │
         ▼
   ┌─────────────────────┐
   │ Attestation Gateway │
   │ 1. Wrap claims      │
   │ 2. Generate ZK att. │
   │ 3. Commit to tree   │
   │ 4. Return attest.   │──▶ Listing published with Lemma proof
   └─────────────────────┘
         │
         ▼
   Attestation stored:
   {
     issuer_sig: "0x123...",
     inventory_hash: "0xabc...",
     price: "$5",
     sla_hash: "0xdef...",
     timestamp: 1714982400,
     merkle_index: 47,
     proof: "0x..."
   }
```

## Data Flow: Purchase Negotiation

```
Buyer Agent → Requests proof of inventory/SLA
         │
         ▼
   Seller provides Lemma attestation
         │
         ▼
   Buyer verifies:
   1. ZK proof validity
   2. Attestation matches on‑chain root
   3. Issuer signature is trusted
         │
         ▼
   If verification passes → proceed to settlement
   If verification fails → reject listing, report fraud
```

## Data Flow: Settlement with Verification

```
Buyer Agent → Initiates x402 payment with attestation condition
         │
         ▼
   x402 rail checks Lemma verification result
         │
         ▼
   If verified → atomic swap executes, funds transferred
   If not verified → payment aborted, buyer retains funds
```

## Integration Points

### With Existing Reputation Systems

- Lemma does **not** replace reputation; it complements it by adding cryptographic certainty to specific claims
- Reputation scores can be weighted by the presence of Lemma attestations (higher trust for attested listings)

### With Marketplaces / Aggregators

- Marketplaces can filter listings based on Lemma attestation status
- They can offer "verified" badges for attested sellers, reducing fraud and increasing buyer confidence

### With Issuer Ecosystems

- Software vendors, digital asset custodians, and service providers can issue signed attestations for their products
- Creates a web of trust where buyers can verify provenance directly