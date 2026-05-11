---
title: "A real-world example: CBAM audit and multi-tier spreadsheets"
---


An automotive parts manufacturer exports steel body panels to the EU. Once CBAM is fully in force, importers must pay a carbon price on embedded emissions and submit the calculation grounds. Procurement runs five tiers deep — Tier-1: steel / Tier-2: mill / Tier-3: import trader / Tier-4: mining operator / Tier-5: power mix — and each tier passes emissions data upstream via Excel and PDF.

At audit, what the regulator asks is not "the aggregated data" itself but "the grounds that this data arrived from the supplier of record without tampering." There is no way to prove the provenance of a spreadsheet on Excel. Worse, an autonomous procurement agent introduced to verify CBAM compliance before placing orders finds no verifiable structure in the electronic data — and ends up waiting for a human final check.

With Lemma, each tier issues issuer-signed emissions attestations passed upstream, with per-material cryptographic links that structurally rule out double counting. The EU importer can independently verify, via ZK proof, that the embedded emissions sit below the CBAM threshold — without seeing supplier names or contract terms. At audit, the regulator verifies a cryptographic provenance chain instead of a stack of Excel files.

Sector-specific regulatory mapping (CBAM formula, EUDR DDS, DPP required attributes) and integration patterns with existing supplier-management systems (SAP Ariba, Coupa, etc.) are shared in the sector-specific kit we send after the consultation call.
