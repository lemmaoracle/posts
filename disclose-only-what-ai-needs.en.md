---
slug: "disclose-only-what-ai-needs"
date: "2026.02.28"
category: "Guides"
section: "Essays"
title: "Disclose Only What AI Needs"
abstract: "Selective disclosure lets holders reveal just the attributes your model requires, while the link to the original issuer signature stays intact."
---

## The Selective Disclosure Problem

A signed document contains many fields. A KYC record might include name, date of birth, address, nationality, ID number, and verification status. But a lending protocol only needs to know "is this person verified and not sanctioned?" — not their home address.

Without selective disclosure, you face a binary choice: share everything or share nothing. Lemma introduces a third option: share exactly the fields that matter, with cryptographic proof that they belong to the original signed document.

## How BBS+ Selective Disclosure Works

Lemma uses BBS+ signatures as the default selective disclosure mechanism. The SDK's `disclose` module provides a complete BBS+ implementation:

**Key generation and message preparation:**
```typescript
import { disclose } from "@lemmaoracle/sdk";

// Generate BBS+ key pair
const kp = await disclose.generateKeyPair();

// Convert payload to sorted "key:value" messages
const messages = disclose.payloadToMessages({ age: 25, name: "Alice", country: "JP" });
// → ["age:25", "country:JP", "name:Alice"]
```

**Issuer signs the full document:**
```typescript
const signed = await disclose.sign(client, {
  messages,
  secretKey: kp.secretKey,
  header: new TextEncoder().encode("my-app-header"),
  issuerId: "issuer-1",
});
// signed.signature → BBS+ signature
```

**Holder reveals only selected attributes:**
```typescript
const revealed = await disclose.reveal(client, {
  signature: signed.signature,
  messages,
  publicKey: signed.publicKey,
  disclosedIndexes: [0], // index of "age:25" in messages array
  header: signed.header,
});
// revealed.disclosed → { age: "25" }
// revealed.proof → BBS+ selective disclosure proof

// Wrap for spec compliance
const sd = disclose.toSelectiveDisclosure(revealed);
// sd.format → "bbs+", sd.disclosedAttributes → { age: "25" }, sd.proof → hex-encoded
```

The proof mathematically guarantees that `{ age: 25 }` is part of the document the issuer signed, without revealing any other field. The verifier never sees the full document.

## Provenance Survives Partial Views

A common concern with selective disclosure is loss of context. If you only see one field, how do you know it is trustworthy?

In Lemma, disclosed attributes carry full provenance:

- The issuer who signed the original document.
- The schema that defines the attribute's type and normalization.
- The proof that links the disclosed value to the original BBS+ signature.
- The on-chain registry entry that anchors the document's existence.

Even a single disclosed field traces back to its origin through an unbroken chain of cryptographic evidence.

## Role-Based and Party-Specific Views

Different consumers need different views of the same document:

- A lending protocol sees `{ verified: true, not_sanctioned: true }`.
- An age-gated service sees `{ age_bucket: "adult" }`.
- An internal auditor sees `{ country: "JP", verification_date: "2026-01-15" }`.

Each view is generated from the same signed document with a different `attributes` array passed to `disclose.reveal`. No separate copies of the data are created. No additional signing steps are needed.

## Future-Proof Design

The SDK's `disclose` namespace is named for the function it performs, not the cryptographic scheme it uses. The initial implementation uses BBS+, but the API is designed to support SD-JWT and other mechanisms without changing the developer-facing interface. When you call `disclose.reveal`, you are expressing intent ("show only these fields") rather than choosing a specific algorithm.

### Complete BBS+ API
The `disclose` module includes all necessary BBS+ operations:
- `generateKeyPair()`: Create BBS+ key pair (32-byte secret, 96-byte public)
- `payloadToMessages()`: Convert attribute object to sorted "key:value" array
- `sign()`: BBS+ signing with issuer secret key and header
- `verify()`: Verify BBS+ signature against issuer public key
- `reveal()`: Generate selective disclosure proof for specific message indexes
- `verifyProof()`: Verify selective disclosure proof
- `toSelectiveDisclosure()`: Wrap `RevealOutput` into spec-compliant `SelectiveDisclosure` type
