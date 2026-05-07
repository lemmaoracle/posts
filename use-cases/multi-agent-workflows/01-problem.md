---
title: "Problem"
---


When an orchestrator agent delegates tasks to sub-agents, the delegation chain goes opaque. Who authorized whom? Who saw what? Who decided what? Without a provenance layer, these questions cannot be answered — not by the orchestrator, not by the auditor, not by the sub-agent itself.

Current approaches rely on logging and monitoring: post-hoc reconstruction from mutable, fragmented, system-scattered records. Logs can be altered. Context windows expire. Tool responses are ephemeral. When something goes wrong — a wrong decision, a data leak, an unauthorized access — there is no cryptographic proof of how it happened.

**Delegated ≠ traceable.** That is the structural gap Lemma closes.
