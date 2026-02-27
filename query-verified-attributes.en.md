---
slug: "query-verified-attributes"
date: "2026.02.28"
category: "Guides"
section: "Essays"
title: "Query Verified Attributes"
abstract: "Ask 'users over 18 in Japan' and get back attributes with full provenance — proof status, schema, issuer, generator, and verification method — ready for your RAG policy layer."
---

## The Last Mile: From Proofs to Answers

Encryption, commitments, ZK proofs, and selective disclosure are all infrastructure. What a developer or AI agent actually needs is an answer: "Give me verified attributes matching this condition."

Lemma's `attributes.query` API is that last mile. It accepts natural language or structured queries and returns verified attributes with complete provenance metadata.

## Natural Language Queries

```typescript
import { attributes } from "@lemmaoracle/sdk";

const results = await attributes.query(client, {
  query: "users over 18 in Japan",
  mode: "natural",
  proof: { required: true, type: "zk-snark" },
  targets: { schemas: ["user-kyc-v1"] },
});
```

The response includes, for each matching subject:

- The verified attributes (e.g., `{ age_bucket: "adult", country: "JP" }`).
- Proof status: whether the proof was verified on-chain, off-chain, or is pending.
- The `circuitId` that produced the proof.
- The `schemaId` that defines the attribute space.
- The `issuerId` that signed the original document.
- The `generatorId` that describes how the raw document was produced.

This is not a key-value lookup. It is a provenance-rich response that lets your application or AI agent make trust decisions.

## RAG Policy Integration

The provenance metadata returned by `attributes.query` is designed to feed directly into RAG policy layers. Common patterns:

- **Proof-required policy**: Only use attributes where `proof.status === "onchain-verified"`.
- **Schema whitelist**: Accept only attributes from known, audited schemas.
- **Issuer trust list**: Filter by trusted issuers to exclude unverified sources.
- **Recency check**: Use `date` metadata to exclude stale verifications.
- **Generator reproducibility**: Require a specific `generatorId` to ensure the raw data was produced by a known, auditable process.

These policies are implemented in your application layer, not in Lemma itself. Lemma provides the facts and their provenance; your application decides which facts to trust.

## What Sets This Apart

Traditional oracles return values. Lemma returns values with proof. When your AI says "this user qualifies," it can point to the exact circuit, schema, issuer, and on-chain verification event that supports the claim. This is the difference between an assertion and evidence.
