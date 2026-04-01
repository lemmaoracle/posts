---
slug: "privacy-preserving-attribute-marketing-lemma-verifiable-ai"
date: "2026.04.01"
category: "Business Strategy"
section: "Essays"
title: "Privacy-Preserving Attribute Marketing: Lemma Verifiable AI's Practical Approach"
abstract: "As data sharing within corporate groups becomes restricted by regulations, Lemma Verifiable AI uses Zero-Knowledge Proof technology to verify attributes without disclosing data, enabling secure marketing collaboration. This article explains the technical approach to attribute marketing based on ZK proofs, implementation details, and expected KPI improvements."
cover: "assets/rdFbtbFObPk.jpg"
---

## Introduction

In the marketing domain, there is growing demand to more effectively utilize customer data and advance segmentation analysis and personalization. Large-scale dataset integration is key, but regulations such as privacy laws and GDPR make sharing within corporate groups challenging. Additionally, the black-box nature of AI models reduces the reliability of analysis results and hinders consensus-building among stakeholders.

In response to these challenges, Lemma's Verifiable AI, based on Zero-Knowledge Proof (ZKP) technology, proposes a new data utilization paradigm. By mathematically proving attributes without outputting the data itself, it enables secure marketing collaboration. This article provides a detailed explanation of the technical background, implementation details, and expected KPI impact.

## Evolution and Background of Marketing Collaboration

In today's increasingly API-driven ecosystem, Application-to-Application (A2A) collaboration among corporate groups is gaining importance. Marketing professionals want to dynamically utilize purchase history and behavioral data to launch real-time campaigns. However, siloed attribute data creates bottlenecks, particularly with delayed verification of "high-engagement attributes" for loyal customers.

This transformation aligns with DX trends, and the evolution of W3C Verifiable Credentials (VC) standards is driving the fusion of ZK and ML technologies.

| Item                | Traditional Approach                 | Lemma Verifiable AI                                                                   |
| ------------------- | ------------------------------------ | ------------------------------------------------------------------------------------- |
| Data Sharing        | Sharing the entire dataset is required (concerns about leakage risk)  | Attribute proof only (ZK mathematical non-disclosure)                                 |
| Analysis Flow       | Manual aggregation/static dashboards | AI dynamic + ZK proof chain                                                           |
| Collaboration Speed | Several days (manual work dependent) | Real-time (within hours)                                                              |
| Protection Level    | Basic encryption (AES only)          | ZK proof + BBS+ selective disclosure + DID identity (post-quantum migration designed) |
| Auditability        | Log-dependent                        | Immutable proof chain                                                                 |

## Detailed Structure of Trust Issues

Breaking down the barriers to marketing data utilization reveals five key challenges:

- Attribute leakage concerns: Exposure of PII (Personally Identifiable Information) during sharing.
- AI decision opacity: Bias suspicion due to black-box nature.
- Insufficient audit trails: Difficulty in compliance verification.
- Group accountability sharing: Ambiguity in accountability among multiple companies.
- Scalability limits: ZK computational overhead with large-scale data.

Lemma addresses these with a 3-layer architecture: Layer 1 issuer DID proof, Layer 2 ZK attribute extraction (Groth16 protocol), Layer 3 scope-based control. The result is a transparent infrastructure compliant with ESG standards.

## Technical Implementation Details of Lemma

Here is a code example of attribute marketing automation using Lemma SDK (TypeScript/Node.js compatible):

```typescript
import { create, attributes } from "@lemmaoracle/sdk";

const client = create({
  apiBase: "https://api.lemmaoracle.example.com",
  apiKey: process.env.LEMMA_KEY,
});

const result = await attributes.query(client, {
  attributes: [
    { name: "loyalty_score", operator: "gt", value: 0.8 },
    { name: "engagement_rate", operator: "gt", value: 0.5 },
  ],
  proof: {
    required: true,
    type: "zk-snark",
  },
  targets: {
    schemas: ["customer-profile-v1", "behavior-v2"],
  },
});

if (result.results.length > 0) {
  // Secure sharing and optimization within the group
  await campaign.optimize({
    issuer: "groupA-crm",
    attributes: result.results.map((r) => r.attributes), // No PII disclosure
    verified: true,
  });
} else {
  throw new Error("No verified attributes found");
}
```

Key technologies:

- **Input protection**: ECDH key exchange + HKDF key derivation + AES-256-GCM encryption. Both Lemma and the query side handle only ciphertext, with no access to plaintext.
- **Knowledge integration**: RAG (Retrieval-Augmented Generation) schema-based attribute CID matching. Flexible custom schemas can be registered, enabling verification of any attribute set.
- **Proof logging**: BBS+ (IETF draft-irtf-cfrg-bbs-signatures) selective disclosure signatures. Forms W3C VC/DID-compliant proof chains with designed post-quantum migration paths.
- **Scale**: Parallel ZK verification enables low-latency, horizontally scalable query processing.

## Expected Impact Metrics

Quantitative expectations from Lemma implementation:

- Customer retention rate: A 15-20% improvement is anticipated as attribute accuracy increases.
- Campaign ROI: A 2-3x improvement is expected through real-time optimization.
- Analysis misjudgment rate: Reduction to below 1% is theoretically achievable through ZK proof verification.
- Attribute utilization rate: Full attribute utilization becomes possible even in a non-disclosure environment.

If these effects are realized, marketing in FinTech, Retail, and E-commerce could see further progress in ESG compliance. Additionally, reducing analysis errors may contribute to improved conversion rates.

## KPI Design and PoC Validation Flow

**Primary KPIs**:

- Attribute utilization rate: 100%.
- Automation rate: 80% or higher.
- Misjudgment rate: <1%.
- Collaboration latency: <1 hour.

**PoC Flow (1-2 weeks)**:

1. **AI entry verification**: Data normalization + initial ZK generation.
2. **Policy testing**: Custom query/scope definition.
3. **Group A2A deployment**: CRM/E-commerce API integration, KPI dashboard construction.

**Before/After Comparison**:

| Metric            | Before (Traditional)  | After (Lemma)             |
| ----------------- | --------------------- | ------------------------- |
| Analysis time     | 3 days                | 1 hour                    |
| Trust score       | Subjective evaluation | ZK proof-based verifiable evaluation |
| Sharing scope     | Internal only         | Secure groups             |
| Compliance burden | High (manual audit)   | Low (automatic proof)     |
| ROI impact        | Baseline              | +200% expected            |

## Conclusion

Lemma Verifiable AI presents a new approach to attribute marketing in data non-disclosure environments. As part of the Trusted Web infrastructure envisioned for Society 5.0, it offers one potential path forward. For more details, please refer to our whitepaper.

Partner registration (1 minute required) is currently open for PoC demos and priority support. Please apply below.

[Register as a Partner Candidate (1 minute)](https://429bpd.share-na2.hsforms.com/2E6_TsCd2RUSdP4fKsuhxzw)
