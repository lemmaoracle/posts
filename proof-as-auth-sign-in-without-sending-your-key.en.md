---
slug: proof-as-auth-sign-in-without-sending-your-key
title: "Proof-as-Auth: Sign In Without Sending Your Key"
date: "2026.05.23"
category: Engineering
section: Essays
abstract: "Every conventional auth flow has one inescapable step: at some point, the secret crosses the wire. Bearer tokens, refresh tokens, hashed passwords — the server receives something it must store and protect. Seal proof authentication breaks that assumption. The key never leaves the browser. What the server receives is a zero-knowledge proof of key possession — unforgeable, nonce-bound, and impossible to replay. The latest version goes further: even the key's hash never appears on the wire."
tags:
  - zk-proof
  - authentication
  - seal
  - verifiable-ai
  - provenance
---

**TL;DR**

Every conventional auth flow has one inescapable step: at some point, the secret crosses the wire. Bearer tokens, refresh tokens, hashed passwords — the server receives something it must store and protect. Seal proof authentication breaks that assumption. The key never leaves the browser. What the server receives is a zero-knowledge proof of key possession — unforgeable, nonce-bound, and impossible to replay. The current circuit goes further: even the key's hash never appears on the wire.

---

## The question non-technical people ask first

*"If I never send my key, how does the server know it's me?"*

The intuition behind the question is reasonable. We've all been taught that authentication works by sending something the server recognizes: a password, a token, a cookie. The server checks what it received against what it stored.

Seal proof authentication works differently. The browser runs a Groth16 zero-knowledge circuit on your key, producing a *proof* and a short *nullifier* — a single field element that is unique to this key and this session, but reveals nothing about the key itself or even its hash. The server verifies the proof, then scans its registered key hashes to find which one produces the same nullifier for this session's nonce. If it finds a match, you're in.

The proof and nullifier arrive at the server. The key does not. The key's hash does not either.

---

## What actually happens in the browser

When you click "Sign in with a Seal proof" on the Lemma Dashboard, three things happen in sequence:

**1. Challenge fetch.** The server issues a random nonce and wraps it in a short-lived signed token (5-minute TTL). This nonce is what binds the proof to this specific sign-in attempt — and what makes the nullifier unique to this session.

**2. Proof generation.** Your API key is expanded into 512 bits (64 ASCII bytes). The `seal-identity-v2` Groth16 circuit running in your browser via WebAssembly takes those bits as a *private* input alongside the nonce as a *public* input. Internally, the circuit:

- Computes `keyHash = SHA-256(keyBits)` — but keeps it as an internal signal, not a public output
- Splits the 256-bit hash into two 128-bit field elements: `keyHash_hi` and `keyHash_lo`
- Computes `nullifier = Poseidon(keyHash_hi, keyHash_lo, nonce)`

The public signals the proof produces are exactly two values: `nullifier` and `nonce`. The key bits and the SHA-256 hash never appear in any output.

The code that runs in the browser:

```ts
import { apiKeyToBits } from "@lemmaoracle/seal";
import { create, prover } from "@lemmaoracle/sdk";

const witness = {
  keyBits: apiKeyToBits(key).map(Number),
  nonce: challenge.nonce,
};

const client = create({});
const { proof, inputs } = await prover.prove(client, {
  circuitId: "seal-identity-v2",
  witness,
});
```

**3. Proof submission.** The proof and two public signals (`nullifier`, `nonce`) are sent to the server. The server verifies the Groth16 proof via the Lemma Relay (snarkjs requires browser APIs unavailable on Cloudflare Workers). Then it scans all active `api_keys` rows in D1, computing `Poseidon(keyHash_hi, keyHash_lo, nonce)` for each, until it finds one that equals the submitted nullifier. That match resolves the scope and account, and a session cookie is issued.

The network log shows: one POST to `/api/auth/seal` carrying `proof`, `publicSignals` (two values), and a `token`. No API key. No key hash.

---

## How this differs from bearer tokens and refresh tokens

**What the server stores.** A bearer-token system stores (or can reconstruct) the secret itself. A refresh-token system keeps a long-lived token server-side. In both cases, a database breach exposes credentials. With Seal proof auth, the server stores only `key_hash` — a one-way commitment. Even then, the hash never travels over the wire during authentication. An attacker who intercepts every sign-in request sees only a nullifier: a session-specific value that reveals nothing about the key and cannot be reused.

**Replay resistance.** A leaked bearer token works until it expires. A leaked Seal proof is inert. The nullifier is uniquely bound to the nonce issued for this session. The same key producing two proofs for two different sessions yields two completely unrelated nullifiers. There is no correlation surface.

