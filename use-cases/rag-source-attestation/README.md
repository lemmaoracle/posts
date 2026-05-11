---
title: "RAG Source Attestation"
abstract: "Bind each AI citation to a ZK proof of the exact docHash it claims to reference. Citation integrity holds across index rebuilds. Book a 30-minute discovery call to see how it fits your workflow."
thesis: "Cited ≠ verified"
pillar: verifiable-ai
targetVerticals:
  - Legal tech
  - Enterprise knowledge management
  - Financial compliance
tags:
  - rag
  - citation-verification
  - source-attestation
relatedUseCases:
  - ai-audit-log-proof
  - rag-content-provenance
---

# Use Case: RAG Source Attestation

## Problem

Enterprise RAG systems provide answers with citations, but there's no cryptographic guarantee that the cited source matches the unaltered original. Documents evolve, embeddings drift, and indexes are rebuilt without preserving citation integrity.

**Cited ≠ verified.** That is the structural gap Lemma closes.

## Contents

- **[01-problem.md](./01-problem.md)** — Detailed problem analysis: why citations lack verifiable binding to sources
- **[02-scenario.md](./02-scenario.md)** — Legal compliance scenario: outdated policy citation timeline
- **[03-architecture.md](./03-architecture.md)** — docHash + ZK proof binding architecture
- **[04-proof.md](./04-proof.md)** — Proven facts: document hash, version, access timestamp, citation-to-source binding
- **[05-related.md](./05-related.md)** — Links to related use cases with pillar attribution

## Target Verticals

- Legal tech (case law research, contract analysis)
- Enterprise knowledge management (policy compliance, internal documentation)
- Financial compliance (regulatory reporting, audit trails)

## Implementation Reference

- [example-rag-attestation](https://github.com/lemmaoracle/example-rag-attestation) — End-to-end scenario: citation proof generation and verification
- [example-x402](https://github.com/lemmaoracle/example-x402) — Reference implementation of the proof-bundle primitives (commitment, selective disclosure, ZK proof)

## Related Use Cases

- [AI Audit Log Proof](../ai-audit-log-proof/) — Verifiable AI counterpart for decision rationale
- [RAG Content Provenance](../rag-content-provenance/) — Verifiable origin counterpart for ingested content