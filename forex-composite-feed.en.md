---
slug: "forex-composite-feed"
date: "2026.07.24"
category: "Announcements"
audience: technical
section: "Changelog"
title: "Announcing our proof-backed forex rate feed — verify any rate, free"
cover: "assets/cover-announcements.png"
abstract: "The first release of Lemma's proof-backed data API is live: a composite forex rate, cross-checked across multiple public sources and delivered with a cryptographic proof issued at the moment of capture. No key, no account — and anyone, anywhere can re-verify every rate for free. Here's what it guarantees, what it doesn't, and why that distinction is the whole point."
tags:
  - verifiable-origin
  - provenance
  - zk-proof
relatedLinks:
  - label: "Attach a proof to the rate (what it looks like in payments)"
    href: "https://lemma.frame00.com/blog/verifiable-fx-rates-for-payments/"
  - label: "See the breakdown in the Verification Center"
    href: "https://lemma.frame00.com/verify/"
  - label: "Three core technologies (Pillars)"
    href: "https://lemma.frame00.com/pillars/"
---

Today we're shipping the first release of Lemma's proof-backed data API: a composite forex rate feed. It is live right now — no key, no account, nothing to sign up for. One GET away:

```
GET https://workers.lemma.workers.dev/v1/suites/feeds/forex/composite/latest
```

It returns a composite rate built by cross-checking multiple public forex APIs — published only after every source has been verified — with a cryptographic proof issued at the moment the data was born. Updates are daily.

Exchange-rate data is everywhere. Exchange-rate data that can **prove itself** is not. That is the gap this feed opens: you don't have to trust us, or any single vendor — you can check. This post covers what the feed returns, what it guarantees, and how far we disclose sources. First, though, we need to be clear about what "proof-backed" means here.

## What "proof-backed" means here

In Lemma feeds, "proof-backed" means a proof is issued where the data is born, and anyone can later re-verify that proof. It has three properties:

- **A proof is issued at capture time.** When the forex rates are fetched, we do not merely store the values. We issue a proof that "this value was correctly computed by this procedure."
- **Anyone can re-verify the proof.** Each proof is verifiable with Groth16, and the public signals include the actual rate values. If re-verification succeeds, the values have not been tampered with.
- **Verification is free — no key, no account.** From anywhere in the world, authenticity can be checked with public information alone.

So "proof-backed" is not decoration meant to earn trust. It is a mechanism that lets you verify without presupposing trust. That distinction underpins the later decision not to put the raw source rates in the response.

## What it returns

The response includes the composite rates plus a `verification` object that shows how fully those values have been verified — the re-verification result for the proofs attached to that document.

```json
{
  "feedId": "forex/composite",
  "docHash": "0x…",
  "schema": "canonical-sort-v1",
  "subjectId": "forex/composite-…",
  "createdAt": "…",
  "attributes": {
    "source": "forex-composite",
    "base": "USD",
    "date": "<YYYY-MM-DD>",
    "rates.AUD…rates.ZAR": "<float>",
    "rates.AUD_scaled…rates.ZAR_scaled": "<float × 10^8>",
    "sourceRoot.frankfurter": "0x…",
    "sourceRoot.erApi": "0x…"
  },
  "verification": {
    "total": 29,
    "verified": 29,
    "failed": 0,
    "verifyIds": ["…"],
    "verifyUrl": "https://workers.lemma.workers.dev/v1/documents/0x…"
  }
}
```

`verification.total` is the number of proofs attached to the document; `verified` is how many passed Groth16 verify; `failed` is the failure count. `verifyIds` are content-derived receipts from each verify call (the ID returned by `POST /v1/proofs/verify`); `verifyUrl` points at `GET /v1/documents/{docHash}` (the registration record). `rates.JPY_scaled` is the same scaled integer that appears in the proof's public inputs. Rate values change with each snapshot (each call).

Each proof includes the circuit's **public signals**, which contain the actual rate values. The public-input order for `forex-average-v1` is (confirm with `GET /v1/circuits/forex-average-v1`):

```
sourceRootA, sourceRootB, randomnessA, randomnessB, pathHash, averageRate
```

The last field, `averageRate`, is the rate. Values are scaled by 10⁸ to avoid floating-point error: `rates.<CCY>_scaled === rates.<CCY> × 10⁸`.

## Verification — two stages

