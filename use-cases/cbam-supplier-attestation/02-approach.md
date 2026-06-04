---
title: "How Lemma approaches it"
---


Each tier of the chain — the smelter, the rolling mill, the trader, the importer — issues attestations for the attributes CBAM actually checks: country of production, carbon intensity, the regulatory regime the attribute was measured against. The attributes are signed against each supplier's reference data (production logs, energy mix, monitoring system output), but the proof reveals only the attribute, never the underlying source.

Downstream parties verify the attestation directly. A trader chaining steel from a smelter to an EU importer doesn't need the smelter's production data — it needs proof that the smelter's reported carbon intensity holds under CBAM's measurement scheme. When that proof is a ZK attestation rather than a PDF, the trader can compose it with their own attestation and pass a single chained proof to the importer.

The result is that CBAM evidence becomes a cryptographic chain instead of a document-discovery exercise — and each link controls exactly what flows downstream.
