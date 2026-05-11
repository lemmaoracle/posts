---
title: "DeFi Bridge Verification"
abstract: "Verify cross-chain message origin before the receiving side commits state — a second cryptographic layer that runs alongside the DVN. Start with a 30-minute conversation."
thesis: "Cryptographically valid ≠ semantically right"
pillar: verifiable-origin
targetVerticals:
  - Liquid staking / restaking protocols
  - Cross-chain bridges
  - Lending protocols
  - DEXs
tags:
  - bridge-exploit
  - pre-execution-attestation
  - LayerZero
  - DeFi
relatedUseCases:
  - supply-chain-component-provenance
  - rag-content-provenance
  - financial-data-exfiltration
---

# Use Case: DeFi Bridge Verification

## Thesis

**Cryptographically valid ≠ semantically right**

Cross-chain bridge messages are cryptographically valid yet not necessarily semantically correct. Lemma adds a cryptographic layer that enables the receiving system to independently verify message origin before committing state.
