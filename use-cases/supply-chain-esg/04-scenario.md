---
title: "A real-world example: CBAM audit and multi-tier spreadsheets"
---


An automotive parts manufacturer exports steel body panels to the EU. Once CBAM is fully in force, importers must pay a carbon price on embedded emissions and submit the calculation basis. Procurement runs five tiers deep — Tier-1: steel / Tier-2: mill / Tier-3: import trader / Tier-4: mining operator / Tier-5: power mix — and each tier reports emissions data up the chain via Excel and PDF.

At audit, the regulator is not asking for the aggregated data itself — they're asking for verifiable grounds that the data arrived from the supplier of record, untampered. There is no way to prove the provenance of a spreadsheet inside Excel. Worse, an autonomous procurement agent introduced to verify CBAM compliance pre-order finds no verifiable structure in the electronic data — and ends up waiting on a human final check.

With Lemma, each tier issues issuer-signed emissions attestations that pass up the chain, with per-material cryptographic links that structurally rule out double counting. The EU importer can verify via ZK proof that the embedded emissions sit below the CBAM threshold — without seeing supplier names or contract terms. At audit, the regulator verifies a cryptographic provenance chain instead of a stack of Excel files.

Sector-specific regulatory mapping (CBAM formula, EUDR DDS, DPP required attributes) and integration patterns with existing supplier-management systems (SAP Ariba, Coupa, etc.) are shared in the sector-specific kit we send after the consultation call.
