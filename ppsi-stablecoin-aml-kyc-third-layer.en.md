---
slug: "ppsi-stablecoin-aml-kyc-third-layer"
date: "2026.05.15"
category: "Industry"
section: "Essays"
title: "Adding Layer 3 to x402 — Choices for Stablecoin Issuers and Adopters"
cover: "assets/cover-industry.png"
abstract: "Layer 3 for businesses handling stablecoins — a design that proves customer attributes across agent payments without sharing the underlying data. Lemma's ZK attribute proof layer unifies multiple use cases, including U.S. PPSI and EU MiCA compliance, on a single foundation."
tags:
  - ppsi
  - genius-act
  - stablecoin
  - kyc-aml
  - x402
  - payments
  - zk-proof
  - compliance
---

**TL;DR**

The agent-payment stack on x402 now has signature verification and spending governance in place. What remains is the customer-understanding layer — proving who is paying whom, without sending the underlying data. The **PPSI NPRM** (Notice of Proposed Rulemaking for Permitted Payment Stablecoin Issuers) that FinCEN and OFAC jointly published on 2026-04-08 brings this layer directly into scope for stablecoin issuers. Lemma's ZK attribute proof layer sits complementarily on top of x402 + AWS Bedrock AgentCore Payments, filling Layer 3 and making PPSI compliance, A2A agent payments, and regulatory audit trails implementable on a single foundation.

The rule is written for the issuer. The implementation lives with the delegation targets — the parties downstream who actually handle the attributes.

*Facts are accurate as of 2026-05-12. Preview scope and legal interpretations are subject to change.*

---

## 1. The PPSI NPRM and the current state of the x402 stack

A snapshot of both the regulatory and the technical sides, from the perspective of a business handling stablecoins.

### Regulatory side — the PPSI NPRM

