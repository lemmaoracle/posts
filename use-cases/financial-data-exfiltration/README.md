---
title: "Financial Data Exfiltration Prevention"
abstract: "When employees are seconded between organizations, the trust boundary for customer data becomes ambiguous. Lemma provides ZK-proof access provenance to close the gap."
pillar: verifiable-ai
targetVerticals:
  - Life insurance
  - Regional banks
  - Securities firms
tags:
  - AML
  - KYC
  - data-exfiltration
  - FSA
relatedUseCases:
  - defi-bridge-verification
---

# Use Case: Financial Data Exfiltration Prevention

## Problem

When employees are seconded between organizations (e.g., insurance company → bank agency), the trust boundary for customer data becomes ambiguous. Seconded staff retain access to systems at both origin and destination, creating a window for unauthorized data exfiltration.

**Triggering incident:** MetLife Japan (2026) — a seconded employee exfiltrated customer data from 36 agency partners (Hiroshima Bank, Fukuoka Bank, etc.), affecting 2,476 records. The largest such incident in Japanese life insurance history. Across the top 4 life insurers, ~3,500 total cases were identified. The FSA issued a reporting order under the Insurance Business Act.

## Lemma's Value Proposition

| Capability | Lemma's Role |
|---|---|
| Access provenance (who accessed what, when) | ✅ Core — ZK-proof of access logs with privacy preservation |
| Real-time exfiltration detection | ⚠️ Complementary — integrate with DLP/SIEM; Lemma provides the proof layer |
| Post-incident tamper evidence | ✅ Core — immutable, verifiable trail |
| Psychological deterrence | ✅ Indirect — "undeniable proof exists" changes behavior |

### Key Positioning

**Detection = existing tools (DLP, SIEM). Proof = Lemma.**

Lemma does not compete with DLP/SIEM — it completes them. DLP flags the anomaly; Lemma proves the access happened and the log hasn't been tampered with. This is a defensible, non-overlapping position.

## Contents

- **[scenario.md](./scenario.md)** — Before/after narrative based on the MetLife incident
- **[architecture.md](./architecture.md)** — Lemma + DLP/SIEM integration architecture
- **[proof-points.md](./proof-points.md)** — Regulatory timeline, industry data, enforcement trends
- **[pitch-deck.md](./pitch-deck.md)** — Single-slide sales asset

## Target Verticals

- Life insurance / general insurance
- Regional banks (地銀) with agency partnerships
- Securities firms with cross-organization secondment
- Any regulated industry with FSA/PPC oversight

## Related

- [FP architecture guide](../architecture/fp.md)
- Lemma spec: `packages/spec/`
