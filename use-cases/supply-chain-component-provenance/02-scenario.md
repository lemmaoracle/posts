---
title: "Scenario"
---

# Scenario

Major aerospace component manufacturer Company M supplies precision bearings and hydraulic components to Western airframers. In the aerospace industry, all safety-critical components require FAA Form 8130-3 or EASA Form 1 certification, with complete traceability from manufacture to disposal.

Company M's upstream spans 4 tiers:

- Tier-1: Company M (final machining, quality assurance)
- Tier-2: Steel and specialty alloy manufacturers
- Tier-3: Specialty alloy smelters
- Tier-4: Nickel and cobalt raw material trading companies

Each tier conveys component provenance via paper certificates and Excel traceability tables. Company M's quality assurance department has 10 engineers consumed by normalizing different supplier formats and visually inspecting certificate authenticity.

In August 2026, an airline reports abnormal bearing wear on the same aircraft type. Root cause analysis and component recall are urgent. Company M begins cross-referencing two years of shipping ledgers against upstream supplier certificates to identify potentially affected lots. Estimated completion: 3 weeks.

During those 3 weeks, bearings from the same series continue operating across multiple aircraft models.

With Lemma, each tier's component attributes would be encrypted with issuer signatures, forming a cryptographic chain traceable upstream. Each attribute contains:

- Manufacturing lot number and timestamp
- Cryptographic binding to upstream raw material and component lots
- Quality inspection results at each process step and inspection body signatures
- Process executor (manufacturer, inspection body) issuer signatures

Company M's autonomous procurement agent cryptographically verifies component authenticity and traceability before confirming orders. Upon receipt, incoming inspection confirms the cryptographic provenance chain rather than paper certificates.

At recall time, affected lots and their downstream products are **identified in seconds** from the cryptographic provenance chain. The airframer independently confirms that components originate from a legitimate multi-tier chain — without being disclosed Company M's supplier names or contract terms. FAA and EASA auditors perform cryptographic verification rather than reviewing bundles of paper documents.

Safety is assured while supplier trade secrets remain protected.
