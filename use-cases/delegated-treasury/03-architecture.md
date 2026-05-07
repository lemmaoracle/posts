---
title: "Architecture"
---

# Architecture: On-Chain Spend-Control Attestations

## Design Principle

**The delegation is the proof. The attestation is the contract. The on-chain anchor is the guarantee.**

```
┌─────────────────────────────────────────────────────────────┐
│                     ENTERPRISE (Principal)                   │
│                                                              │
│  ┌──────────────────┐        ┌──────────────────────────┐   │
│  │  Delegation Policy│───────▶│  Lemma Attestation Issuer │   │
│  │  - $10K/mo ceiling│        │  - ZK proof of authority  │   │
│  │  - SaaS only      │        │  - On-chain anchor        │   │
│  │  - $500/tx max    │        │  - Revocation endpoint    │   │
│  └──────────────────┘        └────────────┬─────────────┘   │
└────────────────────────────────────────────┼─────────────────┘
                                             │ attestation
                                             ▼
┌─────────────────────────────────────────────────────────────┐
│                     AGENT (Delegate)                          │
│                                                              │
│  ┌──────────────────┐        ┌──────────────────────────┐   │
│  │  Purchase Request │───────▶│  Attestation Carrier      │   │
│  │  - $299 SaaS sub  │        │  - Proof attached to tx   │   │
│  │  - Within scope   │        │  - Validity checkable     │   │
│  └──────────────────┘        └────────────┬─────────────┘   │
└────────────────────────────────────────────┼─────────────────┘
                                             │ payment + proof
                                             ▼
┌─────────────────────────────────────────────────────────────┐
│                     SELLER (Counterparty)                     │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Lemma Verification                                   │   │
│  │  1. Is the attestation signed by the claimed issuer?  │   │
│  │  2. Is the spend within the stated ceiling?           │   │
│  │  3. Is the category within the stated scope?          │   │
│  │  4. Is the attestation still valid (not revoked)?     │   │
│  │  ✓ All checks pass → accept payment                   │   │
│  │  ✗ Any check fails → reject transaction               │   │
│  └──────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

## Component Flow

1. **Issuance:** The enterprise's delegation policy is encoded as a Lemma attestation. Poseidon hash commits the constraints on-chain; BBS+ signature proves the issuer's authority.
2. **Carriage:** The agent carries the attestation with each transaction. The proof does not reveal the full policy — only that the specific transaction falls within authorized bounds.
3. **Verification:** The seller checks the attestation independently. No trust in the agent or platform is required — only in the issuing organization's signature.
4. **Revocation:** If the enterprise revokes the delegation, the on-chain revocation status updates immediately. Sellers check revocation in real-time.
