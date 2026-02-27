---
slug: "define-your-domain-as-a-schema"
date: "2026.02.28"
category: "Guides"
section: "Essays"
title: "Define Your Domain as a Schema"
abstract: "Model how your AI retrieves and clusters knowledge — bucket ages, risk scores, regions — with typed schemas and normalization. Register ZK circuits and generators so every fact traces back to its source."
---

## Why Schemas Matter

Without schemas, a "temperature" field in one document might be Celsius while another uses Fahrenheit. An "age" field might be a raw integer in one record and a date of birth in another. ZK proofs cannot work with ambiguous data — they need a normalized, typed attribute space where comparisons are well-defined.

Lemma schemas solve this by defining, per domain, exactly what raw data looks like, what normalized data looks like, and how to transform one into the other.

## Defining a Schema

A schema has three parts:

1. **Raw type** — the shape of the original document.
2. **Norm type** — the normalized attribute space used for commitments and proofs.
3. **normalize function** — the transformation from Raw to Norm.

```typescript
import { define } from "@lemmaoracle/sdk";

type UserKycRaw = { age: number; country: string };
type UserKycNorm = { age_bucket: "adult" | "minor"; country: string };

const userKycSchema = define<UserKycRaw, UserKycNorm>({
  id: "user-kyc-v1",
  normalize: (raw) => ({
    age_bucket: raw.age >= 18 ? "adult" : "minor",
    country: raw.country,
  }),
});
```

The `id` is referenced throughout the system — in `prepare`, `documents.register`, circuit metadata, and `attributes.query`. It is the thread that ties raw data to proofs to query results.

## Normalization Is the Key Design Decision

The normalize function determines the granularity of your verified attributes. Consider temperature:

- `normalize: (raw) => ({ temp_bucket: raw.temperature < 10 ? "cold" : raw.temperature < 25 ? "mild" : "hot" })` — three buckets, lossy but privacy-preserving.
- `normalize: (raw) => ({ temperature: raw.temperature })` — exact value, no bucketing, less privacy.

This is a domain-specific design decision. Lemma does not prescribe the right granularity — it gives you the tools to define it precisely and ensures that ZK proofs and commitments operate on the normalized space you chose.

## Generators: Documenting Data Origins

A schema defines the attribute space. A **generator** defines how raw documents are produced — which script, which inputs, which external APIs.

The `generator_id` and its hash are included in ZK public inputs, so every verified attribute traces not just to a schema and circuit, but to the exact process that produced the original data.

## Circuits Complete the Picture

A schema normalizes data. A generator produces data. A **circuit** proves predicates on normalized, committed data. Together, these three components form a complete, auditable chain:

`Generator → Raw document → Schema → Normalized attributes → Commitments → Circuit → Proof`

When a consumer queries verified attributes, the response includes all three IDs — `schemaId`, `generatorId`, and `circuitId` — so the full chain of reasoning is transparent.

## Extensibility by Design

Schemas, generators, and circuits are registered via the same pattern: metadata goes to a server-side registry, artifacts (wasm, zkey) are stored on IPFS/HTTPS, and IDs are referenced by other APIs. Adding a new domain means registering a new schema, writing a normalize function, and optionally creating circuits and generators. No changes to the core protocol are needed.
