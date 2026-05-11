---
title: "Scenario"
---


The MetLife Japan 2026 incident exposed this structural gap. A life insurance company seconded employees to agency partner locations (regional banks, securities firms) to sell insurance products through both channels. A seconded employee exfiltrated 2,476 customer records across 36 agency partners.

### Before Lemma — How the Incident Unfolded

| Phase | What Happened | Why It Wasn't Detected |
|---|---|---|
| Access | Seconded employee queried 2,476 customer records across both organizations' systems | Access appears legitimate. Employee holds valid credentials. |
| Collection | Data copied to personal devices, USB drives, cloud storage | Logs are mutable and fragmented across organizations |
| Exfiltration | Data transferred to new employer or buyer | No unified cross-organizational audit trail exists |
| Discovery | Months later, through customer complaints and regulatory audit | Access logs may have been tampered with. No cryptographic proof of original access patterns. |
| Response | Cannot precisely prove what was accessed and when | Mutual recrimination, regulatory penalties for both organizations |

The root cause is simple: **ambiguous trust boundaries operated with mutable logs.**

### After Lemma — The Same Scenario with a Difference

A Lemma Authentication Gateway is deployed between each organization's CRM/database and its users. Every data access request generates a ZK attestation:

`proof(user_id_hash, record_id_hash, timestamp, access_type)`

Without revealing record contents, only who accessed what and when is cryptographically fixed. Attestations are anchored on-chain (or via commitment root), making them **tamper-detectable by construction.**

| Phase | Behavior with Lemma |
|---|---|
| Access | Each query generates a ZK attestation, committed with a timestamp |
| Collection attempt | Anomalous patterns (volume, timing) are flagged by DLP. Lemma attestations precisely prove which records were accessed |
| Deterrence | Employees know that "all access is cryptographically proven." Psychological deterrence functions |
| Discovery | Both organizations immediately present tamper-proof shared proofs. No room for dispute |
| Regulatory response | FSA requests evidence. Verifiable attestation set exported within hours |

| Metric | Without Lemma | With Lemma |
|---|---|---|
| Anomalous access detection time | Weeks to months | Real-time (DLP) + cryptographic proof (Lemma) |
| Access log disputability | High | Near zero |
| Regulatory audit preparation time | Weeks (manual log aggregation) | Hours (attestation set export) |
| Cross-organizational blame | Mutual denial | Shared non-repudiable proof |
| Insider threat deterrence | Low ("I can probably deny it") | High ("everything is cryptographically proven") |
