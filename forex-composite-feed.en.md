---
slug: "forex-composite-feed"
date: "2026.07.24"
category: "Announcements"
section: "Changelog"
title: "We've published a proof-backed forex rate feed"
cover: "assets/cover-announcements.png"
abstract: "A composite rate built from multiple public forex APIs, delivered with a proof at the moment of capture. No key required to call it; anyone can re-verify the returned rate against its proof. This post covers what we guarantee, what we do not — and how far we disclose sources."
tags:
  - verifiable-origin
  - provenance
  - zk-proof
relatedLinks:
  - label: "See the breakdown in the Verification Center"
    href: "https://lemma.frame00.com/verify/"
  - label: "Three core technologies (Pillars)"
    href: "https://lemma.frame00.com/pillars/"
---

As the first release of Lemma's proof-backed data API, we have published a composite forex rate feed. No key and no account required.

```
GET https://workers.lemma.workers.dev/v1/suites/feeds/forex/composite/latest
```

It returns a composite rate built by cross-checking multiple public forex APIs — and only after every source has been verified. Updates are daily.

This post covers what the feed returns, what it guarantees, and how far we disclose sources. First, though, we need to be clear about what "proof-backed" means here.

## What "proof-backed" means here

In Lemma feeds, "proof-backed" means a proof is issued where the data is born, and anyone can later re-verify that proof. It has three properties:

- **A proof is issued at capture time.** When the forex rates are fetched, we do not merely store the values. We issue a proof that "this value was correctly computed by this procedure."
- **Anyone can re-verify the proof.** Each proof is verifiable with Groth16, and the public signals include the actual rate values. If re-verification succeeds, the values have not been tampered with.
- **Verification is free — no key, no account.** From anywhere in the world, authenticity can be checked with public information alone.

So "proof-backed" is not decoration meant to earn trust. It is a mechanism that lets you verify without presupposing trust. That distinction underpins the later decision not to disclose sources.

## What it returns

The response includes the composite rates plus a `verification` object that shows how fully those values have been verified — the re-verification result for the proofs attached to that document.

```json
{
  "feedId": "forex/composite",
  "docHash": "0x68d4bff9…",
  "schema": "canonical-sort-v1",
  "subjectId": "forex/composite-2026-07-23",
  "createdAt": "2026-07-23 17:46:00",
  "attributes": {
    "source": "forex-composite",
    "base": "USD",
    "date": "2026-07-23",
    "rates.JPY": 163.27832,
    "rates.JPY_scaled": 16327832000,
    "rates.EUR": 0.877087
  },
  "verification": {
    "total": 29,
    "verified": 29,
    "failed": 0,
    "verifyIds": ["…"],
    "verifyUrl": "https://workers.lemma.workers.dev/v1/documents/0x68d4bff9…"
  }
}
```

`verification.total` is the number of proofs attached to the document; `verified` is how many passed Groth16 verify; `failed` is the failure count. `verifyIds` are content-derived receipts from each verify call (the ID returned by `POST /v1/proofs/verify`); `verifyUrl` points at `GET /v1/documents/{docHash}` (the registration record). `rates.JPY_scaled` is the same scaled integer that appears in the proof's public inputs. The `rates` above are excerpted from a live API snapshot.

Each proof includes the circuit's **public signals**, which contain the actual rate values. The public-input order for `forex-average-v1` is (confirm with `GET /v1/circuits/forex-average-v1`):

```
sourceRootA, sourceRootB, randomnessA, randomnessB, pathHash, averageRate
```

The last field, `averageRate`, is the rate. Values are scaled by 10⁸ to avoid floating-point error — for example, `JPY = 163.27832` becomes `16327832000`.

## Verification — two stages

Verification for this feed has two stages, depending on what you need.

### Basic verification (anyone, on the spot)

On each `GET .../latest`, the feed re-verifies every attached proof via an internal `POST /v1/proofs/verify`. If `verification.verified` equals `verification.total`, every proof attached to that document has passed Groth16 verification.

```bash
curl -s https://workers.lemma.workers.dev/v1/suites/feeds/forex/composite/latest \
  | jq '.verification'
# if verified === total, all proofs are verified
```

Cumulative verification counts are also available from the public registry (`GET /v1/counters`), and those numbers themselves carry provenance in the Verification Center. Verification costs ¥0.

### Detailed verification (value binding and re-verifying the proof)

You can separate "is this value in the public inputs?" from "does Groth16 accept this proof?". There is **no `GET /v1/proofs/{id}`** — a `verifyId` is a verification receipt, not a key for fetching the proof body.

#### 1. Check the public-input value (no API key)

`rates.<CCY>_scaled` in the feed response is the same integer as the proof's `averageRate`.

```bash
curl -s https://workers.lemma.workers.dev/v1/suites/feeds/forex/composite/latest \
  | jq '{
      jpy: .attributes["rates.JPY"],
      scaled: .attributes["rates.JPY_scaled"]
    }'
# scaled === 16327832000 means it matches the public-input value
```

Circuit public-input names:

```bash
curl -s https://workers.lemma.workers.dev/v1/circuits/forex-average-v1 \
  | jq '.inputs'
# [..., "averageRate"] — rate is last
```

