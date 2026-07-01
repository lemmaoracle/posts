---
slug: "gemma4-verifiable-claim-check"
date: "2026.06.30"
category: "Announcement"
section: "Essays"
cover: "assets/cover-announcements.png"
title: "Verifiable Claim-Check: pairing on-device Gemma 4 with cryptographic model attestation"
abstract: "For Google's Gemma 4 hackathon (Safety & Trust track), we built example-claim-check — an open-source reference implementation that binds every AI verdict to the exact model that produced it. Swap the weights and the proof breaks, visibly. A reference for Verifiable AI."
tags:
  - gemma
  - verifiable-ai
  - ollama
  - model-attestation
  - provenance
  - zero-knowledge-proof
  - open-source
  - hackathon
---

**TL;DR**
For Google's Gemma 4 hackathon, we built `example-claim-check` — an open-source (Apache 2.0) reference implementation that does one thing precisely: it binds an AI-generated verdict to the specific model that produced it. If the local model is swapped out — even subtly — the cryptographic check breaks immediately and visibly. The pipeline is deliberately small: read the model digest, hash the claim, canonicalise the payload, and call Lemma. No heavyweight crypto client-side. This post is the announcement and walkthrough.

---

## The problem: AI outputs are taken on faith

AI models produce millions of claims a day — summaries, interpretations, analyses. But how do you know the model that produced an output is the one you think it is, and that its weights weren't tampered with after it was vetted?

Today, mostly, you can't. The output is taken on trust.

That gap is not hypothetical. Model supply-chain tampering and post-vetting substitution are documented threat vectors, and they grow as AI gets deployed into places where the end user has no way to independently check what the model told them — a journalist fact-checking under bandwidth constraints, a clinic, a field office.

It is also the same structural weakness behind a recent, widely-reported incident in which a malicious agent "skill" passed every security scanner and reached tens of thousands of agents: the artifact is checked once, but what it points to can change afterward. Trust signals like a clean scan or a star count describe a past moment, not the artifact running right now.

## What we built

`example-claim-check` runs a small, legible pipeline:

```
[Claim input]
   ↓
[Model attestation] — read the model's content-addressed manifest digest from
   Ollama's /api/tags, compare against a pinned known-good digest
   ↓ (match)
[Gemma 4 inference] — local, JSON-constrained output via Ollama
   ↓
[Proof binding] — SHA-256 over canonical(modelDigest, claimHash, outputHash, nonce, ts)
   ↓
[Lemma submission] — register the binding via the Lemma documents API
   ↓
[Verdict] — ✔ VERIFIED · ✘ TAMPERED · ! UNVERIFIED
```

The on-device part is deliberately small: read the model digest, hash the claim, canonicalise the payload, and call Lemma. The proof-side work is delegated to the Lemma workers API — the client depends on no cryptographic primitive package, just HTTP.

The core idea is **re-verify at use-time, not just at vetting-time**. Attestation runs before every inference, comparing the live model's digest to a pinned known-good value. That is exactly the property the "scanned once" failure mode lacks.

## Why Gemma 4 fits

Gemma 4 (by Google DeepMind) ships as open weights under Apache 2.0, with day-one Ollama support — and that openness is what makes this work:

- **Content-addressed, verifiable identity.** Because the weights are openly distributed, Ollama exposes a content-addressed manifest digest that is identical on any machine that pulled the same version. That digest is what we pin and re-check — the anchor of the whole trust chain.
- **On-device, local-first.** The pipeline runs locally via Ollama; claims and sources need not leave the device. Trust verification and local inference turn out to be complementary, not competing, priorities.
- **Multimodal, capable inference.** Gemma 4's reasoning and multimodal input let the same pipeline handle real-world claims; the attestation layer rides on top without changing how the model is used.

## The WOW moment: when trust breaks

The demo's point is what happens when trust is violated:

```
pnpm dev           # 1. run a claim → ✔ VERIFIED
pnpm tamper        # 2. flip the expected digest (simulated supply-chain tamper)
pnpm dev           # 3. same claim → ✘ TAMPERED, in red
pnpm untamper      # 4. restore trust
```

The tamper script touches no model weights on disk — it overrides the expected digest, reproducing exactly what a real substitution would do to the verdict. The checkmark flipping from green to red is the abstract threat made tangible.

The same hash-compare primitive runs a second mode — attribute attestation for KYC / DeFi compliance (`--mode attribute`, or `--mode both`) — so one primitive demonstrably covers two domains: AI trust and verifiable compliance.

## What it does not do (yet)

We are deliberate about scope:

- **Not a security product, and not "prevention."** This detects model substitution and binds outputs to a verified model; it does not prevent attacks or replace scanners. It is a verification / provenance layer.
- **Edge proving is future work.** Today the proof step registers a binding via the Lemma documents API; full on-device zero-knowledge proving is an active research direction, not a shipped feature.
- **Weight integrity only.** Attestation detects post-training tampering, not training-data bias or the provenance of the sources a model reasons over. Those are separate, complementary problems.

## One primitive, many domains

The trust layer here is not domain-specific. The same digest-binding primitive that proves "this output came from verified weights" extends to:

- **DeFi / stablecoin compliance** — verifiable attribute proofs for regimes like MiCA and the US PPSI framework, without exposing personal data on-chain.
- **Agent payments** — proving agent identity and authorization before autonomous transactions.
- **Software / artifact provenance** — re-verifying that a skill, model, or pipeline matches a known-good reference at the moment it runs — the direct counter to "scanned once, trusted forever."

We started with AI claim-checking because the trust problem is intuitive and the impact is immediate. The infrastructure underneath is a general-purpose verifiable trust layer — for AI, for finance, for any domain where "prove it" beats "trust me."

---

**Try it**

- Repository: [example-claim-check](https://github.com/lemmaoracle/example-claim-check) (Apache 2.0)
- [Talk to us](https://tally.so/r/Pd2Rl5) about verifiable AI in your stack

*Gemma and Gemma 4 are trademarks of Google LLC / Google DeepMind, used here descriptively to identify the model; this project is independent and not endorsed by or affiliated with Google. Built by FRAME00 / Lemma.*
