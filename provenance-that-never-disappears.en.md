---
slug: "provenance-that-never-disappears"
date: "2026.02.28"
category: "Guides"
section: "Essays"
title: "Provenance That Never Disappears"
abstract: "Document commitments, schemas, issuers, and ZK verification results are anchored on-chain. Your RAG index can be rebuilt, your embeddings re-computed — the provenance layer stays permanent."
---

## Why On-Chain Provenance

Off-chain databases can be modified, deleted, or lost. Backups drift. Logs get rotated. If provenance metadata only lives on a server, the guarantee is only as strong as the operator's integrity and uptime.

Lemma anchors provenance on-chain because blockchain state is append-only, publicly auditable, and survives any single point of failure. When a document is registered, its provenance record becomes permanent.

## What Gets Anchored

The `LemmaRegistry` contract stores the following for each registered document:

- `docHash` — the SHA3-256 hash of the encrypted document.
- `attrCommitmentRoot` — the Merkle root of all attribute commitments.
- `schemaIdHash` — identifies which schema was used.
- Issuer and subject identifiers.
- Revocation data (revocation root and scheme).
- Links to ZK verifier contracts.

```solidity
struct DocumentProvenance {
  bytes32 docHash;
  bytes32 attrCommitmentRoot;
  bytes32 schemaIdHash;
  address issuer;
  address subject;
  bytes32 revocationRoot;
}
```

When a ZK proof is verified, the verifier contract emits a `ProofVerified` event with the `docHash`, `circuitIdHash`, `generatorIdHash`, and public inputs. This event is permanently recorded in the blockchain's event log.

## Rebuilding Without Losing Trust

Applications change. Vector databases get re-indexed. Embedding models get upgraded. RAG pipelines get rebuilt from scratch.

None of this affects provenance. Because the trust anchor lives on-chain, you can:

- Rebuild your RAG index and re-verify all attributes against the same on-chain records.
- Migrate to a new embedding model without re-proving — the proofs and their verification events are already permanent.
- Audit historical claims by replaying on-chain events.

The provenance layer is decoupled from the application layer. Your infrastructure can evolve freely.

## Smart-Contract Hooks

Lemma supports optional smart-contract hooks that execute at document registration time. These hooks receive the same `DocumentProvenance` payload that the registry stores:

```typescript
onchainHooks: [
  {
    chainId: 1,
    contractAddress: "0xYourHookContract...",
    method: "onLemmaDocumentRegistered",
    mode: "after-registry",
    payload: "registry-public-inputs",
  },
],
```

This enables composability with other on-chain protocols — a registered document can automatically trigger DeFi actions, update access control lists, or notify downstream contracts, all with the same provenance data.

## The Trust Model

Lemma's trust boundary is clear: it does not trust client-side code. It trusts:

- ZK proofs and their on-chain/off-chain verification.
- Cryptographic commitments.
- Issuer BBS+ signatures.
- On-chain registry and verifier contract correctness.

If a client sends a falsified normalization, the ZK proof will fail. If someone tampers with a stored document, the `docHash` will not match. The on-chain record is the ultimate arbiter, and it never disappears.
