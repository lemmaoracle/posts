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

So "proof-backed" is not decoration meant to earn trust. It is a mechanism that lets you verify without having to trust. That distinction is the root of the later decision not to disclose sources.

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

`verification.total` is the number of proofs attached to the document; `verified` is how many passed Groth16 verify; `failed` is the failure count; `verifyIds` are individual verification record IDs; `verifyUrl` points to the document registration record. The `rates` above are excerpted from a live API snapshot. The `verification` counts show the shape of a fully verified document.

Each proof includes the circuit's **public signals**, which contain the actual rate values. In the forex `forex-average-v1` circuit, the public signals include `averageRate`. Values are stored as scaled integers to avoid floating-point error — for example, `JPY = 163.27832` becomes `16327832000`.

## Verification — two stages

Verification for this feed has two stages, depending on what you need.

### Basic verification (anyone, on the spot)

If `verification.verified` equals `verification.total`, every proof attached to that document has passed Groth16 verification.

```bash
curl -s https://workers.lemma.workers.dev/v1/suites/feeds/forex/composite/latest \
  | jq '.verification'
# if verified === total, all proofs are verified
```

Cumulative verification counts are also available from the public registry (`GET /v1/counters`), and those numbers themselves carry provenance in the Verification Center. Verification costs ¥0.

### Detailed verification (when you need to check the values themselves)

You can also go further and confirm that the returned `JPY = 163.27832` is actually present in the proof's public inputs.

1. Take `verification.verifyIds[0]`
2. Look up that `verifyId`'s verification record and read the proof's public inputs
3. Confirm that the public input `averageRate` matches the expected value (`163.27832` → `16327832000`)

At that point, a third party can independently confirm that **the value they received is inside a verified proof**. Detailed steps will appear in separate documentation and future posts. This article stops at confirming that "the actual values are in the proof's public signals and have been verified."

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
