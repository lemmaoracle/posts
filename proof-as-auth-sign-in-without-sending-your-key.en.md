---
slug: proof-as-auth-sign-in-without-sending-your-key
title: "Proof-as-Auth: Sign In Without Sending Your Key"
date: "2026.05.23"
category: Engineering
section: Essays
abstract: "Every conventional auth flow has one inescapable step: at some point, the secret crosses the wire. Bearer tokens, refresh tokens, hashed passwords — the server receives something it must store and protect. Seal proof authentication breaks that assumption. The key never leaves the browser. What the server receives is a zero-knowledge proof of key possession — unforgeable, nonce-bound, and impossible to replay."
tags:
  - zk-proof
  - authentication
  - seal
  - verifiable-ai
  - provenance
---

**TL;DR**

Every conventional auth flow has one inescapable step: at some point, the secret crosses the wire. Bearer tokens, refresh tokens, hashed passwords — the server receives something it must store and protect. Seal proof authentication breaks that assumption. The key never leaves the browser. What the server receives is a zero-knowledge proof of key possession — unforgeable, nonce-bound, and impossible to replay.

---

## The question non-technical people ask first

*"If I never send my key, how does the server know it's me?"*

The intuition behind the question is reasonable. We've all been taught that authentication works by sending something the server recognizes: a password, a token, a cookie. The server checks what it received against what it stored.

Seal proof authentication works differently. The browser runs a Groth16 zero-knowledge circuit on your key, producing a *proof* — a short mathematical object that convinces any verifier that the prover knows a key matching a particular hash, without revealing the key itself. The hash was registered when you first created your API key. The server stores only the hash, never the raw secret.

The proof arrives at the server. The key does not.

---

## What actually happens in the browser

When you click "Sign in with a Seal proof" on the Lemma Dashboard, three things happen in sequence:

**1. Challenge fetch.** The server issues a random nonce and wraps it in a short-lived signed token (5-minute TTL). This nonce is what binds the proof to this specific sign-in attempt.

**2. Proof generation.** Your API key is expanded into 256 bits. Those bits, together with the nonce, are fed as private inputs into the `seal-identity-v1` Groth16 circuit running in your browser via WebAssembly. The circuit produces a proof and a set of public signals — the SHA-256 hash of your key bits (256 signal values) and the nonce (one more signal value). The circuit runs entirely client-side. The key bits are the witness: they are consumed by the prover and are never part of the output.

The code that runs in the browser is exactly this:

```ts
import { apiKeyToBits } from "@lemmaoracle/seal";
import { create, prover } from "@lemmaoracle/sdk";

const witness = {
  keyBits: apiKeyToBits(key).map(Number),
  nonce: challenge.nonce,
};

const client = create({});
const { proof, inputs } = await prover.prove(client, {
  circuitId: "seal-identity-v1",
  witness,
});
```

**3. Proof submission.** The proof and public signals are sent to the server. The server verifies the Groth16 proof (delegating to the Lemma Relay, since snarkjs requires APIs unavailable in Cloudflare Workers), then reads `keyHash` from the first 256 public signals. It looks up that hash in the `api_keys` table, resolves the matching scope and account, and issues a session cookie. Done.

The network log shows: one POST to `/api/auth/seal` carrying `proof`, `publicSignals`, and a `token`. No API key. No raw secret of any kind.

---

## How this differs from bearer tokens and refresh tokens

The conventional token-based auth stack has a well-understood threat model. Here is where Seal proof auth differs at the level of mechanism, not just policy.

**What the server stores.** A bearer-token system stores (or can reconstruct) the secret itself — or at minimum a form from which the secret can be verified by direct comparison. A refresh-token system keeps a long-lived token server-side and issues short-lived access tokens. In both cases, a database breach exposes the credentials it holds. With Seal proof auth, the server stores only `key_hash` — a one-way commitment. Knowing the hash does not let an attacker authenticate; they still need to produce a valid ZK proof, which requires knowing the pre-image.

**Replay resistance.** A leaked bearer token works until it expires. A leaked Seal proof is inert. Every proof is bound to the challenge nonce issued for that specific sign-in attempt. Submitting the same proof twice fails immediately: the server checks the nonce against the challenge token it issued, and it is single-use.

**The surface the server must protect.** Bearer/refresh token systems require protecting the token store — a live, queryable secret database. Seal proof auth requires protecting only the `key_hash` column. Hashes are public-input-grade data: publishing them does not enable impersonation. The key must still be rotated if compromised, but a compromised hash database alone does not compromise accounts.

**The more fundamental difference.** In a bearer token flow, trust is established by *possession of the secret*. In a Seal proof flow, trust is established by *proof of knowledge of the pre-image of a hash*. This is a different mathematical guarantee. The secret participates in the proof circuit but never appears in the output. A server that cannot see your key cannot leak it.

---

## A simple use case

You are a developer building on Lemma's API. You want to ship a dashboard for your own users where they can sign in without your app ever touching their API keys — keys that also authorize billing and data access.

With Seal proof auth, the sign-in flow is entirely client-side up to the proof submission step. You can build a form that takes the key, generates the proof in the browser, and sends only the proof to your backend. Your backend verifies the proof against the registered hash. The raw key stays on the user's machine.

The same pattern applies to agents. An AI agent that holds an API key can sign itself into a session by generating a Seal proof. The orchestrator infrastructure never needs to store the raw key — it stores the hash at registration time, and from then on accepts only proofs.

---

## A note on the Dashboard

Lemma Dashboard at [dashboard.lemma.workers.dev](https://dashboard.lemma.workers.dev/signin) is live in a quiet early-access state. The Seal proof sign-in is the secondary path — first-time sign-in requires GitHub OAuth to bootstrap the account and issue the first API key. Once you have a key, subsequent sign-ins can use the Seal proof path entirely. The code shown in the snippet above is verbatim from the sign-in screen: the Dashboard renders it as a preview of what runs in your browser.

---

## What the server never touches

To close the loop on the original question:

The server receives `proof`, `publicSignals` (keyHash + nonce), and a signed challenge token. It verifies the proof is mathematically valid. It checks that the nonce in the proof matches the challenge it issued. It looks up the keyHash against its records. It issues a session.

At no point does the server see, transmit, store, or compare your API key. The key existed on your machine. The proof proved it. That is the whole guarantee.
