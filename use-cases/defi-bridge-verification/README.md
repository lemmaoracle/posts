---
title: "DeFi Bridge Verification"
abstract: "Cross-chain bridge messages are cryptographically valid yet not necessarily semantically correct. Lemma adds a cryptographic layer that enables the receiving system to independently verify message origin before committing state."
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
