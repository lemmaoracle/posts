---
slug: "mcp-tool-call-audit-trail"
date: "2026.08.26"
category: "Technical"
audience: technical
section: "Essays"
title: "An audit trail for MCP tool calls that anyone can check afterwards"
abstract: "In August 2026 a CVSS 9.1 deserialization RCE landed in Splunk MCP Server (CVE-2026-76404). Its precondition is a user holding the Splunk admin role — authentication passed, role checks passed, and arbitrary OS commands ran anyway. OAuth and RBAC decide who gets to call what. Neither says anything about whether the record of what was called is genuine. Here is how to register each MCP tool call with a commitment and let a third party verify it without an API key, in code that was actually run."
tags:
  - mcp
  - audit-trail
  - provenance
  - zk-proof
  - verifiable-ai
relatedLinks:
  - label: "Lemma Dashboard — a five-minute quick start"
    href: "/blog/dashboard-quickstart/"
  - label: "@lemmaoracle/sdk (npm)"
    href: "https://www.npmjs.com/package/@lemmaoracle/sdk"
  - label: "@lemmaoracle/mcp (npm)"
    href: "https://www.npmjs.com/package/@lemmaoracle/mcp"
  - label: "Glossary"
    href: "/glossary/"
---

This is for engineers running MCP servers in production. The goal: register every MCP tool call with a commitment, and put a third party in a position to check that record later without holding an API key. Every output shown below came from an actual run — the one exception is step 5, the handler wiring, which is a skeleton you adapt to your own MCP server.

## What just happened

Three facts, with sources.