**What an observer learns.** With bearer tokens, any observer of the auth request learns the token — a reusable credential. With Seal proof auth, an observer learns a nullifier that is valid only for the nonce it was bound to, which the server will not reissue. Capturing the proof transcript yields nothing actionable.

**The more fundamental difference.** In a bearer token flow, trust is established by *possession of the secret*. In a Seal proof flow, trust is established by *proof of knowledge of the pre-image of a hash* — where the hash itself is also hidden behind a Poseidon commitment. The secret participates in the proof circuit but never appears in the output. The hash participates in the nullifier derivation but also never appears in the output. A server that cannot see your key or its hash cannot leak either.

---

## The nullifier and the server-side scan

One question the new design raises: if the server can't see `keyHash`, how does it know which account you belong to?

The answer is a linear scan. The server computes `Poseidon(keyHash_hi, keyHash_lo, nonce)` for each registered key hash and compares it to the submitted nullifier. Poseidon is a ZK-friendly hash function designed for exactly this — it runs in sub-millisecond time in JavaScript (via `poseidon-lite`, a pure JS implementation that is compatible with Cloudflare Workers and matches the circomlib round constants used in the circuit). For any realistic number of registered API keys the scan completes well within a Cloudflare Worker's CPU budget.

This is the privacy-for-compute trade-off in v2: the server does a little more work per sign-in, and in exchange, the key hash never appears anywhere outside the circuit.

**Scaling the scan.** The current O(N) scan is practical for any realistic number of API keys — Poseidon runs in sub-millisecond time per key in V8, so 10,000 keys costs roughly 100 ms of CPU. If that budget ever tightens, there are several ways to reduce it, in order of implementation cost:

- **shard\_id hint (cheapest, ~20 added constraints).** Add `shard_id = keyHash_hi[0..7]` as an extra public signal. The server filters `api_keys` by that 8-bit bucket before running Poseidon, cutting the scan target to ~1/256 on average. Privacy is fully preserved — the shard reveals nothing the nullifier does not already imply.
- **Challenge-time pre-computation.** When a nonce is issued, compute expected nullifiers for all active keys upfront and cache them (keyed by challenge token, 5-minute TTL). Sign-in lookup becomes O(1). The memory cost is manageable combined with shard bucketing — only ~1/256 of keys need to be materialized per challenge.
- **Dual nullifier (O(1) index, minor privacy trade-off).** The circuit emits two outputs: a stable `lookup_nullifier = Poseidon(hi, lo, 0)` stored in the database at key-registration time, and the per-session `session_nullifier = Poseidon(hi, lo, nonce)` used in audit logs. Sign-in is a direct index lookup on `lookup_nullifier`. The trade-off: `lookup_nullifier` is constant per key, so it can correlate sign-ins if the lookup path is observed — `session_nullifier` stays uncorrelatable, but the two outputs exist on the same proof.

For most deployments the scan alone is sufficient. The shard hint is the natural first step if scale demands it.

---

## A simple use case

You are building a developer tool where users authenticate with Lemma API keys. You want to support sign-in without your backend ever receiving or storing the raw key — or even its hash, which would let you fingerprint returning users across sessions.

With Seal proof auth, the sign-in flow is fully client-side up to the proof submission. Users enter their key locally, the circuit generates a nullifier bound to the current session nonce, and your backend verifies the proof and resolves the account by scanning hashes. Each session produces a different nullifier, so even your own logs cannot correlate sign-ins for the same key across time.

The same principle applies to AI agents signing themselves into sessions. The agent holds the raw key; it generates the proof; the orchestrator layer stores only hashes and receives only nullifiers.

---

## A note on the Dashboard

Lemma Dashboard at [dashboard.lemma.workers.dev](https://dashboard.lemma.workers.dev/signin) is live in a quiet early-access state. The Seal proof sign-in is the secondary path — first-time sign-in requires GitHub OAuth to bootstrap the account and issue the first API key. Once you have a key, subsequent sign-ins can use the Seal proof path entirely. The circuit snippet shown above is the verbatim code the Dashboard renders as a preview of what runs in your browser.

---

## What the server never touches

The server receives `proof`, two public signals (`nullifier`, `nonce`), and a signed challenge token. It verifies the proof is mathematically valid. It checks the nonce in the challenge token. It scans key hashes to find the one that produces the submitted nullifier. It issues a session.

At no point does the server see, transmit, store, or compare your API key or its SHA-256 hash. The key existed on your machine. The hash lived inside the circuit. The proof proved it. The nullifier confirmed it, once, for this session only.
