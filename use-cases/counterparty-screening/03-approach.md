---
title: "Lemma's approach"
---


Lemma lets the issuer that ran the decision (a bank, a compliance team, a credit / sanctions data provider) emit the decision as a **predicate** — "not on the sanctions list", "credit score above threshold", "not resident in a restricted jurisdiction" — as an independent attribute proof. The underlying data (transaction history, credit information, the full list, query history) stays under the issuer; what travels to the verifier is only the ZK proof of the result.

The receiving side (counterparty, group company, auditor, AI agent) verifies "does it meet the bar" independently, without ever touching the underlying data. When, by whom, and that the decision was issued tamper-free is fixed via a provenance anchor (docHash) and stays reconstructable years later. The duplicate-screening work across firms and the leakage / defamation risk of shipping the basis come off the same design at once.

Where this proof-issuance layer drops into your existing credit / sanctions data providers, in-house decision pipelines, or compliance workflows is what the first conversation maps out.
