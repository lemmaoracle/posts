---
title: "A real-world example: counterfeit parts entering the assembly line"
---


An automaker sources ECU power semiconductors through a Tier-1 electronics distributor, who buys from a Tier-2 semiconductor manufacturer, who in turn buys from a Tier-3 wafer processor. Each tier attaches lot numbers and quality-test results as PDF/Excel on shipment.

One month, a spike in field failures reveals that some shipment lots contained counterfeits — pin-compatible by part number, but off-spec. The Tier-1 supplier-submitted paperwork looks perfectly consistent. Lot numbers check out. Tracing upstream to Tier-2 and Tier-3 means asking each tier to re-submit documents in their own formats and reconciling against internal ledgers — weeks of work. By then, tens of thousands of vehicles are already in the field.

With Lemma in place, every component lot carries an issuer-signed provenance attestation at shipment, with cryptographic links to upstream tiers. An autonomous procurement agent verifies the chain as a ZK proof before the lot enters the assembly line — lots outside the Tier-3 wafer processor's certified scope are structurally rejected. The judgment runs on cryptographic integrity, not paper consistency.

Sector-specific component-class schema design, integration patterns with existing PLM, MES, and ERP systems (SAP, Siemens Teamcenter, etc.), and recall-response evidence-trail design are shared in the sector-specific kit we send after the consultation call.
