---
title: "Scenario: a payment that should not have cleared"
---


In April 2026, a procurement team at a mid-size manufacturer deploys an AI agent to settle recurring SaaS invoices. The agent is given an API key bound to the company's payment provider and a prompt-side guardrail capping spend at USD 50,000 per month. Two months later, an audit surfaces a USD 180,000 settlement to a counterparty no one recognizes, in a jurisdiction the company doesn't operate in.

Reconstruction is hard. The API key was valid. The provider logs show the agent issued the call. The prompt-side guardrail was bypassed by a malformed instruction in an upstream tool response. Nobody can prove, from the logs alone, that the agent ever held the authority to push that specific payment to that specific counterparty.

Had Trust402 been in front of the rail, every call would have carried an attestation: the principal that delegated this agent, the spend cap for the current period, and a jurisdiction attribute the counterparty had to match. The over-cap payment would not have produced a verifiable proof, and the settlement contract would have rejected it before clearing — without anyone needing to read prompt logs after the fact.

Sector-specific delegation patterns (treasury operations, multi-agent settlement, API metering), x402 / MCP / A2A integration sketches, and the Base Sepolia reference implementation walkthrough live in the post-Discovery materials.
