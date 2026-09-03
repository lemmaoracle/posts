---
slug: "verifiable-fx-rates-for-payments"
date: "2026.08.07"
category: "Solutions"
audience: business
industries: [finance]
coverPhoto: /assets/covers/verifiable-fx-rates-for-payments.jpg
section: "Essays"
title: "Attach a proof to the rate: FX payments you can check after the fact"
abstract: "Lemma's proof-backed forex feed is live: a composite rate cross-checked across multiple public FX APIs, delivered with proof of its origin, its point in time, and its freshness. Using cross-currency payments as the worked example, this post shows how a proof-backed rate removes the back-and-forth with the issuer, the mismatched-rate dispute, and the stale-rate settlement — and how that opens the door to automated reconciliation and agent-to-agent payments. The same pattern applies to reference data in any industry."
tags:
  - verifiable-origin
  - provenance
  - payments
  - financial-services
relatedLinks:
  - label: "Finance & FinTech use cases"
    href: "https://lemma.frame00.com/solutions/use-cases/finance/"
  - label: "Trust402 — delegate to agents, transact with confidence"
    href: "https://lemma.frame00.com/trust402/"
  - label: "Our proof-backed forex rate feed"
    href: "https://lemma.frame00.com/blog/forex-composite-feed/"
  - label: "See the real numbers in the Verification Center"
    href: "https://lemma.frame00.com/verify/"
---

_This post describes the Lemma proof-backed forex feed as of August 2026._

---

## Now available: the proof-backed forex feed

The first release of Lemma's proof-backed data feeds is a composite forex rate. We cross-check multiple public FX APIs and issue the resulting composite rate with a proof, generated at the moment of capture. Whoever receives it can re-verify it for free, as many times as they like, with no key and no account. Three things become checkable.

- **Origin** — which public sources the composite was built from. Each rate carries source identifiers and a commitment (a fingerprint of what was fetched), so a third party can call the same public APIs and check that the results line up.
- **Point in time** — when the rate is from. The proof is issued at the moment of capture, so "this is the value we used, at this moment" is fixed in a form no one can move.
- **Freshness** — whether this is the latest snapshot. You can check whether the value in your hands has kept up with updates, which means a stale value still in use becomes detectable.

You don't have to hand the underlying rate to the other side: if the proof re-verifies, that is confirmation the value has not been altered. Instead of asking someone to trust the number, both sides can check it without trust as a precondition. That is what separates this from a conventional API that simply hands out a rate as "data." We wrote up the mechanics in [Announcing our proof-backed forex rate feed](https://lemma.frame00.com/blog/forex-composite-feed/).

## One worked example: cross-currency payments

From here on, we take cross-currency payments as a single example of the work involved.

When you invoice, receive, or remit in a foreign currency, an exchange rate is applied each time and the amount follows from it. But what reaches the other party is only the converted figure, or a statement noting the rate. Which point in time that rate came from, and which source — there is normally no way to check that independently afterwards. Going digital does not change this, because anyone can produce a number. The basis for the rate is left to self-declaration, and the moment there is a discrepancy, the queries to the issuer begin.

With a proof-backed rate, the benefits stack in two stages. First, the rate itself becomes provable after the fact. On that foundation, the confirmation step can be removed and the work automated. The first lowers risk and cost; the second speeds up processing and widens what can be automated.

### Lowering risk

- No more round-trips to the issuer. The receiving side can settle the question on the spot, alone.
- No mismatched applied rates. Both sides check the same origin, point in time, and freshness.
- No settlements against a stale rate. Sync drift between systems shows up in the freshness check.

### Faster processing, more automation

- Reconciliation and invoice conversion can be automated. The basis carries its own proof, so the manual check comes out.
- Payments can be processed immediately. Less waiting on confirmation.
- Rate confirmation can be delegated to AI agents ([Trust402](https://lemma.frame00.com/trust402/)). Agents check each other's rate proofs before they settle.

Payments do not happen once at period end; they happen every time. So the risk reduction compounds with volume — and on top of it sits the next tier: automation and agent-to-agent settlement.

## And the reference data in your industry

This payments example is only one way in. The same shape — layering proof of origin, point in time, and freshness onto external reference data so the receiving side can check it afterwards — applies whatever the industry or the data. Use this example as a template and build the version that fits your own operations.

It works especially well for reference data that rarely changes, like postal codes or public holidays: the risk there is carrying on with an old value without noticing it changed, and the freshness check is exactly what catches that. Proof-backed feeds for domains where reference data changes quietly are the subject of a follow-up post.

## Toward payments whose rate stays checkable

A rate used in a payment thins out into a bare number the instant it is handed over. Layer proof of origin, point in time, and freshness onto it, and the rate survives the payment as a fact that can still be checked. Instead of waiting on a query, the receiving side checks it on the spot. Every payment, with a basis you can check.
