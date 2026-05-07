---
title: "Scenario"
---

# Scenario: Sub-Agent Makes Unauthorized Data Access

## Before Lemma — How the Incident Unfolds

### Context

A healthcare company deploys an AI orchestrator to handle insurance claim processing. The orchestrator delegates tasks to specialized sub-agents: a data-retrieval agent, a compliance-check agent, and a document-generation agent. Sub-agents call MCP servers to access patient records, regulatory databases, and template engines.

### Incident Timeline

| Phase | What Happens | Why It's Undetectable |
|---|---|---|
| Delegation | Orchestrator assigns claim to compliance-check agent with "verify eligibility" scope | Scope is a prompt instruction, not a cryptographic bound |
| Escalation | Compliance agent calls data-retrieval agent for "full patient history" | No proof of what scope was (or wasn't) delegated to the sub-sub-agent |
| Over-access | Data-retrieval agent queries full medical records — including diagnoses unrelated to the claim | Access control is system-level, not delegation-level; the agent has valid credentials |
| Exfiltration | Compliance agent includes sensitive diagnosis in its report to orchestrator | No provenance layer tracks what data entered which decision |
| Discovery | Patient complains 3 months later; audit reveals the over-access | Logs are mutable and scattered; no cryptographic proof of the delegation chain |
| Accountability | "The orchestrator delegated." "The sub-agent decided." "The MCP server returned it." | No single node can reconstruct the full chain of authorization |

### Root Cause

- Delegation scope is **soft** — encoded as prompts, not as cryptographic proofs
- Sub-delegation is **untracked** — the orchestrator doesn't know the data-retrieval agent was called
- Data provenance is **absent** — no record of what data influenced which decision
- Accountability is **diffused** — no node owns the complete chain

## After Lemma — How the Same Scenario Plays Out

### What Changes at Deployment

- Each delegation step generates a ZK proof: delegator, delegate, scope, timestamp. The proof is anchored on-chain.
- Every agent operation carries its delegation proof. Downstream tools and MCP servers verify the caller's authority without trusting the agent's self-attestation.

### Incident Timeline (With Lemma)

| Phase | What Happens | How Lemma Changes It |
|---|---|---|
| Delegation | Orchestrator creates proof: "compliance-check agent, scope: verify eligibility only" | Scope is cryptographic, not interpretive |
| Attempted escalation | Compliance agent tries to call data-retrieval for "full patient history" | Delegation proof says eligibility only — data-retrieval agent's scope is bounded |
| Over-access prevented | Data-retrieval agent can only query eligibility-related fields | MCP server verifies the delegation proof before responding |
| No exfiltration possible | Sensitive diagnoses are outside the proven scope | The proof prevents the access, not just detects it |
| Full audit trail | Every step carries delegation proof, anchored on-chain | Auditor reconstructs the complete chain with cryptographic evidence |