Verification for this feed has two stages, depending on what you need.

### Basic verification (anyone, on the spot)

On each `GET .../latest`, the feed re-verifies every attached proof via an internal `POST /v1/proofs/verify`. If `verification.verified` equals `verification.total`, every proof attached to that document has passed Groth16 verification.

```bash
curl -s https://workers.lemma.workers.dev/v1/suites/feeds/forex/composite/latest \
  | jq '.verification'
# if verified === total, all proofs are verified
```

Cumulative verification counts are also available from the public registry (`GET /v1/counters`), and those numbers themselves carry provenance in the [Verification Center](https://lemma.frame00.com/verify/). Verification costs ¥0.

### Detailed verification (value binding and re-verifying the proof)

You can separate "is this value in the public inputs?" from "does Groth16 accept this proof?".

#### 1. Check the public-input value (no API key)

`rates.<CCY>_scaled` in the feed response is the same integer as the proof's `averageRate`.

```bash
curl -s https://workers.lemma.workers.dev/v1/suites/feeds/forex/composite/latest \
  | jq '{
      jpy: .attributes["rates.JPY"],
      scaled: .attributes["rates.JPY_scaled"],
      ok: (.attributes["rates.JPY_scaled"] == (.attributes["rates.JPY"] * 1e8 | round))
    }'
# ok === true means the float and _scaled in the response match at ×10^8
# scaled is the same integer as public-input averageRate
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
  -d '{"docHash":"<docHash from feed>","attributes":[]}' \
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
    "publicSignals": ["<sourceRootA>", "<sourceRootB>", "<randomnessA>", "<randomnessB>", "<pathHash>", "<averageRate>"]
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

Short version: **raw rates fetched from the forex APIs stay private; the source APIs are disclosed.** In the response, `sourceRoot.frankfurter` and `sourceRoot.erApi` name which public APIs were used and carry the Merkle commitment root of each fetch.

The point we most want to make is this: **you do not need those raw rates, because the ZK cryptographic binding already holds.** The `forex-average-v1` circuit proves that "the average was correctly computed from two inputs." If re-verification passes, the composite computation is confirmed. There was no need to put the originals in the API response — the proof already does that job.

On top of that, the source APIs are disclosed. Anyone can call the same public APIs, build a commitment from the returned rates, and check whether it matches `sourceRoot.*`. Even without the raw rates in our response, a third party can primitively confirm that the fetch results were the same.

For feeds where the origin itself is the value — postal codes, holidays — we surface the sources (Japan Post, Cabinet Office) as attribution. The forex composite feed exposes source identifiers and commitment roots; that is a different orientation from making "being that institution's data" the product.

## What we guarantee — and what we do not

This feed's proof covers only the following:

- That the composite rate was computed according to the stated composite procedure
- That that value is included in the public inputs of a verified proof

It does not cover the following:

- That any individual source's value is the "correct" market rate

The proof addresses composite computation and value integrity, not the truth of the primary inputs. Reasonableness of those inputs is handled in practice by the composite step — cross-checking multiple sources. Correctness of computation and reasonableness of value are kept on separate mechanisms.

## Where it fits

Picture the close of a quarter: overseas revenue (USD) has to be booked into yen, and an auditor asks where the conversion rate came from. With this feed, the rate arrives carrying its own answer — a proof of "when, and from what." The same applies on the expense side: the yen conversion of a USD invoice gets provenance. Unlike a self-reported screenshot, a third party can verify the conversion rate itself. That is the practical difference a proof-backed feed makes — and it's available today, for free.

For how a proof-backed rate removes the round-trip to the issuer and the mismatched-rate dispute, taking cross-currency payments as the worked example, see [Attach a proof to the rate](https://lemma.frame00.com/blog/verifiable-fx-rates-for-payments/).

## This is just the start

The forex feed is the opening move. Two more proof-backed feeds are already on deck, and we'll introduce them here one at a time:

- **Postal codes:** over 120,000 nationwide entries, also fetchable one at a time — and what a 4/4 data-commitment means (this feed supports local `contentHash` checks).
- **Holidays:** why it's worth putting a proof on data that changes only once a year.

One dataset at a time, we're building public data that carries its own evidence. Issue once — verify free, any number of times. Try the feed, re-verify a proof, and see the numbers accumulate in the [Verification Center](https://lemma.frame00.com/verify/).
