---
title: "A real-world example: an appeal arrives a year later"
---


In September 2026, a regional bank's corporate lending AI declined a small-business loan application. A year later, an appeal arrives: "Under current standards, was the original decision actually justified?"

The problem: the model was updated in December 2026. Reconstructing exactly which features carried which weights, and which internal guideline produced the decline, is nearly impossible. What remains is a thin log: "Model version v2.3 / decline score 0.71 / decline reason code C-04." A quiet, organization-wide state of "logs exist but cannot explain."

With Lemma in place, at the moment the decision was made the model hash, the docHash of the input attributes, the signature of the applied guideline, and the attribution of the final output are all sealed into a single ZK proof. The bank can prove that "on 2026-09-18, model A-v2.3 — under internal guideline G-2026-04 and based on input attribute category X — produced this decision," without exposing the underlying data. The regulator, the auditor, and the appellant each verify the same proof, independently.

Sector-specific regulatory mapping (EU AI Act Article 12, ISO 42001 audit requirements), MLOps integration patterns, and regulatory response time estimates are shared in the sector-specific kit we send after the consultation call.
