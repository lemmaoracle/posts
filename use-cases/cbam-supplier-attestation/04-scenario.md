---
title: "Scenario: a steel shipment under CBAM"
---


A Japanese steel mill ships rolled coil to a German automotive supplier. Under CBAM, the supplier in Germany is the declarant and needs to attest the embedded emissions per ton. The mill has the source data — energy mix, production logs, monitoring system output — but is unwilling to expose any of it because the same fields would reveal its proprietary process efficiency.

In the document-based workflow, the mill produces a third-party-verified PDF report. The trader between them re-issues the report under its own letterhead. The importer combines this with its own paperwork and files. Every step accumulates trust on a paper trail no one can independently verify, and a single supplier disclosure leak undermines the chain.

With per-attribute ZK attestation, the mill issues a proof: "tons-CO₂-per-ton-steel = N, measured against CBAM Annex IV." The trader chains its own attestation for the routing and handling. The German importer composes both proofs and submits a single chained attestation as the CBAM filing — verifiable by the authority, by the auditor, and by the supply chain itself, without anyone ever seeing the mill's monitoring data.

Category-specific attribute schemas (steel / aluminum / cement / fertilizer), multi-tier composition patterns, and Lemma's reference attestation flow for CBAM live in the post-Discovery materials.
