---
title: "Architecture"
---


Lemma's four cryptographic layers correspond to the cross-chain bridge lifecycle.

**1. ENCRYPT — Issuance-Time Confidentiality**

At the moment a cross-chain message is generated on the source chain, the confidential states involved in issuance (custody path, solvency status, restaking depth) are encrypted with AES-GCM. Originals remain under the protocol's control; only attestable hashes ride in the message payload.

**2. PROVE — Origin Authentication Gateway**

A Lemma Origin Authentication Gateway is deployed at the issuance boundary on the source chain. At cross-chain message generation time:

```
proof(issuer_id, source_chain_id, action_hash, conditions_hash, timestamp)
```

is generated as a ZK proof. The proof binds the action to its source without revealing confidential states. Circuit primitives use Poseidon over BN254 (commitment), BBS+ over BLS12-381 (selective disclosure), and Groth16 (ZK proof).

The proof is attached to the message payload as an attestation and does not depend on the sender's continued honesty during transit.

**3. DISCLOSE — Pre-Execution Verifier and Domain Policy**

A Lemma Pre-Execution Verifier is integrated into the OFT adapter on the destination chain. Before the adapter commits:

- Extract origin proof from incoming message
- Verify ZK proof against issuer, source chain, and conditions
- Check domain policy layer: replay-prevention nonce, custody path, restaking depth limits, solvency attestation

Policies are configurable per protocol. Conservative protocols may require all four; more permissive protocols may enable only a subset. If verification fails, the message is rejected at write time.

**4. PROVENANCE — On-Chain Attestation Anchoring**

All origin proofs are committed to a Merkle tree whose root is periodically anchored on-chain. This provides post-hoc forensic evidence surviving log deletion, audit trails for regulatory compliance, and independent verification for dispute resolution — permanently.
