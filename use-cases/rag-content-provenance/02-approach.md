---
title: "How Lemma approaches it"
---


At the moment an internal document enters the RAG pipeline, Lemma encrypts the source with AES-GCM and writes the docHash, content identifier (CID), issuer signature, and active-version metadata into the index. What the retrieval layer matches on is not the raw source but a fact carrying provenance.

A sentence quoted in an answer is cryptographically traceable, via the indexed docHash, to a specific version of a specific source. When the document is revised, a new docHash and issuer signature are attached; references to the prior version are structurally detectable. The AI only touches facts whose provenance can be verified.

Where the provenance layer fits into your existing RAG ingest pipeline is what we map out in a first conversation.
