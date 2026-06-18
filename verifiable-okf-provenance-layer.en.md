---
slug: "verifiable-okf-provenance-layer"
date: "2026.06.15"
category: "Announcement"
section: "Essays"
title: "From readable knowledge to verifiable knowledge — we've open-sourced the first implementation that adds provenance to OKF"
abstract: "A common format for the \"knowledge\" AI agents read — Google's Open Knowledge Format (OKF) — has arrived, letting that knowledge be shared in one shape across organizations and tools. But what OKF standardizes is the representation and sharing of knowledge; \"readable\" and \"trustworthy\" are two different layers. Who issued it, whether it has been tampered with, whether it meets the required conditions — that provenance is out of the spec's scope. Just days after OKF's release, Lemma is open-sourcing the first implementation that adds provenance to OKF. It changes the standard not at all: signing, verification, and condition proofs (Groth16) included, in a form anyone can try on their own bundles."
tags:
  - open-knowledge-format
  - verifiable-okf
  - provenance
  - ai-agents
  - did
  - zero-knowledge-proof
  - open-source
---

**TL;DR**
A common specification for sharing the "knowledge" AI agents read — in one shape across organizations and tools — has arrived (Google's OKF). But what OKF standardizes is the representation and sharing of knowledge, and "readable" is a different problem from "trustworthy." Who issued it, whether it has been tampered with, whether it meets the conditions — that provenance is out of the spec's scope. **Just days after OKF's release, Lemma is releasing the first implementation that adds provenance to OKF, as open source.** It changes the standard not at all, and opens it to the ecosystem in a form anyone can use.

Represented ≠ trusted.

---

## Introduction — separating "readable" from "trustworthy"

AI agents reading internal and external data on a person's behalf, and making judgments from it — that usage is moving into real operations. Whether an agent behaves correctly depends heavily on the quality of the "knowledge" it loads: table definitions, the meaning of metrics, business rules and runbooks.

The **Open Knowledge Format (OKF)** that Google published last week is a common specification for expressing this knowledge. Each concept is written as a single markdown file plus YAML frontmatter, so it can be handed off in one shape across organizations and tools. It requires no particular SDK or runtime and achieves interoperability with a minimal set of conventions — a well-organized design.

Here we need to separate what OKF covers from what it does not. What OKF standardizes is the **representation and sharing** of knowledge. The following three things, needed to judge whether that knowledge is **trustworthy**, are out of the spec's scope.

- **Origin** — who issued the concept. The `resource` URL is a pointer to a location, not a signature by the issuer.
- **Integrity** — whether the body has been altered since it was issued.
- **Conditions** — whether rules such as "contains no personal data," "governance-approved," or "within its validity period" can be shown to hold without disclosing the underlying data.

For knowledge used only inside a single organization, these can be assumed. But when knowledge issued by another organization or another agent is used in audit, KYC/AML, or payment decisions, compatibility of representation alone is not enough. Issuer, integrity, and conditions must be attached to the knowledge itself in a machine-verifiable form.

Lemma has implemented this layer. **It is the first implementation that adds provenance to OKF.** It changes the OKF spec not at all; we release it as an extension a producer can attach optionally, plus the tools to verify it. **It is all open source — anyone can try it on their own bundles and build it into their own tools.** Building verifiability openly on top of the open standard that is OKF — we open it as a contribution to the ecosystem.

> From here on, this explains the design and implementation, for developers.

---

## ▸ Background: layering trust onto rail and format

Over the past few weeks, the infrastructure of the agent economy has rapidly taken concrete shape. x402 extended the rail by which agents pay per API call to multiple chains (Injective, XRPL, and others), and Google Cloud showed, with OKF, a common format for agents to read the "table of contents" of enterprise data. At the same time, "Know Your Agent (KYA)" — a framework to make an agent's identity, provenance, and authority verifiable — is rising as an enterprise trend.

Looked at structurally, three layers come into view. The payment rail standardized "being able to pay," and the knowledge format standardized "being able to read." When a layer that confirms "whether that data is trustworthy" is added on top, an agent's judgments can be placed, with confidence, into audit, KYC/AML, and payment operations. **Verifiable OKF carries this third layer, on the knowledge-format side.**

In OKF's case, the moment the table of contents crosses an organizational boundary, three questions appear that the format itself cannot answer.

- **Origin** — who issued this concept. The `resource` URL is a pointer, not a signed proof.
- **Integrity** — whether the body is as issued, or was rewritten along the way.
- **Conditions** — whether rules such as "contains no personal data" / "governance-approved" / "within its validity period" can be stated to hold without disclosing the underlying data.

In regulated work such as audit, KYC/AML, and supply chains, "the file said so" is not enough.

Cryptographically valid ≠ semantically right.

---

## ▸ What we're shipping

What Lemma is releasing is **Verifiable OKF** — a **producer-side extension** that attaches provenance to OKF bundles.

The OKF spec allows producers to add arbitrary keys from the start (*"Producers may add any extra keys; consumers should preserve them and not reject documents with unknown fields."*). We use this existing extension mechanism to provide a `provenance` key, and the tools to verify signatures, integrity, and condition proofs. Producers write OKF as they always have, and optionally attach verifiable provenance on top.

Models change. Proofs remain.

What we provide is three pieces, mirroring the fact that OKF itself ships producer / consumer / visualizer reference implementations.

- **Signing producer** — takes an existing OKF bundle as-is and attaches an integrity hash and signature per concept. Producers simply write OKF as they always have.
- **Verifying consumer** — the consuming agent verifies integrity, issuer, and any required condition proofs, and returns a verified / unverified / failed state per concept.
- **Proof-status visualizer** — a single HTML file, no backend, with no data leaving the page, that shows a verification badge per concept on the bundle's graph.

---

## ▸ How it works (for developers)

Producers add an optional `provenance` key to the frontmatter. Everything inside is optional; you emit only what you can.

- Integrity is **SHA-256** over the canonicalized body (`content_hash`).
- Signing is **Ed25519**. It binds the entire issuer frontmatter and the body together, over a payload canonicalized with **JCS (RFC 8785)**. This makes it impossible to swap out just the title or just a condition proof.
- The issuer is a **did:web / did:key** DID. This functions directly as the owner-bound, verifiable identifier for **KYA (Know Your Agent)**.
- Condition proofs are pluggable. As a first one, we bundle offline verification via **Groth16 over BN254 (snarkjs)** and a demo circuit that proves "contains no personal data" (`contains_no_pii`). Verification needs only the verification key — no proving key, no circom, no network. Selective disclosure of attributes (**BBS+ over BLS12-381**) is also defined in the spec. Selective disclosure — "show only the attributes that are needed" — maps directly onto OKF's idea of "share only the knowledge that is needed."

Verification needs no network. **The core runs fully offline and depends on no particular chain or vendor.** For situations that need stronger guarantees, such as a public anchor, the design lets you add chain anchoring later as an *optional* layer, but the core functionality is complete without anchoring.

A consumer that does not understand `provenance` ignores it and reads it as plain OKF. A bundle without it is, as before, perfectly valid. **Nothing breaks.**

### What's published (runs now)

- The Verifiable OKF extension spec v0.3 (normatively specifying the `provenance` key, canonicalization, signing, and the verification algorithm) and `provenance.schema.json`
- `@verifiable-okf/canon` (body canonicalization + JCS signing-payload construction)
- `@verifiable-okf/signer` (`vokf-sign`) / `@verifiable-okf/verifier` (`vokf-verify` · Ed25519 · SHA-256 · did:web / did:key, with per-concept verification status)
- conformance fixtures (signed / tampered golden examples) and tests
- `@verifiable-okf/visualizer` (bundle → single offline HTML with a per-concept verification badge)
- `@verifiable-okf/attest-zk-groth16` (Groth16 over BN254 / snarkjs · fully offline) and the `contains_no_pii` demo circuit
- a worked example (sign → verify → generate a report for a real GA4 OKF bundle)

**It runs end to end, from signing to verification.** A separate agent — fully offline with did:key — verifying integrity, issuer, and condition proofs, and emitting a report with a per-concept verification badge, is complete.

---

## ▸ What's next

1. Add selective disclosure of attributes (BBS+ over BLS12-381) and condition-proof circuits beyond `contains_no_pii`.
2. KMS/HSM integration for production signing keys, and a Python reference implementation.
3. Interoperability with W3C VC / DID / C2PA, and a revocation model.
4. Share with the OKF community a **case report** that "we built a verification layer using the standard's extension mechanism" (with no request to change the spec).

---

## ▸ Who this is for

To x402 builders, MCP developers, AI-agent operators, data producers publishing OKF bundles, and enterprise teams handling workflows that carry trust across systems — we'd love to hear from anyone who wants to shape the verifiability layer with us. Whether you want to try signing and verification on your own bundles, or design condition proofs for your own use case, both are welcome. The working code is yours to touch today.

**Built for decisions that matter.**

---

## ▸ Resources

- Verifiable OKF repository: https://github.com/lemmaoracle/verifiable-okf
- Extension spec, SPEC v0.3: https://github.com/lemmaoracle/verifiable-okf/blob/main/SPEC.md
- worked example (sign → verify → report on a GA4 bundle): https://github.com/lemmaoracle/verifiable-okf/tree/main/examples/okf-bundle

— The Lemma team
