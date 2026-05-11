---
title: "Scenario"
---


Automotive parts manufacturer Company S exports steel body panels to Europe. With CBAM's full application starting in 2026, payment of carbon prices corresponding to embedded emissions and submission of calculation grounds are now mandatory.

Company S's procurement spans 5 tiers:

- Tier-1: Steel that S directly purchases
- Tier-2: Steel mills (electric arc / blast furnace)
- Tier-3: Iron ore and coking coal import trading companies
- Tier-4: Mine operators
- Tier-5: Power source composition at extraction sites (fossil fuels / renewables)

Each tier sends emissions data upstream in Excel and PDF. S aggregates these and incorporates them into documents provided to European importers. Internally, four ESG data specialists at S are consumed by normalizing different supplier formats.

At audit, S presents "supplier-submitted data used in aggregation" to the authorities. But the authorities ask whether that **data arrived from the supplier at the time without tampering.** There is no way to prove a spreadsheet's provenance in Excel.

Additionally, S's newly introduced autonomous procurement agent requires pre-order verification for CBAM compliance, but current electronic data lacks verifiability — the agent ultimately waits for manual final confirmation.

With Lemma, each tier encrypts emissions attributes with issuer signatures before passing them upstream. Each attribute contains:

- Issuer identity (mining operator, steel mill, trading company)
- Measured values and methodology (GHG Protocol Scope 1-3, EUDR compliance criteria)
- Measurement timestamp and scope (batch ID, raw material lot)
- Cryptographic binding to upstream tier (raw-material-unit chain)

S's autonomous procurement agent verifies component-level CBAM compliance as a ZK proof before confirming the order. The EU importer independently confirms that embedded emissions are below the CBAM threshold — without being disclosed raw material details or supplier contracts. When the same raw material is allocated to other products, the cryptographic binding structurally detects double-counting.

At audit, authorities verify a cryptographic provenance chain rather than a bundle of spreadsheets. Supplier trade secrets remain protected while regulatory compliance is established.
