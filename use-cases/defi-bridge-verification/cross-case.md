# Cross-Case: TradFi (MetLife) × DeFi (Kelp DAO) — Unified Proof Layer

## The Unified Message

**Both TradFi and DeFi share the same structural gap: the receiving system commits without verifying origin.**

The surface is different — employee exfiltration vs. bridge exploit — but the failure mode is identical: cryptographically valid actions that are semantically wrong, committed because no independent verification exists.

## Side-by-Side Comparison

| Dimension | MetLife (TradFi) | Kelp DAO (DeFi) |
|---|---|---|
| **Loss** | 2,476 customer records | $292M |
| **Surface** | Seconded employee accesses data across org boundary | Attacker spoofs message across chain boundary |
| **Root cause** | Trust boundary ambiguity (no inter-org data access policy) | Origin assumption at trust boundary (1-of-1 DVN) |
| **What was "valid"** | Employee had legitimate credentials at both orgs | Cross-chain message had valid DVN signature |
| **What was wrong** | Access violated implicit data governance rules | Message did not originate from claimed source chain |
| **Post-hoc response** | Weeks of log forensics, mutual blame | 46-min pause, $177M bad debt, $13B TVL exit |
| **What was missing** | Cryptographic proof of who accessed what, when | Pre-execution verification that message origin is genuine |
| **Lemma's fix** | ZK attestations for data access (tamper-evident audit trail) | ZK origin proofs for cross-chain messages (pre-execution attestation) |

## The Pattern: "Valid but Wrong"

```
TradFi:   Employee credentials valid + Access semantically unauthorized = Exfiltration
DeFi:     DVN signature valid + Message semantically forged = $292M exploit
```

In both cases, the existing verification layer validates the **cryptographic wrapper** (credentials, signatures) but not the **semantic content** (authorization scope, origin truth). Lemma provides the missing semantic verification.

## The Unified Architecture

```
                    ┌─────────────────────────────┐
                    │      Lemma Trust Layer       │
                    │  (verifiable origin proofs)  │
                    └──────────┬──────────────────-┘
                               │
                ┌──────────────┼──────────────┐
                ▼              ▼              ▼
        ┌──────────────┐ ┌──────────┐ ┌──────────────┐
        │   TradFi     │ │  DeFi    │ │  AI / Agent  │
        │  Access      │ │  Bridge  │ │  Tool/API    │
        │  Provenance  │ │  Origin  │ │  Authorization│
        └──────────────┘ └──────────┘ └──────────────┘
```

The same verification primitive — "prove the origin before committing" — applies across all three domains. The circuit and policy details differ, but the architectural pattern is shared:

1. **Issuance:** Produce origin proof at the moment of state mutation
2. **Transport:** Carry the proof alongside the action across the trust boundary
3. **Verification:** Receiving system verifies before committing; rejects if proof fails

## Sales Positioning

### For TradFi Prospects

> "When your employee walks out with customer data, your DLP flags the anomaly but can't prove what happened. When the regulator asks, you spend weeks on log forensics. The same proof layer that stops $292M bridge exploits also makes your audit trails undeniable — in hours, not weeks."

### For DeFi Prospects

> "The same structural gap that let an employee walk out with 2,476 records at MetLife let an attacker drain $292M from Kelp DAO. In both cases, the action was 'valid' but semantically wrong. In both cases, the receiving system committed without verifying origin. Lemma is the proof layer that works on both sides of the boundary."

### For Cross-Domain Prospects (Enterprises with Both)

> "Your compliance team needs undeniable audit trails. Your DeFi operations need pre-execution attestation. These are the same primitive: verifiable origin. One proof layer, two surfaces."

## Implications for Product Roadmap

The unified pattern suggests that Lemma's proof-bundle primitives (Poseidon/BN254, BBS+/BLS12-381, Groth16) should be presented as a **shared verification foundation** with domain-specific policy layers:

| Layer | Shared | Domain-Specific |
|---|---|---|
| Circuit primitives | Poseidon, BBS+, Groth16 | Same across all |
| Proof generation | Issuance-side origin proof | Same pattern |
| Proof verification | Pre-execution attestation | Same pattern |
| Domain policy | — | TradFi: access scope, retention rules; DeFi: replay prevention, custody path, rehypothecation depth; AI: authorization scope, model version |

This architecture allows a single sales conversation to address multiple verticals without rebuilding the core for each.
