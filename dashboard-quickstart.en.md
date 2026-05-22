---
slug: dashboard-quickstart
title: "Lemma Dashboard — 5-minute Quickstart"
date: "2026.05.21"
category: Guides
section: Essays
abstract: "A tour of the Lemma Dashboard at dashboard.lemma.workers.dev — sign in, mint an API key, learn what each tab does, and connect your first AI agent. Written for developers already familiar with x402 and zero-knowledge proofs."
tags:
  - zk-proof
  - verifiable-ai
  - x402
  - provenance
relatedLinks:
  - label: "Dashboard"
    href: "https://dashboard.lemma.workers.dev/dashboard"
  - label: "@lemmaoracle/sdk on npm"
    href: "https://www.npmjs.com/package/@lemmaoracle/sdk"
  - label: "Glossary"
    href: "/glossary/"
---

The Lemma Dashboard at [dashboard.lemma.workers.dev](https://dashboard.lemma.workers.dev/dashboard) is where you manage the API keys, listings, and registered resources that live under your scope. This guide walks through what each tab does so you can get oriented in five minutes. It assumes you know what a ZK proof is, what an HTTP 402 payment flow looks like, and what BBS+ selective disclosure buys you — if any of that is new, read the [Glossary](/glossary/) first.

## The 5-minute UI tour

### 1. Sign in (GitHub OAuth, first time only)

Open [dashboard.lemma.workers.dev](https://dashboard.lemma.workers.dev/signin) and click **Continue with GitHub**. The OAuth flow creates a fresh **scope** (your tenant boundary) and a first API key bound to it. The raw secret of that key is shown only once on the next screen — copy it now, or you will need to revoke and re-create.

Subsequent sign-ins can use **Seal Proof**: a Groth16 ZK proof that you hold an API key, generated client-side without sending the secret. Seal needs a key to exist already, so it cannot bootstrap a new account — that's what the GitHub path is for.

### 2. API keys — your credentials

Open the **API keys** tab. The first key you created during sign-in is already listed. Two things to know:

- **Secrets are shown once.** Hit *Create new key*, copy the secret, then leave the page. The list view from then on only shows the key name and creation timestamp — never the secret again.
- **Revoke is destructive.** Revoking immediately invalidates the key everywhere. Plan a rotation: create the new key, deploy it, then revoke the old one.

Use the key as the bearer token for any API call your SDK or HTTP client makes against the Lemma API. Keep it out of version control.

### 3. Reference — what the API accepts

Open the **Reference** tab. This is the page you will keep open while integrating. It lists, all read-only:

- **Supported chains** — chain IDs and explorer URLs for every network where on-chain verification is available.
- **Cryptographic algorithms** — the exact strings the API accepts: `groth16-bn254-snarkjs` for proofs, BBS+ over BLS12-381 for selective disclosure, ECDSA on secp256k1 for issuer signatures, ECIES (secp256k1 + HKDF-SHA256 + AES-256-GCM) for payload encryption, SHA-256 for hashing.
- **Official libraries** — links to [`@lemmaoracle/sdk`](https://www.npmjs.com/package/@lemmaoracle/sdk), [`@lemmaoracle/mcp`](https://www.npmjs.com/package/@lemmaoracle/mcp), and [`@lemmaoracle/parser`](https://www.npmjs.com/package/@lemmaoracle/parser).
- **MCP config snippet** — a ready-to-paste `claude_desktop_config.json` block. Replace `YOUR_API_KEY` with the key from step 2 and AI clients that speak MCP will see your verified attributes.

### 4. Register your first resources

Schemas, circuits, generators, documents, and proofs are not created from the Dashboard itself — the Dashboard reads them. You register them programmatically against the workers API at `https://workers.lemma.workers.dev` (the SDK's default base URL), either through the [SDK](https://www.npmjs.com/package/@lemmaoracle/sdk) or by hitting the HTTP endpoints directly. Start from the SDK README; it is the source of truth for the exact functions and payload shapes.

### 5. Overview — watch your scope populate

Once you have registered something, the **Overview** tab is where you confirm it landed. The page polls `/api/resources` every ten seconds and groups everything under your scope into five sections: Schemas, Circuits, Generators, Documents, Proofs. Each row opens a detail panel with the full record and copy-to-clipboard helpers.

### 6. Usage — see what you spend

The **Usage** tab shows API request volume for your scope. The chart is read-only and updates as requests accumulate. Useful for sanity-checking before the request count starts mattering for billing or rate limits.

That is the full UI. Anything not in those tabs is via SDK or direct API call.

## Concepts

These are the six nouns the Dashboard uses everywhere. None are vendor-specific buzzwords; they map cleanly onto the cryptography you already know. For the SDK call that registers each one, see the matching empty state in the Dashboard or the [`@lemmaoracle/sdk`](https://www.npmjs.com/package/@lemmaoracle/sdk) README.

### Scope

Your tenant boundary. Everything you register — keys, schemas, circuits, documents, proofs — lives under one scope ID. A new GitHub sign-in creates a new scope.

You do not register a scope explicitly; it is created automatically the first time you sign in through GitHub OAuth on the Dashboard. The scope ID is shown in the footer of every signed-in page.

### Schema

A typed declaration that pins what a document's attributes look like, anchored to a normalize artifact (a WASM module that hashes into the same circuit). Schemas are immutable once registered; version them in the `id` (`age-over-eighteen.v2`) rather than mutating in place.

### Circuit

A ZK circuit registered against a schema, addressable by circuit ID. Most circuits today are Groth16 on BN254 compiled with `snarkjs`. See the Reference tab for the exact algorithm strings the API accepts. Related: [Zero-Knowledge Proof](/glossary/zk-proof/).

### Generator

Metadata for a document generation script — it describes how to produce a rawDoc: inputs (`inputsSpec`), outputs (`outputsSpec`), and a source location (`source`). Execution happens on developer infrastructure, not on Lemma; the `generatorId` and its hash are treated as ZK public inputs for verification.

### Document

An issuer-signed assertion. The Dashboard stores only `docHash`, `cid`, `issuerId`, `subjectId`, and the document commitments plus revocation state — never the cleartext payload. Related: [docHash](/glossary/doc-hash/), [CID](/glossary/cid/), [Provenance](/glossary/provenance/).

### Proof

A submitted ZK proof instance against a registered circuit, optionally tied to a document. The Overview lists every proof your scope has produced along with its verification status. Related: [Selective Disclosure](/glossary/selective-disclosure/).

## Where to go next

- **Concept depth** — the [Glossary](/glossary/) introduces 27 entries on ZK, provenance, agent payments, and the regulatory layer.
- **API reference** — the [`@lemmaoracle/sdk`](https://www.npmjs.com/package/@lemmaoracle/sdk) README is the source of truth for functions, fetch helpers, and the exact payload shapes.
- **MCP for AI agents** — the Reference tab carries the [`@lemmaoracle/mcp`](https://www.npmjs.com/package/@lemmaoracle/mcp) configuration; drop it into Claude Desktop and your agent can read verified attributes.

If something is missing, please open an issue in the [docs repo](https://github.com/lemmaoracle/posts/issues).
