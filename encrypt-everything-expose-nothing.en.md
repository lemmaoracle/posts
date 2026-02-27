---
slug: "encrypt-everything-expose-nothing"
date: "2026.02.28"
category: "Guides"
section: "Essays"
title: "Encrypt Everything, Expose Nothing"
abstract: "How Lemma keeps every document AES-GCM encrypted so your AI never touches raw PII — only docHash and CID are exposed as stable anchors for provenance."
---

## The Problem with Raw Data in AI Pipelines

Most AI systems ingest documents in plaintext. The moment a PDF, JSON record, or API response enters a RAG pipeline, every field — names, addresses, salaries, diagnoses — becomes readable by the model, the embedding store, and anyone with database access.

This is not a theoretical risk. A single misconfigured vector store can expose millions of records. Encryption at rest does not help when the application layer decrypts everything before indexing.

## How Lemma Encrypts Documents

Lemma encrypts every document **before** it enters the system, and never decrypts it.

1. The Holder's public key is obtained (derived from a DID or wallet).
2. A shared key `K_doc` is derived via ECDH + HKDF as a hybrid encryption key.
3. The raw document is encrypted with AES-GCM to produce `encryptedDoc`.
4. The encrypted blob is stored off-chain on IPFS or Ceramic, yielding a `cid`.
5. A `docHash = SHA3-256(encryptedDoc)` is computed and used as the on-chain primary key.

From this point forward, Lemma handles only two identifiers: `docHash` and `cid`. The plaintext is never reconstructed on the server side.

```typescript
import { encrypt } from "@lemmaoracle/sdk";

const enc = await encrypt(client, {
  payload: rawDoc,
  holderKey: holderPubKey,
});
// enc.docHash → on-chain primary key
// enc.cid    → IPFS/Ceramic storage reference
```

## What AI Actually Sees

An AI agent querying Lemma never receives raw fields. Instead, it receives **verified attributes** — typed, proven facts with full provenance metadata. The raw document stays encrypted; the AI works with the output of ZK proofs and selective disclosure.

This means your RAG pipeline can answer "is this user over 18?" without ever seeing their date of birth, address, or government ID number.

## Why docHash and CID Are Enough

`docHash` is a cryptographic commitment to the encrypted content. If anyone tampers with the stored document, the hash will not match. `cid` is a content-addressed identifier that lets authorized parties locate and decrypt the original — but only if they hold the correct key.

Together, these two values give Lemma everything it needs to:

- Anchor the document's existence on-chain.
- Link ZK proofs and selective disclosure back to a specific document.
- Allow authorized holders to retrieve and decrypt when needed.

No raw content ever crosses the trust boundary.

## When to Use This

Encrypt-first is the default in Lemma — you do not opt into it. Every call to `encrypt()` produces a `docHash`/`cid` pair, and every subsequent operation (commit, prove, disclose, register) references these identifiers rather than the plaintext. If your application handles PII, financial records, medical data, or any document that should not be readable by intermediaries, this is the foundation everything else builds on.
