---
title: "Architecture"
---


Lemma's four cryptographic layers correspond to the cross-organizational data access lifecycle.

**1. ENCRYPT — Immutability of Access Content**

The Lemma Authentication Gateway never stores or inspects data contents. Queries and results to CRM systems and databases simply pass through the Lemma boundary. AES-GCM-encrypted payloads remain in the original systems.

**2. PROVE — ZK Access Attestation Generation**

The gateway intercepts all reads and queries, generating the following ZK attestation per access event:

`proof(user_id_hash, record_id_hash, timestamp, access_type)`

Both user identifiers and record identifiers are hashed; no personal information is exposed from the attestation itself. Attestations are aggregated into a local commitment tree.

**3. DISCLOSE — Stakeholder-Specific Verifiability**

Each party can independently verify:

- **Origin organization:** "Employee X accessed records [1..N] between dates D1 and D2"
- **Host organization:** "The same employee accessed our records [M..P] during their secondment"
- **Regulatory authority:** "Both organizations' attestations are consistent and tamper-detectable"

When DLP/SIEM flags an anomaly, a verified access report can be requested from Lemma. For the first time, the incident has non-repudiable evidence.

**4. PROVENANCE — Commitment Root On-Chain Anchoring**

The commitment root of all attestations is periodically anchored on-chain. Any party can verify that an attestation was generated at a specific time and has not been modified. **No confidential data exists on-chain — only commitments.** This enables regulatory compliance, dispute resolution, and post-incident forensics without data disclosure.
