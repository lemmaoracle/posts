# Architecture: Lemma + DLP/SIEM Integration

## Design Principle

**Lemma is the proof layer, not the detection layer.**

Existing enterprise security tools (DLP, SIEM, CASB) handle detection and alerting. Lemma provides cryptographic proof of what happened, making audit results undeniable and tamper-evident.

```
┌─────────────────────────────────────────────────────────┐
│                     Enterprise Network                   │
│                                                          │
│  ┌──────────┐     ┌──────────────┐     ┌──────────────┐ │
│  │  CRM /   │────▶│   Lemma      │────▶│   DLP/SIEM   │ │
│  │  DB      │     │  Attestation │     │  (existing)  │ │
│  │          │     │  Gateway     │     │              │ │
│  └──────────┘     └──────┬───────┘     └──────────────┘ │
│                          │                               │
│                          ▼                               │
│                  ┌──────────────┐                        │
│                  │  Commitment  │                        │
│                  │  Root /      │                        │
│                  │  On-chain    │                        │
│                  │  Anchor      │                        │
│                  └──────────────┘                        │
└─────────────────────────────────────────────────────────┘

                         │
          ┌──────────────┼──────────────┐
          ▼              ▼              ▼
   ┌────────────┐ ┌────────────┐ ┌────────────┐
   │ Insurer A  │ │ Agency B   │ │ Regulator  │
   │ (verify)   │ │ (verify)   │ │ (audit)    │
   └────────────┘ └────────────┘ └────────────┘
```

## Component Breakdown

### 1. Lemma Attestation Gateway

Sits between the data store and the user. Intercepts every read/query and:

- Generates a ZK attestation: `proof(user_id, record_id, timestamp, access_type)`
- Commits the attestation to a local Merkle tree
- Periodically anchors the Merkle root on-chain
- Does **not** inspect or store the data contents (privacy-preserving)

### 2. DLP/SIEM (Existing)

Continues its normal function:

- Monitors access patterns for anomalies (bulk export, unusual hours, off-hours access)
- Generates alerts and incidents
- **New integration:** When DLP flags an anomaly, it can request a **verified access report** from Lemma — a cryptographic proof of exactly which records were accessed, when, and by whom

### 3. Commitment Root / On-chain Anchor

- Merkle root of all attestations, anchored at regular intervals
- Enables any party (Insurer A, Agency B, regulator) to verify that an attestation was generated at a specific time and has not been altered
- No sensitive data on-chain — only commitments

### 4. Verification Layer

Each stakeholder can independently verify:

- **Insurer A**: "Employee X accessed records [1..N] between dates [D1..D2]"
- **Agency B**: "The same employee accessed our records [M..P] during secondment"
- **Regulator**: "Both organizations' attestations are consistent and tamper-evident"

## Data Flow: Access Event

```
User → CRM Query
         │
         ▼
   ┌─────────────────────┐
   │ Attestation Gateway │
   │ 1. Intercept query  │
   │ 2. Generate ZK att. │
   │ 3. Commit to tree   │
   │ 4. Forward query    │──▶ CRM returns data
   └─────────────────────┘
         │
         ▼
   Attestation stored:
   {
     user_hash: "0xabc...",
     record_hash: "0xdef...",
     timestamp: 1714982400,
     access_type: "read",
     merkle_index: 47,
     proof: "0x..."
   }
```

## Data Flow: Anomaly Detection → Proof

```
DLP detects anomaly ──▶ Requests verified access report from Lemma
                              │
                              ▼
                    Lemma assembles attestations
                    for the flagged user/timeframe
                              │
                              ▼
                    Returns cryptographic proof:
                    "User X accessed Y records
                     between T1 and T2,
                     here is the Merkle proof
                     against root R (anchored at block B)"
                              │
                              ▼
                    DLP incident now has
                    UNDENIABLE EVIDENCE
```

## Data Flow: Regulatory Audit

```
FSA requests audit ──▶ Insurer exports attestation set
                                  │
                                  ▼
                        FSA verifies against
                        on-chain commitment roots
                                  │
                                  ▼
                        No manual log reconciliation.
                        Proof is self-evident.
```

## Deployment Model

| Model | Description | Best For |
|---|---|---|
| **Gateway proxy** | Lemma sits as a reverse proxy in front of the CRM/DB | Greenfield deployments, cloud-native systems |
| **SDK integration** | Application calls Lemma SDK directly before/after data access | Existing systems with API-layer control |
| **Log post-processor** | Lemma processes existing access logs and generates attestations after the fact | Legacy systems where proxy insertion is difficult (weakest guarantee — log must be trusted) |

## Security Considerations

- **Gateway bypass**: If a user can access the CRM directly (bypassing the gateway), attestations are incomplete. Defense: network-level enforcement (gateway is the only path to the data store)
- **On-chain anchor frequency**: Trade-off between cost and proof granularity. Recommended: hourly anchors for high-sensitivity environments
- **Privacy**: ZK attestations prove *that* an access occurred without revealing *what* was accessed (record contents remain private)
