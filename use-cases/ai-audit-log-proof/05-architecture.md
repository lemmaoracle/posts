---
title: "Architecture in concept"
---


Lemma does not replace your model or your MLOps stack. We add a single decision-time attestation step in the inference path.

<!-- TODO: replace with a Mermaid or SVG diagram. Horizontal layout, recommended size that fits an internal proposal deck. -->
<!-- Temporary placeholder:

  [Input] → [Inference model] → [Decision-time attestation gate] → [Final output]
                                              │
                                              ▼
                                     [ZK proof generated]
                                              │
                                              ▼
                                     [Model ID / docHash / guideline signature sealed]
                                              │
                                              ▼
                                     [On-chain anchored]
-->

Even after the model is updated, the hashes and guidelines of past decisions remain intact. Source data stays inside your perimeter. Only the cryptographic identity of the decision crosses to the verifier. Regulator, auditor, and appellant each verify independently.

Integration patterns with existing MLOps (MLflow, Weights & Biases, in-house inference platforms), alignment to EU AI Act Article 12 / ISO 42001 audit requirements, and decision-time overhead minimization are detailed in the whitepaper and the post-call technical kit.