FinCEN (the U.S. Treasury's Financial Crimes Enforcement Network) and OFAC (Office of Foreign Assets Control) jointly published an NPRM on 2026-04-08 to implement the AML/CFT and sanctions-program requirements that the GENIUS Act places on permitted payment stablecoin issuers. Two dates matter for readers: **the comment period closes 2026-06-09**, and **the final rule is proposed to take effect twelve months after publication**.

At the core of the NPRM, PPSI issuers are treated as financial institutions under the Bank Secrecy Act. The principal requirements placed on the issuer are:

- **Customer identification and customer due diligence (CIP / CDD)**: verify identity, perform risk assessment, identify beneficial owners.
- **Suspicious activity reports (SARs)**: ongoing transaction monitoring and reporting against defined thresholds and typologies.
- **Recordkeeping**: retention of transaction records and customer documents.
- **OFAC sanctions compliance program**: senior-management commitment, risk assessment, internal controls, testing and auditing, training — the five elements.

A defining feature of the proposal is that these obligations land squarely on the stablecoin-issuing entity itself.

### Technical side — the x402 payment stack

The agent-driven payment stack has accelerated through several layers in the past six months. A three-layer view:

| Layer | Role | Status (as of 2026-05) | Provider |
|---|---|---|---|
| Layer 1 | HTTP-layer payment protocol, signature verification | Specification established, SDK vulnerability fixed | x402 Foundation / Coinbase / Cloudflare |
| Layer 2 | Spending limits, audit trail, wallet integration | Running as preview | AWS Bedrock AgentCore Payments (Coinbase CDP / Stripe Privy) |
| Layer 3 | AML/KYC attributes, CDD, beneficial ownership, OFAC screening | Outside preview scope, delegated upward | Issuers / MSBs / adopters (under PPSI, the parties expected to implement) |

The roles of each player in this stack:

- **x402 Foundation** — Protocol specification (a payment protocol built around HTTP 402 Payment Required).
- **Coinbase** — CDP wallet, SDK, and x402 co-promotion.
- **Stripe** — Privy wallet and AgentCore Payments co-development.
- **AWS** — Operational layer via Bedrock AgentCore Payments (spending limits, audit trail, wallet integration).
- **Lemma** — Attribute proof layer, running as an x402 Header extension.

With these five parties combined, a coherent stack emerges from payment protocol → spending governance → customer understanding. The fact that payment platforms cover Layer 1 and Layer 2 while Layer 3 is delegated upward reflects a sensible separation of concerns — payment automation and customer understanding are different problems, and folding KYC into a payment platform would create vendor lock-in and cross-jurisdiction friction.

But once the PPSI NPRM takes effect, the parties that Layer 3 has been delegated to — stablecoin issuers, MSB-licensed adopters, and middleware providers — are the parties that must apply CDD and SAR obligations to agent-routed transactions as part of their own AML/CFT program.

---

## 2. The delegation targets' responsibility — U.S. law-firm analysis

For how the PPSI NPRM shapes the delegation model, we recommend referring to public analysis from major U.S. law firms rather than to any Lemma-specific interpretation.

- **Mayer Brown**: Even when an issuer does not directly interact with customers (typically when agents or middleware sit in between), the issuer is expected to implement transaction monitoring and CDD informed by its own risk assessment.
- **Holland & Knight**: As agent-driven transactions grow, issuers are expected to set explicit risk assessment and controls for indirect customer access paths.
- **Fried Frank / PwC**: From a different angle, both firms confirm the structure under which issuers bear responsibility for indirect transactions in the delegation model.

Several independent legal readings converge on the same direction: Layer 3 (the customer-understanding attribute layer) lands on one of the delegation targets — the issuer, an MSB, or the adopting business. Exactly what to implement, and how far, is a judgment each business has to make against its own legal and compliance posture.

This post takes that judgment as given and, in the next section, walks through one technical architecture that sits among the available options.

---

## 3. Filling Layer 3 with Lemma

What Layer 3 needs to satisfy, and how the Lemma attribute proof layer answers each requirement.

### What Layer 3 has to satisfy

- A proof that the agent — and the principal behind it — has cleared KYC.
- Where required, beneficial ownership, residency, and sanctions-list non-listing as additional attributes.
- Sending these attributes in full per transaction is itself a privacy cost (transmitting PII per request is unlawful and operationally impractical).
- The right primitive is therefore an **attribute proof** — a mechanism that proves "this principal has cleared KYC" or "is not on the sanctions list" without sending the underlying data.

### Lemma's ZK attribute proof layer

Lemma's contribution is precisely this Layer 3 attribute proof. BBS+ signatures combined with zkSNARK selective disclosure carry only the attributes the issuer asks for, on the HTTP layer. The properties of the design:

- **Wallet-agnostic**: Operates as an x402 Header extension on either Coinbase CDP wallet or Stripe Privy wallet.
- **HTTP-layer complete**: Does not require additional on-chain verification. The issuer or verifier extracts the attribute proof from the HTTP Header and verifies it directly.
- **Selective disclosure**: You can disclose only "KYC verified", or a richer set such as "KYC verified + country of residence + not on sanctions list".
- **PPSI alignment**: CDD, beneficial ownership, and OFAC screening can each be expressed as attributes.

The integration carries an `extensions.lemma` Header alongside the x402 response. The x402 SDK import is one line; calling `@lemmaoracle/x402` makes attribute proof generation and verification work inside AgentCore Gateway or any agent runtime.

```ts
// Carry a Lemma attribute proof alongside the x402 response
import { x402 } from "@x402/sdk";
import { attachAttributeProof } from "@lemmaoracle/x402";

const response = await x402.respond({
  amount: "1.00",
  asset: "USDC",
  recipient: "0x...",
  extensions: {
    lemma: await attachAttributeProof({
      attributes: ["kyc_verified", "ofac_clear"],
      issuer: "did:lemma:issuer/...",
    }),
  },
});
```

### Use cases that open up with x402 + AgentCore Payments + Lemma

Combining the x402 protocol, AgentCore Payments' spending governance, and Lemma's attribute proof lets businesses handling stablecoins support multiple workflows on a single foundation.

- **Regulatory compliance (PPSI / MiCA)**: Carry the attribute proof Header on the x402 payment flow to prove KYC and OFAC screening results to counterparties without sharing the underlying data.
- **A2A agent payments**: On top of AgentCore Payments' spending governance, also verify the counterparty agent's authority and attributes at the HTTP layer.
- **Onboarding**: Each organization retains attributes about vendors, partners, and customers locally and shares only the attributes that are needed.
- **Regulatory reporting and audit response**: Combine attribute proofs with the x402 + AgentCore Payments transaction trail to retain a tamper-evident long-term record that holds up to regulators and third-party auditors.

PPSI is the near-term forcing function, and the x402 + AgentCore Payments + Lemma combination is a configuration you can roll out incrementally across these use cases.

---

## 4. Looking across the Atlantic — alignment with EU MiCA

In parallel with the U.S. PPSI NPRM, the EU MiCA grandfathering period ends 2026-07-01. After that, non-licensed CASPs cannot legally provide services in the EU. MiCA likewise requires stablecoin issuers to maintain a 1:1 reserve, AML/KYC, periodic audits, and transaction reporting. The need for issuers to understand their customers cuts across both jurisdictions.

Lemma's ZK attribute proof layer is designed to cover the customer-understanding requirements of U.S. PPSI and EU MiCA with a single attribute proof layer. For businesses operating across both the U.S. and European markets, that compresses the cost of regulatory compliance.

---

## 5. Summary

- The April 2026 PPSI NPRM places Bank Secrecy Act AML/CFT obligations on stablecoin issuers. Alongside the AWS Bedrock AgentCore Payments preview of the same month, the x402 payment stack has Layer 1 and Layer 2 in place — while Layer 3 (KYC attribute proof) is delegated upward, outside preview scope.
- The delegation targets — stablecoin issuers, MSBs, and adopting businesses — face a choice: implement Layer 3 in-house, or integrate a third-party attribute provider. For the specifics of obligation scope, refer to the law-firm analyses cited in §2.
- Lemma's ZK attribute proof layer fills Layer 3 with a wallet-agnostic, HTTP-layer-complete, selective-disclosure design. It supports PPSI compliance, A2A agent payments, onboarding, and regulatory audit trails on a single foundation.
- The U.S. PPSI and EU MiCA timelines align on a single attribute proof layer, by design.

---

PPSI taking effect is a forcing function for businesses handling stablecoins to redesign their customer-understanding mechanism. Adopting Lemma's ZK attribute proof layer lets you build, on one foundation:

- **The CDD and OFAC screening** that PPSI requires, proven without sharing customer data.
- **Counterparty authority and attribute verification** in A2A agent payments, at the HTTP layer.
- **The regulatory audit trail**, retained in a tamper-evident, long-term form.

**Target readers**:

- **Stablecoin issuers** — JPYC, Progmat, USDC, USDT, and equivalents.
- **Fintech and AI-agent operators** — organisations handling payments via x402 or AgentCore.
- **Compliance teams at adopting businesses** — MSB-licensed operators.
- **Regulatory affairs and risk-management leads** — those evaluating PPSI / MiCA implementation.

We welcome inquiries from stablecoin issuers and adopters considering Lemma — both for PPSI response and beyond. We typically reply within one business day.

[**Talk to us →**](https://tally.so/r/Pd2Rl5)

*Built for decisions that matter.*
