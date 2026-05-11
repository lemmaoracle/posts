---
title: "A real-world example: who touched that customer record"
---


Picture a financial institution's AI workflow. An orchestrator delegates "credit decision" to a credit-evaluation agent. That agent queries an external credit bureau via MCP, while a second agent reads and formats customer transaction history from the in-house CRM. The orchestrator aggregates the decision at the end.

A few days later, the customer files a disclosure request: "Which agents looked at which fields of my transaction history, and for what purpose?" Even joining internal logs, MCP server logs, and each agent's context history, you cannot cryptographically prove the delegation scope at each step, or whether the data accesses stayed within authority. Reconstruction takes days, and even then the evidence is not non-disputable.

With Lemma in place, the final output carries the full delegation-proof chain — orchestrator → credit agent → MCP tool / CRM-formatting agent. The disclosure request can be answered directly, with no reconstruction: each step's authority and data access is already a cryptographic fact. Every proof is anchored on-chain, so post-hoc tampering is structurally detectable.

Industry-specific delegation scope design, MCP-server and A2A integration patterns, and alignment with regulatory obligations (GDPR data-subject requests, healthcare privacy, financial audit) are shared in the sector-specific kit we send after the consultation call.
