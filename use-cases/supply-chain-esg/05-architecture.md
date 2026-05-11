---
title: "Architecture in concept"
---


Lemma does not replace your procurement system, ERP, or PLM. We add one layer in which each tier's supplier issues attestations carrying both a signature and a cryptographic link to the upstream tier, and the top-tier exporter presents the chain as a ZK proof to the regulator.

<!-- TODO: replace with a Mermaid or SVG diagram. Vertical chain layout: Tier-5 → Tier-1 → importer / regulator. -->
<!-- Temporary placeholder:

  [Tier-5: mining + power mix] ─┐
                                 │ (issuer signature + upstream link)
  [Tier-4: mining operator]   ───┤
                                 ▼
  [Tier-3: trader] ─→ [attestation chain] ─→ [Tier-2: mill] ─→ [Tier-1: steel] ─→ [Exporter]
                                                                                       │
                                                                                       ▼
                                                                  [ZK proof (CBAM/EUDR/DPP compliant)]
                                                                                       │
                                                                                       ▼
                                                                  [EU importer / regulator: independent verification]
-->

Each attestation carries the issuer's signature and a cryptographic link to the upstream tier, forming a per-material chain. When the same raw-material lot is allocated across multiple final products, double counting is structurally detected by those cryptographic links. Supplier trade secrets (cost, contract terms) stay protected, and the regulatory compliance lives in the ZK proof.

CBAM formula embedding, EUDR DDS integration, DPP required-attribute schema, and patterns for plugging into SAP Ariba, Coupa, and similar are detailed in the whitepaper and the post-call technical kit.
