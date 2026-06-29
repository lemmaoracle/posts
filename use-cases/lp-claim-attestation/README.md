---
title: "LP Claim Attestation"
abstract: "Issue DeFi liquidity-provider attributes — KYC outcome, region, risk tolerance — as ZK attestations without exposing the raw data, so the protocol verifies only the attribute predicates it needs and meets regulatory requirements without ever holding the source identity record."
thesis: "Pooled ≠ verified"
pillar: regulatory-attribute-proof
industries:
  - fin
cardSummary: "Verify LP eligibility attributes per regulatory regime without exposing individual KYC or balance data."
targetVerticals:
  - DeFi protocol teams
  - Regulated digital-asset platforms
  - Cross-border DeFi exposure desks
  - Compliance and risk leads in crypto-asset firms
tags:
  - defi
  - kyc
  - selective-disclosure
  - liquidity-provider
  - regulatory-attribute
relatedUseCases:
  - kyc-aml-selective-disclosure
  - defi-bridge-verification
  - financial-data-exfiltration
---

# Use Case: LP Claim Attestation

## Thesis

**Pooled ≠ verified**

A DeFi protocol can accept liquidity from any wallet; that does not mean it has verified the contributor's regulatory eligibility, jurisdiction, or risk tolerance. Today the workaround is either no verification (regulatory risk) or full off-chain KYC handoff (privacy loss, custodian risk). Lemma issues a ZK attestation per LP so the protocol verifies only the per-attribute predicate it needs — eligibility, region, risk tier — and never holds the source identity record.
