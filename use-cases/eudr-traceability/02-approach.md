---
title: "How Lemma approaches it"
---


Each tier of the chain — the producer (or the cooperative aggregating producers), the trader, the importer — issues an attestation against the EUDR-specific attributes: country of origin, geolocation polygon, harvest date, permit status, deforestation-free indicator measured against the regulation's reference date.

The attributes are signed against each holder's source data — the producer's plot data, the cooperative's aggregated records, the trader's chain-of-custody — and the proof reveals only the attribute, never the source. The importer's final due-diligence statement is a single composed ZK proof, not a folder of supplier-side spreadsheets.

For multi-smallholder chains, the cooperative pattern handles aggregation without forcing each producer to expose their plot data downstream. A producer can withdraw or update an attestation without rebuilding the chain.
