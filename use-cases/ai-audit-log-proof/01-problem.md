---
title: "Problem"
---


AI is embedded in consequential decision-making: corporate credit approvals, insurance premium calculations, clinical triage, public benefit eligibility — all domains where retrospective accountability is legally required.

Current audit practices rely on plaintext decision logs and model version numbers. Three structural defects:

- **Plaintext logs are mutable.** There is no cryptographic mechanism to prove whether logs have been altered.
- **Model updates make past decisions irreproducible.** Complete weight state preservation and exact re-execution against historical inputs is operationally infeasible.
- **No third-party verification path exists.** The ability to verify "this model decided this, on these grounds" without disclosing data is absent from standard MLOps.

The EU AI Act (explainability requirements for high-risk AI, enforcement 2026), ISO 42001 (AI management system certification), and FSA AI governance guidelines — all quietly shift the requirement from "logs exist" to "tamper-proof grounds are provable."
