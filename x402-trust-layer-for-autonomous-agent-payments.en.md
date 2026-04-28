---
slug: "x402-trust-layer-for-autonomous-agent-payments"
date: "2026.04.28"
category: "Announcement"
section: "Essays"
title: "A Trust Layer for x402"
cover: "assets/Q50vhoUkafc.jpg"
abstract: "AI agents can now pay over HTTP through x402, but a wallet address and a transaction hash do not tell the receiving server who authorized the payment, under what policy, or whether the data returned was tampered with. Today we publish the Lemma × x402 reference implementation, live on Base Sepolia: every settlement carries a ZK proof bundle inside PAYMENT-RESPONSE — issuer identity, settlement, and data integrity, independently verifiable end-to-end."
---

## The design is live

In the [whitepaper announcement](https://lemma.frame00.com/blog/whitepaper-v1-prove-ai-decisions) we previewed an ADVANCED scenario: agents that carry proofs and settle payments in a single flow. Today that scenario ships as a working reference implementation.

Repository: [github.com/lemmaoracle/example-x402](https://github.com/lemmaoracle/example-x402)  
Service: [Trust402 — ZK-Verified Agent Payments](https://lemma.frame00.com/trust402)

Live on Base Sepolia. We offer this trust layer as a service under the name **Trust402**.

![Demo: Agent fetches content → discovers attestation → pays $0.001 via x402 → receives ZK-verified attributes → selectively discloses specific fields](https://github.com/lemmaoracle/example-x402/raw/main/assets/terminal.gif)

---

## The gap x402 leaves

[x402](https://www.coinbase.com/blog/coinbase-and-cloudflare-will-launch-x402-foundation) reactivated `402 Payment Required` and turned HTTP into a payment rail an autonomous agent can speak natively. No account, no API key, no invoice form — a wallet and an HTTP request are enough. Coinbase, Cloudflare, Google Cloud, AWS, Visa, and Mastercard have aligned around it as founding members of the x402 Foundation. The infrastructure for agents-as-economic-actors is being put in place.

But x402 alone settles the payment. It does not tell the receiving server:

- **Who authorized the payment.** The payer is a wallet address. A wallet address is an anonymous primitive, not a principal.
- **Under what policy.** Role, scope, spend limit — none of these are visible to the resource.
- **Whether the data returned will arrive intact.** Once the bytes leave the server, the receiving agent has no cryptographic way to confirm that what it parses is what was settled for.

For agents-paying-agents to become an economy rather than a curiosity, those three questions must be answerable in the same HTTP round trip as the payment itself.

---

## What this adds

Every x402 settlement in this implementation now carries a ZK proof bundle inside `PAYMENT-RESPONSE`. The bundle is independently verifiable by downstream consumers — a second-leg agent, a smart contract, an auditor — without re-running the round trip.

The bundle covers three claims, with selective disclosure as a fourth available on demand.

### Issuer identity

The party that issued the verified data (the issuer) and the document subject are recorded on chain as a Poseidon Merkle commitment. A verifier can match the `issuerId` / `subjectId` returned in the response against the on-chain commitment to confirm cryptographically that the data was issued by exactly that party. Substituting the issuer after the fact, or fabricating a past issuance, is not structurally possible.

### Settlement

In the standard x402 flow, the evidence of payment is essentially the `transaction` field carrying a tx hash. Lemma's extension delivers the full settlement proof bundle in addition to the tx hash, packaged inside the `extensions.lemma` block of `PAYMENT-RESPONSE`. A downstream verifier — a second-leg agent, or a smart contract that needs to confirm receipt — can validate the payment simply by holding the response, without running the x402 round trip again.

### Data integrity

The `SHA-256` of the response body is bound at issuance to the on-chain commitment (`docHash`). The receiving agent computes `SHA-256` over the bytes it actually received and compares it to `attributes.integrity` returned in the response. A match means the bytes are identical to those committed at issuance. No round trip back to Lemma is required.

### Selective disclosure

As a fourth claim, `POST /query` provides **selective disclosure** — the ability to reveal only a chosen subset of attributes from a larger signed set, while keeping the rest cryptographically attested but invisible. We implement this with the BBS+ signature scheme.

For example, suppose a research dataset is signed with attributes like `author`, `institution`, `published`, `funding-source`, `coauthors`, and `salary`. A verifier can request disclosure of just `author` and `published`. The remaining attributes stay sealed but provably part of the original signed set. Privacy and verifiability are satisfied at the same time, instead of trading one off against the other.

Lemma further binds this disclosure to the x402 settlement via the condition `condition.circuitId = "x402-payment-v1"`. Only the party that actually executed the corresponding x402 payment can re-derive the disclosure; copying or replaying it without making the payment is structurally impossible. Payment and disclosure become two faces of the same cryptographic fact.

---

## Four phases per request

The `packages/agent/src/index.ts` reference agent walks through four phases:

1. **Free fetch.** `GET /resource` returns the data and an `X-Lemma-Attestation` header (or a `<link rel="lemma-attestation">` in HTML) pointing at the verification endpoint. Distribution is cheap; the proof is what costs `$0.001`.
2. **Hold as `UNVERIFIED`.** The agent's reasoning loop receives the bytes labeled untrusted and does not yet act on them.
3. **Pay-and-verify in one round trip.** `GET /verify/:hash` via `@x402/fetch` returns `402 Payment Required`, the agent auto-pays `0.001 USDC` on Base Sepolia, and the `200` response carries the verified attributes in the body and the ZK settlement proof in the `PAYMENT-RESPONSE` header.
4. **Integrity check.** The agent computes `SHA-256(body)`, compares it to `attributes.integrity`, and only on match transitions internal state to `VERIFIED`.

Phase 3 is the moment payment and proof become a single artifact. The `PAYMENT-RESPONSE` extension produced by `@lemmaoracle/x402` is:

```typescript
type PaymentResponseExtension = {
  transaction: string;
  network: string;
  payer?: string;
  extensions?: {
    lemma?: {
      proof: string;
      inputs: string[];
      circuitId: string;
      generatedAt: number;
    };
  };
};
```

Anyone holding the response can validate the proof against the on-chain commitment. The Lemma facilitator never needs to be consulted again.

---

## How to integrate

The trust layer is added as a single middleware on the resource server.

The `@lemmaoracle/x402` package shares the same API signature as Coinbase's `@x402/*` packages. In an existing x402 server, replacing imports from `@x402/*` with `@lemmaoracle/x402` is enough to enable ZK proof emission automatically.

```typescript
import {
  paymentMiddleware,
  x402ResourceServer,
  ExactEvmScheme,
  HTTPFacilitatorClient,
} from "@lemmaoracle/x402";
import { createFacilitatorConfig } from "@coinbase/x402";

const resourceServer = new x402ResourceServer(
  new HTTPFacilitatorClient(facilitatorConfig)
).register("eip155:84532", new ExactEvmScheme());

app.use("*", paymentMiddleware(routes, resourceServer));
```

**Route handlers stay untouched.** Business logic does not change. The `x402ResourceServer` constructor automatically registers a Lemma extension (keyed `lemma`) via `enrichSettlementResponse`. After settlement, the extension generates the ZK proof and writes it into the `extensions.lemma` block of `PAYMENT-RESPONSE` before the response headers are sent.

The agent side requires no changes either. Stock `@x402/fetch` from Coinbase is enough — read `extensions.lemma` from the `PAYMENT-RESPONSE` header and verify. No Lemma-specific client SDK is needed.

The full integration cost of the trust layer is **a single import line on the server**. Teams already operating x402 can keep their business logic as-is and add the trust layer afterwards.

---

## Today's scope: resource-side trust

This release covers the **resource-side trust layer**: the side of resource providers and data issuers. In other words, an agent can now confirm cryptographically *who issued* a piece of data, *how much it paid* for it, and that the *bytes arrived intact*.

The other half — the **agent-side trust layer**, answering *who paid, under what authority, and under what policy* — is the next milestone. In today's release the paying agent is identified by a wallet address; binding it to a `did:key`, attaching role and spend-limit attestations, all of that comes next.

We chose to ship the two layers in stages on purpose. The resource-side layer is already useful by itself: an agent operating in the existing x402 economy can extract verifiable data starting today. The agent-side layer is designed to compose independently, so adding it later does not invalidate any of the resource-side proofs already produced.

---

## The agent side, next

The agent-side trust layer aims to turn a paying agent from an anonymous wallet into a verifiable principal.

Concretely, this means identifying the agent as a `did:key` principal, expressing authority through roles and scopes, making spend limits explicit through `spendLimit`, and managing credential lifecycle (issuance and revocation) — all expressed as ZK predicates so they can be verified.

When this lands, `PAYMENT-RESPONSE` will carry an attestation along the lines of "this payment was issued by `did:key:...`, operating under `org:acme` with `payments:read` scope, within spend limit, valid and unrevoked at the moment of settlement." That is the point at which the resource-side trust layer and the agent-side trust layer compose into the unified design previewed in the whitepaper.

---

## Swap the schema, use it on any data

The reference implementation runs on a blog-article attribute schema (author, publication date, language), but that was chosen as the most legible example. The same machinery applies directly to other data types.

The four-phase flow — fetch → pay → ZK-verify — does not depend on the data itself in any way. By swapping the schema (the definition of which attributes to verify) and the circuit (the ZK proof of the verification logic), the exact same plumbing carries over to a different domain.

| Domain | Verified attributes | Use case |
| :--- | :--- | :--- |
| Credentials & qualifications | issuer / validity / scope | Agent authority verification, automated KYC/AML |
| IoT sensor data | device ID / timestamp / measurement integrity | Supply-chain tamper detection |
| Financial attestations | auditor / report date / numeric integrity | Agent-driven financial verification |
| Research data & papers | author / institution / reproducibility hash | Provenance, retraction detection |
| On-chain events | contract / block height / event payload | Cross-chain bridges, DeFi oracles |
| Autonomous procurement (dark factory) | supplier ID / standard conformance / contract integrity | Machine-to-machine procurement with audit trail |

The core capability is that a paying agent can know, cryptographically, who issued whatever it just paid for and that the bytes it received match what was settled for. That capability reaches into broad domains precisely because schema and circuit are interchangeable.

---

## x402 and MCP (α): two surfaces, one layer

In parallel, we are also publishing an **early-stage α demo** of an MCP server ([lemma/packages/mcp](https://github.com/lemmaoracle/lemma/tree/main/packages/mcp)) as a complementary surface. It is a locally installable demo — not a hosted, production service. The two share the same underlying trust layer and split responsibilities cleanly:

- **x402** is the payment rail. The Lemma augmentation is settlement proof + ZK proof bundle in `PAYMENT-RESPONSE`.
- **MCP (α demo, locally installable)** is the read interface for MCP-native agents (Claude Desktop, Cursor, and similar). It exposes verified-attribute queries, schemas, circuits, generators, and proof status as tools. It works today, but expects local installation.

x402 is already agent-callable at the protocol level, so payments do not need an MCP wrapper. MCP exists to let agents that already speak it read from Lemma without a custom REST integration.

---

## What's next

The next post in this series will cover the **agent-side trust layer** — DID binding, role / scope / spend-limit attestations — with a view of the full picture once both layers compose.

The demo runs without credentials — try it first. For teams evaluating production rollout and looking for pricing, plan details, or the whitepaper, please join the [**developer waitlist**](https://tally.so/r/kd0bZR). Waitlist members also get access to the whitepaper PDF.

---

## Links

- Repository: [lemmaoracle/example-x402](https://github.com/lemmaoracle/example-x402)
- MCP server (α demo, locally installable): [lemma/packages/mcp](https://github.com/lemmaoracle/lemma/tree/main/packages/mcp)
- Service page: [Trust402](https://lemma.frame00.com/trust402)
- Whitepaper announcement: [Prove What Your AI Decided On](https://lemma.frame00.com/blog/whitepaper-v1-prove-ai-decisions)
- Developer waitlist: [tally.so/r/kd0bZR](https://tally.so/r/kd0bZR)

---

*Built for decisions that matter.*
