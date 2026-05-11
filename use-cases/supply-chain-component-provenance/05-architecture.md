---
title: "Architecture in concept"
---


Lemma does not replace your PLM, MES, ERP, or procurement system. We add one provenance-chain layer on the path where each tier issues component attributes and on the path where the assembler verifies them.

<!-- TODO: replace with a Mermaid or SVG diagram. Vertical chain layout: Tier-N → Tier-1 → assembler. -->
<!-- Temporary placeholder:

  [Tier-3: wafer / raw material]
       │ (issuer signature + upstream link)
       ▼
  [Tier-2: semiconductor manufacturer / intermediate processing]
       │ (issuer signature + upstream link)
       ▼
  [Tier-1: distributor / direct supplier]
       │ (issuer signature + upstream link)
       ▼
  [Assembler: receiving verification]
       │ (chain verified as a ZK proof → autonomous procurement agent / QA)
       ▼
  [On-chain anchored]
-->

Each attestation carries the issuer's signature and a cryptographic link to the upstream tier, forming a per-lot chain. The assembler's autonomous procurement agent verifies the chain as a ZK proof, rejecting out-of-scope lots and tampered attributes at the boundary. At recall time, the impact scope is identified by walking the provenance chain — nothing more.

Sector-specific component-class schema design, integration patterns with existing PLM (Siemens Teamcenter, PTC Windchill, etc.), MES (Rockwell, Honeywell, etc.), and ERP (SAP, Oracle, etc.) systems, and DPP required-attribute schema design are detailed in the whitepaper and the post-call technical kit.
