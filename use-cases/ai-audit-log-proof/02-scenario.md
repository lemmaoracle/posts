---
title: "Scenario"
---

# Scenario

September 2026. A regional bank's corporate lending AI denies a small business loan application.

The following year, the business files an appeal: "Wasn't the decision unfair by current standards?"

The problem: the model was updated in December 2026. Which features received which weights, which internal guideline drove the denial — accurately reproducing this is difficult. What remains is a simple log entry: "Model v2.3 / Denial score 0.71 / Reason code C-04." Inside the bank, a quiet consensus forms: "We have logs, but we cannot explain."

With Lemma, the following proofs would have been sealed at the moment of decision:

- Model identifier and hash
- Input attribute docHash and CID (identity with originals)
- Cryptographic signature of the applied internal guideline
- Attribution of the final decision

Against the appeal, the bank could prove: "On September 18, 2026, model A-v2.3, following guideline G-2026-04, based on input attribute category X, produced this decision" — without disclosing the underlying data. The regulator, audit firm, and appellant can all independently verify the same proof.

No matter how many times the model is updated, the structure of past decisions remains immutable.
