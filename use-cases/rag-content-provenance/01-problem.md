---
title: "Problem"
---

# Problem

Enterprise RAG systems index internal policies, contracts, clinical trial protocols, SOPs, and regulatory documents daily. These are often the **ultimate grounds** that AI agents cite in business decisions and customer responses.

At the moment of indexing, documents undergo a quiet transformation:

- **Identity with originals is implicit.** There is no verification path to confirm that a chunk in the index matches the "authoritative copy" held by the issuer.
- **Issuer signatures are stripped.** Metadata indicating who issued the document, when, and under what authority is largely lost during the indexing process.
- **Version history collapses.** When the RAG index is rebuilt, reproducing which version was "authoritative" at a given historical point becomes practically impossible.
- **Post-ingestion tampering goes undetected.** The index itself is defenseless against storage-level writes or intentional document swaps by insiders.

The EU AI Act, ISO 42001, pharmaceutical GxP regulations, and FSA AI governance guidelines — all are moving toward demanding **authenticity of grounds** for AI decisions and presented information. Without fixing provenance at the point of indexing, no amount of after-the-fact audit logging can stabilize the grounds of grounds.