**1. A deserialization RCE in Splunk MCP Server.** On August 19, 2026 Splunk published [SVD-2026-0808](https://advisory.splunk.com/advisories/SVD-2026-0808), and the issue was assigned [CVE-2026-76404](https://nvd.nist.gov/vuln/detail/CVE-2026-76404). CVSS 3.1 base score 9.1 (Critical), vector `AV:N/AC:L/PR:H/UI:N/S:C/C:H/I:H/A:H`, CWE-502 (Deserialization of Untrusted Data). The app's credential management component deserialized stored data without checking whether the content was of the expected type. Versions below 1.2.1 are affected; 1.2.1 is the fix. Splunk's own mitigation is blunt: "Turn off or remove the Splunk MCP Server app."

The part of that vector worth pausing on is `PR:H`. **This RCE requires the Splunk admin role.** Authentication passed. Role checks passed. OS commands ran anyway.

**2. A field survey of internet-facing MCP servers.** [Exposed by Design: A Dynamic Security Assessment of Internet-Facing MCP Servers at Scale](https://arxiv.org/abs/2608.00150) (Nicolás Padilla, submitted July 31, 2026) reports over 21,000 MCP server instances detectable on the public internet. Of those, 640 were confirmed as production servers, **414** were dynamically audited, and **91.8% of the audited set lacked OAuth authentication**. Across the confirmed servers, **687 tool instances** exposed shell execution with no access controls.

Mind the denominators when you quote these. The 91.8% is against the 414 servers actually audited, not against 640. And 687 counts tool instances, not servers.

**3. The transport design itself is on the table.** [MCP Dev Summit Seoul](https://events.linuxfoundation.org/mcp-dev-summit-seoul/) ran August 13–14, 2026, presented by the Agentic AI Foundation on the Linux Foundation's event platform, and the STDIO transport design became a central topic. A [piece published just before it](https://forkast.news/the-model-context-protocol-reaches-a-security-inflection-point/) frames the split cleanly: harden the protocol itself, or keep relying on developer-side workarounds.

## What OAuth and RBAC actually cover

The three signals point the same way, but the conclusion is not simply "add more authentication."

OAuth and RBAC both act **before** a call happens. Who may call, which tools are visible, which scopes are permitted. Get all of that right — and something is still left over.

After the call happens, how do you establish who called which tool, when, with what arguments? Logs. And where do those logs live? Usually in a file on the host running the MCP server.

What CVE-2026-76404 demonstrated is that arbitrary commands can run on that host. **A log file on a host where arbitrary commands ran is not evidence about that incident.** Append, delete, rewrite — all available at the same privilege level. Shipping logs elsewhere does not fix it either: the destination cannot tell whether a line was altered before it was shipped.

Access control and trail integrity are two separate problems needing two separate fixes. The first is "don't let it happen." The second is "when it does happen, leave a record that any party can independently reach the same conclusion about." This article is about the second.

## The design: one tool call, one document

The idea is small. Wrap your MCP tool handlers, and on each completed call split the result two ways.

- **Kept locally** — the substance of the call: arguments, result, actor. Encrypted, stored wherever you choose.
- **Registered with Lemma** — only hashes and commitments derived from that substance. No plaintext leaves your side.

Once registered, the document is retrievable by `docHash` from a public endpoint. An auditor without an API key can recompute the same `docHash` from the record in hand and compare. **If they differ, the record changed after registration.**

Be precise about what this buys. A commitment does not **prevent** tampering — that is access control's job. A commitment only makes tampering **detectable**. And it says nothing about whether the registered record was true to begin with. The single provable claim is: this has not changed since it was registered. An audit trail that blurs that line stops being useful exactly when you need it.

## Implementation

We use `@lemmaoracle/sdk`. It is functional in style — the client is passed as the first argument to each function, not a method receiver.

```bash
npm install @lemmaoracle/sdk @lemmaoracle/spec
```

### 1. A client, and one call to record

```ts
import {
  create,
  encrypt,
  commit,
  derivePublicKey,
  documents,
} from "@lemmaoracle/sdk";

const client = create({
  apiBase: "https://workers.lemma.workers.dev",
  apiKey: process.env.LEMMA_API_KEY,
});

// One MCP tool call, as a plain record object
const call = {
  ts: "2026-08-26T04:12:07.881Z",
  server: "splunk-mcp",
  tool: "run_saved_search",
  actor: "agent:ops-bot",
  argsHash: "0x2f1c…",
  outcome: "ok",
};
```

Whether you commit the arguments themselves or only a hash of them is an operational call. The example uses `argsHash`. If you want the full arguments covered, put them straight into `call` — either way Lemma never sees plaintext.

### 2. Encrypt, and get a docHash and cid

`encrypt` uses ECIES (secp256k1 + HKDF-SHA256 + AES-256-GCM) and returns the ciphertext along with `docHash` and `cid`. **The ciphertext comes back to you as a return value; it is not transmitted.** Where it gets stored is your decision.

```ts
// holderKey is the compressed secp256k1 public key of whoever may decrypt
const holderKey = derivePublicKey(process.env.HOLDER_PRIVATE_KEY!);

const enc = await encrypt(client, { payload: call, holderKey });
// enc.docHash, enc.cid, enc.ciphertext, enc.algorithm
```

Run locally, that gives:

```text
holderKey: 034f355bdcb7cc0af728ef3cceb9615d90684bb5b2ca5f859ab0f0b704075871aa
docHash:   0x72f12dc1029acd9a635f221ba16ead84532e8c397a65059856a2e069e696ba40
cid:       bafkreieyjosbpy4l5tyfox4weg2lxhcpt2t6uspnfhbq6zfxnyopssnx7y
algorithm: aes-256-gcm
```

`docHash` is SHA3-256 over the ciphertext; `cid` is a CIDv1-raw.

### 3. Commit field by field

`commit` is local — no network I/O. It splits the object into one leaf per field and builds a Poseidon Merkle tree.

```ts
const c = await commit(call);
// c.root, c.leaves, c.randomness, c.depth,
// c.inclusionProofs, c.leafPreimages
```

Six fields, so six leaves and a depth-3 tree:

```text
root:   0x3f8914c900818eecd291152af8561ee466dab0936d5df8e428264087ded1175
depth:  3
leaves: 6
leafPreimages[0]: {
  "name": "actor",
  "value": "agent:ops-bot",
  "nameHash":  "0x2065763bd2d9baf6b31d533e95776063c79eb49a6bb1062202199cacd225a1b6",
  "valueHash": "0x5cf6e27547601b5bed3960480b060d10d64f2f70cb163fd2bdcf0837f70f171",
  "blindingHash": "0xa99551d706a1bc1ed283adf4d44bd6abffe85e64fe419de5a0f8580705be5929"
}
```

Keeping the leaves independent matters. It leaves the door open to disclosing `actor` alone later, or proving `outcome` was `ok` without revealing the rest. Collapse the whole document into a single hash and that door closes.

`randomness` is the blinding factor, freshly generated on every `commit`. **Record the same call twice and the two roots will not match** — which is precisely why the root reveals nothing about the contents. The flip side: lose `randomness` and no verifier can recompute the root, so store it alongside the ciphertext.

### 4. Register

```ts
await documents.register(client, {
  schema: "mcp-tool-call-v1",
  docHash: enc.docHash,
  cid: enc.cid,
  issuerId: "mcp-gateway", // who recorded it (this wrapper)
  subjectId: "agent:ops-bot", // who made the call
  commitments: {
    scheme: "poseidon",
    root: c.root,
    leaves: c.leaves,
    randomness: c.randomness,
  },
  revocation: { scheme: "none", root: "0x" + "0".repeat(64) },
});
// → { status: "registered", docHash: "0x72f1…" }
```

`commit` does not return a `scheme`, so add `"poseidon"` when assembling `commitments`. `revocation` is required; if you have no notion of revoking a tool-call record, a zero value is the honest answer.

`schema` is a string and this endpoint does not check that it is registered. In production, register a schema with `schemas.register` and use its ID.

### 5. Wire it into the MCP server

Now just wrap the handlers. Note that **a failure to record does not fail the call.** An audit mechanism that costs you availability is the first thing that gets switched off, and an audit trail that has been switched off is no audit trail.

```ts
const withAuditTrail =
  (name: string, handler: Handler): Handler =>
  async (args) => {
    const startedAt = new Date().toISOString();
    const result = await handler(args);

    void recordCall({
      ts: startedAt,
      server: "splunk-mcp",
      tool: name,
      actor: currentActorId(),
      argsHash: await sha256Hex(JSON.stringify(args)),
      outcome: result.isError ? "error" : "ok",
    }).catch((e) => console.error("[audit] record failed", e));

    return result;
  };
```

`recordCall` is steps 1–4 in one function. The `void` plus `.catch` keeps a recording failure from leaking into the tool response — but do not simply swallow it. Queue failures locally and retry. An audit trail that cannot tell you how many records it dropped has the same credibility problem as the log file we started with.

### 6. The verifying side

`GET /v1/documents/:docHash` requires no authentication. You do not have to hand auditors an API key.

```bash
curl https://workers.lemma.workers.dev/v1/documents/0x72f1…
```

```json
{
  "docHash": "0x72f1…",
  "schemaId": "mcp-tool-call-v1",
  "issuerId": "mcp-gateway",
  "subjectId": "agent:ops-bot",
  "commitmentRoot": "0x3f89…",
  "status": "registered",
  "chainId": null,
  "onchainTxHash": null,
  "registeredAt": "2026-08-26T04:12:08Z"
}
```

An unknown `docHash` returns 404 (`{"error":"Document not found"}`). Registration (`POST /v1/documents`) does need a key, so calling it without one returns 401. That asymmetry — write closed, read open — is the whole point of the arrangement.

Verification runs like this: the auditor re-runs `commit` over the record they hold plus its `randomness`, and compares the resulting root against `commitmentRoot` from the API. Match means the record is unchanged since registration. No match means it changed. That is the end of the procedure — no query to Lemma about the contents, and nothing about the contents shown to Lemma.

## What this does not do yet

The boundaries, stated plainly.

**`@lemmaoracle/mcp` is read-only today.** It exposes five MCP tools, all of them queries: `lemma_query_verified_attributes`, `lemma_get_schema`, `lemma_get_circuit`, `lemma_get_generator`, `lemma_get_proof_status`. The write-side tools, `lemma_register_document` and `lemma_submit_proof`, are marked Phase 2 in the README, and the files under `packages/mcp/src/tools/` contain TODO comments and nothing else. **That is why the wrapper above uses `@lemmaoracle/sdk` directly rather than `@lemmaoracle/mcp`.** For the other direction — letting an AI agent read registered trails — `@lemmaoracle/mcp` works as-is.

**ZK predicate proofs are out of scope here.** Proving "`outcome` was `ok`" or "the call fell inside business hours" without revealing the record is reachable through `prover.prove` and `proofs.submit`, but it requires registering a circuit first. This article stops at registration and verification.

**Selective disclosure is a separate step.** BBS+ attribute-level disclosure lives in the `disclose` namespace and is not wired into the flow above. Because `commit` already produces per-field leaves, the path there stays open.

**A commitment does not make a record correct.** As stated up front, the only provable claim is that nothing changed after registration. If the wrapper writes a false value in the first place, the false value is what gets fixed in place. To bind the record to who wrote it, add `signature` (an issuer signature) alongside `issuerId` — `RegisterDocumentRequest` accepts it as an optional field.

## Try it

Go to [dashboard.lemma.workers.dev/signin](https://dashboard.lemma.workers.dev/signin) and hit **Continue with GitHub**. That provisions a scope and a first API key on the spot, no credit card. The key's secret is shown exactly once, right after it is issued — copy it then.

The **Reference** tab has a `@lemmaoracle/mcp` snippet you can paste straight into `claude_desktop_config.json`. Swap in your key for `YOUR_API_KEY` and your MCP client can read registered trails. The same tab lists the exact algorithm strings the API accepts and the supported chain IDs.

For a tour of the dashboard itself, see [Lemma Dashboard — a five-minute quick start](/blog/dashboard-quickstart/). The source of truth for function names and payload shapes is the [`@lemmaoracle/sdk` README](https://www.npmjs.com/package/@lemmaoracle/sdk).

Pick one MCP server and wrap one tool. Once a single call is registered and you can pull its `docHash` without an API key, you have checked the claim this article makes.
