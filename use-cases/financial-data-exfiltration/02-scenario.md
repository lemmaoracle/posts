---
title: "Scenario"
---

# Scenario: Seconded Employee Data Exfiltration

## Before Lemma — How the Incident Unfolds

### Context

A life insurance company (Insurer A) seconds an employee to a bank agency (Agency B) to sell insurance products through the bank's channel. The employee:

1. Has credentials for Insurer A's customer database
2. Gains credentials for Agency B's customer management system
3. Works at Agency B's office with access to both systems simultaneously

### Incident Timeline

| Phase | What Happens | Why It's Undetectable |
|---|---|---|
| Access | Employee queries 2,476 customer records across both systems | Access appears legitimate — employee still has valid credentials |
| Collection | Employee copies data to personal device / USB / cloud storage | No tamper-evident logging — access logs are mutable, stored in siloed systems |
| Exfiltration | Employee carries data to a new employer or sells it | No cross-organization audit trail exists |
| Discovery | Months later, customer complaints or regulatory audit reveals the breach | Logs may have been altered; no cryptographic proof of original access patterns |
| Response | Insurer A and Agency B cannot prove exactly what was accessed or when | Mutual blame; regulatory penalty for both organizations |

### Root Cause Analysis

1. **Trust boundary ambiguity** — No explicit, enforceable data access policy at the inter-organization boundary
2. **Mutable audit logs** — Both organizations store access logs in modifiable databases; no cryptographic guarantee of integrity
3. **No shared proof layer** — Insurer A cannot verify Agency B's logs and vice versa
4. **Deterrence gap** — Employee knows logs can be disputed; no "undeniable proof" deterrent exists

---

## After Lemma — How the Same Scenario Plays Out

### What Changes at Deployment

1. **Lemma attestation gateway** sits between each organization's CRM/database and the user
2. Every data access request generates a **ZK attestation**: "User X accessed record Y at timestamp T" — without revealing the record contents
3. Attestations are anchored on-chain (or to a commitment root), making them **tamper-evident by construction**
4. Both organizations share a **verifiable proof layer** — neither can unilaterally alter the access history

### Incident Timeline (With Lemma)

| Phase | What Happens | How Lemma Changes It |
|---|---|---|
| Access | Employee queries customer records | Each query generates a ZK attestation, timestamped and committed |
| Collection attempt | Employee tries to bulk-export data | Anomalous access pattern (volume, timing) flagged by DLP; Lemma attestation proves exactly which records were accessed |
| Deterrence | Employee is aware that all access is cryptographically proven | Psychological deterrence — "I cannot deny or alter this trail" reduces insider threat incentive |
| If exfiltration occurs | Employee leaves with data | Both organizations have **immutable, shared proof** of what was accessed — no dispute possible |
| Regulatory response | FSA requests audit evidence | Insurer provides verifiable attestations — no manual log reconciliation needed |

### Quantified Impact

| Metric | Without Lemma | With Lemma |
|---|---|---|
| Time to detect anomalous access | Weeks–months | Real-time (DLP) + cryptographic proof (Lemma) |
| Ability to dispute access logs | High — logs are mutable, siloed | Near-zero — ZK attestations are tamper-evident |
| Regulatory audit preparation | Weeks of manual log aggregation | Hours — export verifiable attestation set |
| Cross-org blame | Mutual denial | Shared, undeniable proof |
| Deterrence of insider threat | Low — "I can plausibly deny" | High — "Every access is cryptographically proven" |

---

## Key Scenarios for Sales Narrative

### Scenario A: "The Regulator Asks"

> FSA issues a reporting order. Without Lemma: 2 weeks of log forensics across 36 agencies. With Lemma: export the attestation set in 1 hour, cryptographically verifiable.

### Scenario B: "The Seconded Employee"

> Employee is seconded from Insurer A to Agency B. Lemma issues attribute-based access credentials with time-bound scope. When the secondment ends, access is revoked AND all access during the secondment is provably logged.

### Scenario C: "The Cross-Org Audit"

> Two organizations need to reconcile who accessed what. Without Lemma: each exports their own logs, no way to verify the other's. With Lemma: shared commitment root, mutual verifiability.
