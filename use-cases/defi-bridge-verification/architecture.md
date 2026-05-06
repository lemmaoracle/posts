# Architecture: Pre-Execution Attestation for Cross-Chain Bridges

## Design Principle

**The receiving system must verify origin before it commits.**

Today's bridge architecture places the verification burden on the sending side (DVN signatures, multi-sig thresholds). Lemma shifts the locus of trust to the receiving side: do not commit unless origin independently verifies.

## Current Architecture (Simplified)

```
Source Chain                              Destination Chain
┌──────────────┐    LayerZero OFT     ┌──────────────────┐
│  Kelp DAO    │────────────────────▶│  OFT Adapter     │
│  Contract    │   cross-chain msg   │                  │
└──────────────┘                     │  DVN verify?     │
                                     │  ✅ 1-of-1 sig   │
                                     │       │          │
                                     │       ▼          │
                                     │   COMMIT         │
                                     │  (release funds) │
                                     └──────────────────┘
                                        ↑
                            Single point of trust:
                            DVN signature alone determines
                            whether the action commits
```

**Failure mode:** If the DVN is compromised or the RPC is spoofed, the receiving system has no independent way to reject the message. It commits based on a single trust assumption.

## Lemma-Augmented Architecture

```
Source Chain                              Destination Chain
┌──────────────┐                    ┌──────────────────────┐
│  Kelp DAO    │                    │  OFT Adapter         │
│  Contract    │                    │                      │
└──────┬───────┘                    │  1. DVN verify?      │
       │                            │     ✅ 1-of-1 sig    │
       │  ┌─────────────────┐       │                      │
       └─▶│  Lemma Origin   │       │  2. Origin proof?    │
          │  Attestation    │       │     ✅ ZK verifies    │
          │  Gateway        │       │        │              │
          │                 │       │        ▼              │
          │  Produce ZK     │──────▶│  BOTH verify?        │
          │  origin proof   │  carry│     ✅ → COMMIT       │
          │  at issuance    │  proof│     ❌ → REJECT       │
          └─────────────────┘       └──────────────────────┘
                                        ↑
                            Defense in depth:
                            Two independent verification
                            layers must both pass
```

## Component Breakdown

### 1. Lemma Origin Attestation Gateway (Source Side)

Sits at the issuance boundary on the source chain. When a cross-chain message is generated:

- Produces a ZK origin proof: `proof(issuer_id, source_chain_id, action_hash, conditions_hash, timestamp)`
- The proof binds the action to its source without revealing sensitive state
- Conditions hashed include: solvency status, custody path, rehypothecation depth
- Proof is attached to the cross-chain message as an attestation payload

**Circuit primitives:** Poseidon over BN254 (in-circuit commitments), BBS+ over BLS12-381 (issuer-side selective disclosure), Groth16 (ZK proofs)

### 2. Lemma Pre-Execution Verifier (Destination Side)

Integrated into the OFT adapter's receive path. Before the adapter commits:

1. Extract the origin proof from the incoming message
2. Verify the ZK proof against the expected issuer, source chain, and conditions
3. Check the domain-policy layer: replay prevention, custody-path validation, rehypothecation-depth bounds
4. If verification passes → proceed to commit
5. If verification fails → reject the message, log the failure, trigger alert

**This verification is independent of the DVN layer.** It does not trust the DVN, the RPC nodes, or the sending side's continued honesty.

### 3. Domain Policy Layer

Above the circuit, a configurable policy layer constrains which origin shapes are acceptable for the receiving system:

| Policy | What It Enforces |
|---|---|
| Replay prevention | Each origin proof is bound to a unique nonce; replayed proofs are rejected |
| Custody-path validation | The proof must attest that the assets being bridged have a verifiable custody path from deposit to bridge escrow |
| Rehypothecation-depth bounds | The proof must attest that the rehypothecation depth of the underlying assets does not exceed a configured maximum |
| Solvency attestation | The proof must attest that the issuer was solvent at issuance time (based on on-chain state) |

Policies are per-protocol configurable. A conservative protocol can require all four; a more permissive one can enable a subset.

### 4. On-Chain Attestation Anchors

Every origin proof is committed to a Merkle tree, with the root anchored on-chain at regular intervals. This provides:

- Post-incident forensic evidence that survives log deletion
- Audit trail for regulatory compliance
- Dispute resolution: any party can verify what was attested and when

## Integration Paths

| Path | Description | Effort | Trust Guarantee |
|---|---|---|---|
| **OFT adapter hook** | Integrate Lemma verifier into LayerZero's `lzReceive` path as a pre-commit hook | Medium | Strongest — verification before any state change |
| **DVN augmentation** | Replace or augment the DVN with Lemma origin proofs as an additional verification requirement | Medium | Strong — defense in depth with existing DVN |
| **Monitoring overlay** | Lemma verifies origin proofs in parallel with existing flow; flags mismatches for manual intervention | Low | Weakest — detection only, no automated rejection |

### Recommended: OFT Adapter Hook

The OFT adapter hook is the recommended integration path because it ensures that **no state change occurs without origin verification**. This is the "verify before commit" guarantee that the current architecture lacks.

```
lzReceive() {
  1. Extract cross-chain message
  2. Run DVN verification (existing)
  3. Run Lemma pre-execution attestation (new)
     └── If fails: revert + alert
  4. Commit state change (only if both pass)
}
```

## Failure Injection

The [example-origin](https://github.com/lemmaoracle/example-origin) repository includes a failure injection mode:

- A transition that is cryptographically valid but semantically wrong (fake cross-chain message) is injected
- The verification rejects it before commit
- Demonstrates that the exploit is stopped at the boundary, not detected after the fact

## Security Considerations

- **Gateway bypass**: If the source-side gateway is bypassed (messages sent without origin proofs), the destination verifier rejects them. This is the correct behavior — the default is rejection, not acceptance.
- **Proof size and gas cost**: Groth16 proofs are ~128 bytes. Verification is a single pairing check. Gas overhead is minimal compared to the bridge transaction itself.
- **Proof freshness**: Origin proofs include a timestamp and nonce. Stale proofs are rejected. This prevents replay attacks.
- **Commitment frequency**: Merkle root anchors every N blocks or T seconds (configurable). Higher frequency = stronger forensic guarantee + higher on-chain cost.
