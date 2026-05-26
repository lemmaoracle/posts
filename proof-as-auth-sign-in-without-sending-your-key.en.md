---
slug: "proof-as-auth-sign-in-without-sending-your-key"
title: "Proof-as-Auth: Sign In Without Sending Your Key"
date: "2026.05.25"
category: "Technical"
section: "Essays"
abstract: "Every conventional auth flow has one inescapable step: at some point, the secret crosses the wire. Bearer tokens, refresh tokens, hashed passwords — the server receives something it must store and protect. Seal proof authentication breaks that assumption. The key never leaves the browser. What the server receives is a zero-knowledge proof of key possession — unforgeable, nonce-bound, and impossible to replay. The key's hash never appears on the wire either."
tags:
  - zk-proof
  - authentication
  - seal
  - verifiable-ai
  - provenance
cover: "assets/fWD2aenS5Xo.jpg"
---

**TL;DR**

Every conventional auth flow has one inescapable step: at some point, the secret crosses the wire. Bearer tokens, refresh tokens, hashed passwords — the server receives something it must store and protect. Seal proof authentication breaks that assumption. The key never leaves the browser. What the server receives is a zero-knowledge proof of key possession — unforgeable, nonce-bound, and impossible to replay. The key's hash never appears on the wire either.

---

## "If I never send my key, how does the server know it's me?"

The intuition behind the question is reasonable. We've all been taught that authentication works by sending something the server recognizes: a password, a token, a cookie. The server checks what it received against what it stored.

Seal proof authentication works differently. The browser runs a Groth16 zero-knowledge circuit on your key, producing a *proof* and a short *nullifier* — a value unique to this key and this session, but revealing nothing about the key itself or even its hash. The server verifies the proof and confirms identity through the nullifier. If it matches, you're in.

The proof and nullifier arrive at the server. The key does not. The key's hash does not either.

---

## What actually happens in the browser

When you click "Sign in with a Seal proof" on the Lemma Dashboard, three things happen in sequence:

**1. Challenge fetch.** The server issues a random nonce and wraps it in a short-lived signed token (5-minute TTL). This nonce is what binds the proof to this specific sign-in attempt — and what makes the nullifier unique to this session.

**2. Proof generation.** Your API key is expanded into 512 bits (64 ASCII bytes). The `seal-identity-v1` Groth16 circuit running in your browser via WebAssembly takes those bits as a *private* input alongside the nonce as a *public* input. Internally, the circuit:

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
  circuitId: "seal-identity-v1",
  witness,
});
```

**What the proof actually convinces the server of.** The resulting proof simultaneously establishes three claims:

1. **Proof of knowledge.** "I know a 512-bit `keyBits` such that `SHA-256(keyBits)` is a registered hash." The secret input exists and is known to the prover; its value is hidden.

2. **Proof of computation.** "I correctly computed `nullifier = Poseidon(SHA-256(keyBits)_hi, SHA-256(keyBits)_lo, nonce)`." The intermediate chain — SHA-256, 128-bit split, Poseidon — was executed correctly, attested only through the public outputs. The server trusts the computation without seeing any intermediate value.

3. **Proof of freshness.** "This proof was generated for *this* nonce, not replayed." The `nonce * nonce` constraint binds the nonce into the circuit. A proof captured from a previous sign-in cannot be submitted against a different challenge.

Taken together, the proof says: *"I hold the secret for a registered identity, and I just produced a one-shot nullifier for this challenge."* The server never learns which identity, only that it exists in its registry.

**3. Proof submission.** The proof and two public signals (`nullifier`, `nonce`) are sent to the server. The server verifies the proof's mathematical validity, confirms identity through the nullifier, and issues a session cookie.

The network log shows: one POST to `/api/auth/seal` carrying `proof`, `publicSignals` (two values), and a `token`. No API key. No key hash.

---

## How this compares to access tokens, refresh tokens, and Passkeys

**What the server stores.** An access token (also called a Bearer token) system stores (or can reconstruct) the secret itself. A refresh-token system keeps a long-lived token server-side. Passkeys (WebAuthn) keep the key on the client device, and the server registers only the public key. Seal proof auth also keeps the key on the client, like a Passkey, but the server registers only the key's hash — not a public key. All of these carry some risk from database breaches or key theft. With Seal, however, only the nullifier crosses the wire during authentication — not the key, not even its hash.

**Replay resistance.** A leaked access token works until it expires. A leaked Seal proof is inert. The nullifier is uniquely bound to the nonce issued for this session. The same key producing two proofs for two different sessions yields two completely unrelated nullifiers. There is no correlation surface. (Passkeys share this property: each challenge is unique, so replays fail.)

**Where the secret lives.** With access tokens, the secret (the token) is sent to the server. With both Passkeys and Seal proofs, the secret never leaves the device or browser. The distinction is that Passkeys rely on hardware (security keys, biometrics) while Seal relies on a software key (an API key). That makes Seal applicable to agent auth and M2M (system-to-system) auth as well.

**The more fundamental difference.** In an access token flow, trust is established by *possession of the secret*. With a Passkey, it is *possession of the private key*. In a Seal proof flow, trust is established by *proof of knowledge of the pre-image of a hash* — where the hash itself is also hidden behind a Poseidon commitment. The secret participates in the proof circuit but never appears in the output. The hash participates in the nullifier derivation but also never appears in the output. A server that cannot see your key or its hash cannot leak either.

---

## What the nullifier guarantees

The nullifier is unique to this key and this session's nonce. The same key used in two different sessions produces two completely unrelated nullifiers — there is no way to correlate them. The server confirms identity through the nullifier without ever seeing the key or its hash.

The server does a little more work per sign-in; in exchange, the key hash never appears anywhere outside the circuit.

---

## A simple use case

You are building a developer tool where users authenticate with Lemma API keys. You want to support sign-in without your backend ever receiving or storing the raw key — or even its hash, which would let you fingerprint returning users across sessions.

With Seal proof auth, the sign-in flow is fully client-side up to the proof submission. Users enter their key locally, the circuit generates a nullifier bound to the current session nonce, and your backend verifies the proof and resolves the account by scanning hashes. Each session produces a different nullifier, so even your own logs cannot correlate sign-ins for the same key across time.

The same principle applies to AI agents signing themselves into sessions. The agent holds the raw key; it generates the proof; the orchestrator layer stores only hashes and receives only nullifiers.

---

## A note on the Dashboard

Lemma Dashboard at [dashboard.lemma.workers.dev](https://dashboard.lemma.workers.dev/signin) is available as a preview release. The Seal proof sign-in is the secondary path — first-time sign-in requires GitHub OAuth to bootstrap the account and issue the first API key. Once you have a key, subsequent sign-ins can use the Seal proof path entirely. The circuit snippet shown above is the verbatim code the Dashboard renders as a preview of what runs in your browser.

---

## What the server never touches

The server receives `proof` and public signals (`nullifier`, `nonce`). It verifies the proof's validity, confirms identity through the nullifier, and issues a session.

At no point does the server see, transmit, store, or compare your API key or its SHA-256 hash. The key is processed in the browser and the hash is handled as an intermediate value in the circuit. The proof verifies identity, and the nullifier ensures uniqueness per session.