With an API key, you can fetch the public-signal arrays themselves by `docHash` (SDK: `attributes.query`):

```bash
curl -s -X POST https://workers.lemma.workers.dev/v1/verified-attributes/query \
  -H "Authorization: Bearer $LEMMA_API_KEY" \
  -H "content-type: application/json" \
  -d '{"docHash":"0x68d4bff9…","attributes":[]}' \
  | jq '.results[].proof | {circuitId, averageRate: .inputs[-1]}'
# confirm inputs[-1] === rates.JPY_scaled
```

#### 2. Re-verify the proof with Groth16 (no API key)

When you have `{ circuitId, proof, publicSignals }`, re-verification needs no auth:

```bash
curl -s -X POST https://workers.lemma.workers.dev/v1/proofs/verify \
  -H "content-type: application/json" \
  -d '{
    "circuitId": "forex-average-v1",
    "proof": "<proof JSON or base64>",
    "publicSignals": ["<sourceRootA>", "<sourceRootB>", "<randomnessA>", "<randomnessB>", "<pathHash>", "16327832000"]
  }' \
  | jq '{valid, verifyId, circuitId}'
# valid === true means the pairing check passed; verifyId is the receipt
```

For local verification with the SDK, load the vkey via `circuits.getById` and pass it to `verifier.verify`:

```ts
import { create, circuits, verifier } from "@lemmaoracle/sdk";

const client = create({ apiBase: "https://workers.lemma.workers.dev" });
const meta = await circuits.getById(client, "forex-average-v1");
// resolve meta.artifact.location.vkey to a vkey JSON, then:
const { ok } = await verifier.verify({
  alg: "groth16-bn254-snarkjs",
  inputs: { vkey, proof, publicSignals },
});
```

The feed's `verification` block is the result of running that same `POST /v1/proofs/verify` server-side against every proof. Value binding is checked via `rates.*_scaled` (or `proof.inputs`); cryptographic validity via `valid: true` / `verified === total` — two separate axes.

Note: for holiday and postal-code feeds, you can re-hash `contentHash` locally and compare (against the SHA-256 of the canonical JSON). Forex proves a composite value in-circuit, so you check via `verification` and the public signals. The verification method follows the nature of each feed.

## Why "composite"?

Forex rates differ slightly depending on which API you observe. Treating one vendor's number as "correct" is the same as trusting that one vendor.

So we fetch from multiple public forex APIs and compute a composite by cross-checking them. Only snapshots where every source has been fetched and verified are used in the response. If one source goes down, the others keep the feed alive; if one emits an outlier, the composite step dampens the impact.

We currently use two sources, designed so more can be added later. The more sources you have, the thinner the dependence on any single vendor — and the stronger the case for the value's reasonableness.

## How far we disclose sources

This is the part we most wanted to write. Short version: we do not publish individual provider names or the raw rates we fetched. Two reasons.

First, license constraints. Some forex API terms forbid redistributing the rates as fetched. Serving a composite does not violate those terms; putting the original rates in the API response would.

Second, design. Even without revealing the original rates, the correctness of the composite can be checked by verifying the proof. The `forex-average-v1` circuit proves that "the average was correctly computed from two inputs." If re-verification passes, the composite computation is confirmed. There was no need to include the originals in the API response — the proof already does that job.

Also, the sources themselves are public APIs. Anyone can call them directly and obtain the original rates. Using those values to re-verify the proof locally, a third party can independently confirm that "this composite was correctly computed from these sources." It is not hidden; it is reproducible.

For feeds where the origin itself is the value — postal codes, holidays — we do the opposite and surface the sources (Japan Post, Cabinet Office) prominently. That is not a disclosure strategy; it is terms-of-use compliance, and "being that data" is the point. On the same substrate, a composite feed and an official single-source feed point information in opposite directions.

## What we guarantee — and what we do not

If we call it a proof, the boundary has to be clear. This feed's proof goes this far:

- This composite rate was correctly computed by this composite procedure, and that value is included in a verified proof.

What it does not say, also clearly:

- It does not guarantee that any individual source's value is the "correct" market value. The proof covers the composite computation and value integrity — not the truth of the primary inputs.

The composite's job is precisely to narrow, in practice, the gap that "we cannot guarantee the truth of primary inputs" leaves open — by cross-checking multiple sources. Proofs handle "the value was correctly computed and not tampered with"; the composite handles "the value is reasonable." Not letting either one speak for both is the design's core.

## Where it fits

For example: when booking overseas revenue (USD) into yen, the conversion rate carries a proof of "when and from what." The same applies on the expense side — yen conversion of a USD invoice gets provenance. Unlike a self-reported screenshot, a third party can verify the conversion rate itself. That is the practical difference a proof-backed feed makes.

## Next

In the posts that follow, we will cover the remaining two feeds one at a time.

- **Postal codes:** over 120,000 nationwide entries, also fetchable one at a time. What a 4/4 data-commitment means (this feed supports local `contentHash` checks).
- **Holidays:** why put a proof on data that changes only once a year.

This feed is part of Lemma's proof-backed public data API.
