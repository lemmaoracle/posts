---
slug: "mcp-tool-call-audit-trail"
date: "2026.08.26"
category: "Technical"
audience: technical
section: "Essays"
title: "An audit trail for MCP tool calls that anyone can check afterwards"
abstract: "In August 2026 a CVSS 9.1 deserialization RCE landed in Splunk MCP Server (CVE-2026-76404). Its precondition is the Splunk admin role — authentication passed, role checks passed, and arbitrary OS commands ran anyway. OAuth and RBAC stop working the moment a call begins, and whether the record left behind is genuine is a separate problem. Here is how to register each MCP tool call with a commitment and let a third party verify it without an API key, in code that was actually run."
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

Register every MCP tool call with a commitment, and put a third party in a position to check that record later without holding an API key. Every output below came from an actual run. The one exception is step 5, the handler wiring, which is a skeleton you adapt to your own MCP server.

## CVE-2026-76404, and the state of public MCP servers

On August 19, 2026 Splunk published [SVD-2026-0808](https://advisory.splunk.com/advisories/SVD-2026-0808), and the issue was assigned [CVE-2026-76404](https://nvd.nist.gov/vuln/detail/CVE-2026-76404). CVSS 3.1 base score 9.1 (Critical), vector `AV:N/AC:L/PR:H/UI:N/S:C/C:H/I:H/A:H`, CWE-502 (Deserialization of Untrusted Data). The app's credential management component deserialized stored data without checking whether the content was of the expected type. Versions below 1.2.1 are affected; 1.2.1 is the fix. Splunk's own mitigation is blunt: "Turn off or remove the Splunk MCP Server app."

Look at `PR:H` in that vector. This RCE requires the Splunk admin role. Authentication passed. Role checks passed. OS commands ran anyway.

The wider population has come into focus too. [Exposed by Design: A Dynamic Security Assessment of Internet-Facing MCP Servers at Scale](https://arxiv.org/abs/2608.00150) (Nicolás Padilla, submitted July 31, 2026) reports over 21,000 MCP server instances detectable on the public internet. Of those, 640 were confirmed as production servers, 414 were dynamically audited, and 91.8% of the audited set lacked OAuth authentication. Across the confirmed servers, 687 tool instances expose shell execution with no access controls. That 91.8% is against the 414 servers actually audited, not against 640, and 687 counts tool instances rather than servers.

The protocol itself is moving as well. [MCP Dev Summit Seoul](https://events.linuxfoundation.org/mcp-dev-summit-seoul/) ran August 13–14, 2026, presented by the Agentic AI Foundation on the Linux Foundation's event platform, and the STDIO transport design became a central topic. A [piece published just before it](https://forkast.news/the-model-context-protocol-reaches-a-security-inflection-point/) frames the split cleanly: harden the protocol itself, or keep relying on developer-side workarounds.

## OAuth and RBAC stop at the moment of the call

All three point the same way, but the conclusion is not simply "add more authentication."

OAuth and RBAC both act before a call happens. Who may call, which tools are visible, which scopes are permitted. Get all of that right, and something is still left over.

After the call finishes, the place you go to establish who called which tool, when, and with what arguments is usually a log file on the host running the MCP server. And what CVE-2026-76404 demonstrated is that arbitrary commands can run on that host. A log file on a host where arbitrary commands ran is not evidence about that incident. Append, delete, rewrite — all available at the same privilege level. Shipping logs elsewhere does not fix it either: the destination cannot tell whether a line was altered before it was shipped.

Access control and trail integrity are two separate problems needing two separate fixes. The first is "don't let it happen." The second is "when it does happen, leave a record that any party can independently reach the same conclusion about." This article is about the second.

## The design — one tool call, one document

Wrap your MCP tool handlers, and on each completed call split the result two ways.

- Kept locally — the substance of the call (arguments, result, actor), and the blinding factor used for the commitment.
- Registered with Lemma — only hashes and commitments derived from that substance. No plaintext leaves your side.

Verification runs against the **commitment root**. An auditor recomputes the root from the record they hold plus the blinding factor, and compares it with the `commitmentRoot` registered in Lemma. If they differ, the record changed after registration.

`docHash` cannot do this job. It is the SHA3-256 of the ciphertext, and encryption draws a fresh ephemeral key and IV every time, so the same plaintext yields a different value on every run. Treat `docHash` as the identifier you look a document up by, never as the thing you compare contents against.

A commitment does not prevent tampering — that is access control's job. It only makes tampering detectable. It also says nothing about whether the registered record was true to begin with. The single provable claim is that the value has not changed since it was registered, and an audit trail that blurs that line stops being useful exactly when you need it.

## Implementation

We use `@lemmaoracle/sdk`. It is functional in style — the client is passed as the first argument to each function, not a method receiver.

```bash
npm install @lemmaoracle/sdk @lemmaoracle/spec
```

### 1. Build the record

```ts
import {
  create,
  encrypt,
  commitDeep,
  derivePublicKey,
  documents,
} from "@lemmaoracle/sdk";

const client = create({
  apiBase: "https://workers.lemma.workers.dev",
  apiKey: process.env.LEMMA_API_KEY,
});

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

`encrypt` uses ECIES (secp256k1 + HKDF-SHA256 + AES-256-GCM) and returns the ciphertext along with `docHash` and `cid`. The ciphertext comes back to you as a return value; it is not transmitted. Where it gets stored is your decision.

```ts
const holderKey = derivePublicKey(process.env.HOLDER_PRIVATE_KEY!);
const enc = await encrypt(client, { payload: call, holderKey });
// enc.docHash, enc.cid, enc.ciphertext, enc.algorithm
```

```text
docHash:   0x071d25a15f5e6771be6ced522f44b6590409586566bfa1347e348979d4d63bef
cid:       bafkreifvzcon6xhespbxbpltrn745k6zzbjwt5t5fzokygbbinhnceo3jy
algorithm: aes-256-gcm
```

`docHash` is SHA3-256 over the ciphertext; `cid` is a CIDv1-raw. As noted above, both change between runs on identical input.

### 3. Build a commitment you can reproduce

`commitDeep` is local — no network I/O. It splits the object into one leaf per JSON path and builds a Poseidon Merkle tree.

Generate the blinding factor yourself and pass it in. The SDK also ships `commit(value)`, but that one generates the factor internally with no way to supply it, so the root can never be recomputed afterwards. Use `commitDeep` for audit trails.

```ts
import crypto from "node:crypto";

// 31 bytes — 32 could exceed the BN254 scalar field
const randomness = crypto.randomBytes(31).toString("hex");

const c = commitDeep(call, { randomness });
// c.root, c.leaves, c.randomness, c.depth,
// c.inclusionProofs, c.leafPreimages
```

```text
root:   0x16466efede767e5c6d584d863a8947a50ad98c99ea3496169e4b3277129b76ad
depth:  3
leaves: 6
leafPreimages[0]: {
  "name": "$[\"actor\"]",
  "value": "s:agent:ops-bot",
  "nameHash":  "0xd03b3c2c24930303554989293dc25ef490dfea2e5afe635c7511c40b58ae9d7",
  "valueHash": "0x16cc9247c92fd4b89531aed54174e4ebb6164afb1ebccbe6c77b56564134b42d",
  "blindingHash": "0x3d9f0a51c7b48e26f1a05d3c8b7e42906fd15a83c26b4e0197df3a5c8b1e64"
}
```

Leaf names are JSON paths like `$["actor"]` so that nested values fall out of the same rule. Keeping the leaves independent matters too: it leaves the door open to disclosing `actor` alone later, or proving `outcome` was `ok` without revealing the rest. Collapse the whole document into a single hash and that door closes.

Lose `randomness` and the root can never be recomputed, which costs you the trail. Store it with the ciphertext, at the same durability.

### 4. Register with Lemma

```ts
await documents.register(client, {
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
// → { status: "registered", docHash: "0x071d…" }
```

`commitDeep` does not return a `scheme`, so add `"poseidon"` when assembling `commitments`. `revocation` is required; if you have no notion of revoking a tool-call record, a zero value is the honest answer.

`schema` is optional. Omit it and the server stores the document under the registered `passthrough-v1` schema — the input normalized to itself, which is what you want for a plain audit record. Pass a schema ID only when the document needs typed normalization; then register that schema with `schemas.register` first and use its ID.

### 5. Wire it into the tool handler

Record in a `finally`. A call that threw is exactly the one you will want to trace later. A failure to record never propagates into the tool response — an audit mechanism that costs you availability is the first thing that gets switched off.

```ts
const withAuditTrail =
  (name: string, handler: Handler): Handler =>
  async (args) => {
    const startedAt = new Date().toISOString();
    const argsHash = await sha256Hex(JSON.stringify(args));
    let outcome = "error";

    try {
      const result = await handler(args);
      outcome = result.isError ? "error" : "ok";
      return result;
    } finally {
      void recordCall({
        ts: startedAt,
        server: "splunk-mcp",
        tool: name,
        actor: currentActorId(),
        argsHash,
        outcome,
      }).catch((e) => console.error("[audit] record failed", e));
    }
  };
```

`recordCall` is steps 1–4 in one function, and it costs one round trip to Lemma per call. On a busy server, push records onto a local queue and send them in batches — the same queue covers retries. An audit trail that cannot tell you how many records it dropped has the same credibility problem as the log file we started with.

### 6. A third party checks it

`GET /v1/documents/:docHash` requires no authentication. You do not have to hand auditors an API key.

```bash
curl https://workers.lemma.workers.dev/v1/documents/0x071d…
```

```json
{
  "docHash": "0x071d…",
  "schemaId": "passthrough-v1",
  "issuerId": "mcp-gateway",
  "subjectId": "agent:ops-bot",
  "commitmentRoot": "0x1646…",
  "status": "registered",
  "chainId": null,
  "onchainTxHash": null,
  "registeredAt": "2026-08-26T04:12:08Z"
}
```

An unknown `docHash` returns 404 (`{"error":"Document not found"}`). Registration (`POST /v1/documents`) does need a key, so calling it without one returns 401. That asymmetry — write closed, read open — is the whole point of the arrangement.

The auditor re-runs `commitDeep` over the record in hand with the stored `randomness`, and compares against `commitmentRoot`.

```ts
const recomputed = commitDeep(recordInHand, { randomness: storedRandomness });
const intact = recomputed.root === res.commitmentRoot;
```

Match means the record is unchanged since registration. No match means it changed. That is the end of the procedure — no query to Lemma about the contents, and nothing about the contents shown to Lemma.

Because the record is encrypted, only someone who can decrypt it can run that check. To hand the trail to an outside auditor, either include their public key in `holderKey` or keep separate keys for recording and for custody.

`status` has three values. `registered` means recorded in Lemma (off-chain), `anchored` means a confirmed on-chain transaction exists, `pending` means an on-chain write was intended but no transaction is confirmed yet. Nothing is reported as `anchored` without a transaction anyone can go look at.

## What this does not do yet

`@lemmaoracle/mcp` is read-only today. It exposes five MCP tools, all of them queries: `lemma_query_verified_attributes`, `lemma_get_schema`, `lemma_get_circuit`, `lemma_get_generator`, `lemma_get_proof_status`. The write-side tools, `lemma_register_document` and `lemma_submit_proof`, are marked Phase 2 in the README, and the files under `packages/mcp/src/tools/` contain TODO comments and nothing else. That is why the wrapper above uses `@lemmaoracle/sdk` directly. For the other direction — letting an AI agent read registered trails — `@lemmaoracle/mcp` works as-is.

ZK predicate proofs are out of scope here. Proving "`outcome` was `ok`" or "the call fell inside business hours" without revealing the record is reachable through `prover.prove` and `proofs.submit`, but it requires registering a circuit first. This article stops at registration and verification.

Selective disclosure is a separate step. BBS+ attribute-level disclosure lives in the `disclose` namespace and is not wired into the flow above. Because `commitDeep` already produces per-path leaves, the path there stays open.

A commitment does not make a record correct. The only provable claim is that nothing changed after registration. If the wrapper writes a false value in the first place, the false value is what gets fixed in place. To bind the record to who wrote it, add `signature` (an issuer signature) alongside `issuerId` — `RegisterDocumentRequest` accepts it as an optional field.

## Try it locally

Go to [dashboard.lemma.workers.dev/signin](https://dashboard.lemma.workers.dev/signin) and hit Continue with GitHub. That provisions a scope and a first API key on the spot, no credit card. The key's secret is shown exactly once, right after it is issued — copy it then.

The Reference tab has a `@lemmaoracle/mcp` snippet you can paste straight into `claude_desktop_config.json`. Swap in your key for `YOUR_API_KEY` and your MCP client can read registered trails. The same tab lists the exact algorithm strings the API accepts and the supported chain IDs.

For a tour of the dashboard itself, see [Lemma Dashboard — a five-minute quick start](/blog/dashboard-quickstart/). The source of truth for function names and payload shapes is the [`@lemmaoracle/sdk` README](https://www.npmjs.com/package/@lemmaoracle/sdk).

Pick one MCP server and wrap one tool. Once a single call is registered and you can recompute its root locally, you have checked the claim this article makes.
