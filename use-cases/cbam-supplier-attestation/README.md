---
title: "CBAM Supplier Attestation"
abstract: "Chain country, origin, and carbon-intensity attributes required by EU CBAM as cryptographic attestations — without exposing supplier raw data or trade secrets."
thesis: "Reported ≠ provable"
pillar: regulatory-attribute-proof
industries:
  - mfg
  - sc
cardSummary: "Prove CBAM-required supplier attributes by cryptographic attestation, keeping the underlying production data inside each supplier's perimeter."
targetVerticals:
  - Manufacturing exporting to the EU
  - Steel, aluminum, cement, fertilizer
  - Multi-tier supply chain
  - Trading houses and brokers
tags:
  - cbam
  - eu-regulation
  - carbon-intensity
  - selective-disclosure
  - supply-chain
relatedUseCases:
  - eudr-traceability
  - supply-chain-esg
  - supply-chain-component-provenance
---

# Use Case: CBAM Supplier Attestation

## Thesis

**Reported ≠ provable**

The EU Carbon Border Adjustment Mechanism asks importers to prove the embedded carbon of each shipment. Today, that proof is a chain of PDFs, declarations, and emails — auditable in name only, and a structural threat to supplier trade secrets. Lemma issues per-attribute ZK attestations along the supply chain, so the importer can verify CBAM compliance directly against cryptographic facts while the supplier's production data never leaves their perimeter.
