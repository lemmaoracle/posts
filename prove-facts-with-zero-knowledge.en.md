---
slug: "prove-facts-with-zero-knowledge"
date: "2026.02.28"
category: "Guides"
section: "Essays"
title: "Prove Facts with Zero Knowledge"
abstract: "Turn business rules like 'over 18' or 'revenue above threshold' into machine-checkable facts. Each proof is permanently recorded with its circuit and generator."
---

## From Business Rules to Provable Facts

"Is this user over 18?" is a business rule. "The committed attribute `age_bucket` equals `adult` under circuit `age-over-18`, verified against `attrCommitmentRoot` on chain 1" is a provable fact.

Lemma bridges the gap. You define business logic as ZK circuits, and every time a condition is checked, the result is a cryptographic proof that anyone can verify — without seeing the underlying data.

## How ZK Proofs Work in Lemma

The process has three steps:

1. **Normalize** the raw document into typed attributes using a schema.
2. **Commit** the normalized attributes via Pedersen + Merkle to produce an `attrCommitmentRoot`.
3. **Prove** a predicate against the commitments using a ZK circuit.

```typescript
import { prepare, prover } from "@lemmaoracle/sdk";

const prep = await prepare<UserKycRaw, UserKycNorm>(client, {
  schema: "user-kyc-v1",
  payload: rawDoc,
});

const zkResult = await prover.prove(client, {
  circuitId: "age-over-18",
  witness: {
    age_bucket: prep.normalized.age_bucket,
    randomness: prep.commitments.randomness,
    attr_commitment_root: prep.commitments.attrCommitmentRoot,
  },
});
```

The circuit `age-over-18` checks whether `age_bucket` equals `adult`. It never sees the actual age — only the committed, normalized value and the randomness that opens the commitment.

## Bring Your Own Circuits

Lemma does not restrict you to built-in predicates. Any ZK circuit (Circom, Halo2, or others) can be registered with metadata linking it to a schema, public inputs, verifier contract, and precompiled artifacts:

```json
{
  "circuit_id": "age-over-18",
  "schema": "user-kyc-v1",
  "public_inputs": ["attr_commitment_root"],
  "verifier": {
    "type": "onchain",
    "contract_address": "0xVerifier..."
  },
  "artifact": {
    "location": {
      "type": "ipfs",
      "wasm": "ipfs://Qm...-age18.wasm",
      "zkey": "ipfs://Qm...-age18.zkey"
    }
  }
}
```

The SDK's `prover.prove` resolves the artifact location from the `circuitId`, downloads the `wasm`/`zkey`, and generates the proof locally. No raw data leaves the client.

## Permanent Proof Records

Once generated, a proof is submitted to Lemma and verified on-chain or off-chain. The verification result is recorded with full context:

- Which `docHash` the proof pertains to.
- Which `circuitId` was used.
- The `publicInputs` that were verified.
- The `generatorId` that produced the original document.

This means your AI can later ask "how was this fact proven?" and receive a complete audit trail — circuit, schema, generator, and verification method — not just a boolean.

## Trust Boundary

Lemma does not trust the client's normalization code or generator scripts. It trusts the ZK proof, the commitment, and the on-chain verifier. If a client produces a bogus normalization, the proof will fail verification. The math enforces correctness, not the code.
