---
title: "Multi-Agent Workflows"
abstract: "An orchestrator agent assigns tasks to specialist agents. Those agents call MCP servers, invoke tools, return results. Without a provenance layer, the chain goes opaque. Lemma attaches a proof at each delegation node — the final output carries a verifiable trace back to the original principal."
thesis: "Delegated ≠ traceable"
pillar: agent-trust-chain
targetVerticals:
  - AI orchestration platforms
  - MCP-enabled tool ecosystems
  - Regulated industries with audit requirements
tags:
  - multi-agent
  - delegation-chain
  - MCP
  - audit-trail
relatedUseCases:
  - x402-commerce
  - delegated-treasury
---

# Use Case: Multi-Agent Workflows

## Thesis

**Delegated ≠ traceable.**

An orchestrator agent assigns tasks to specialist agents. Those agents call MCP servers, invoke tools, return results. Without a provenance layer, the chain goes opaque: who authorized whom, who saw what, who decided what. Lemma attaches a proof at each delegation node. The final output carries a verifiable trace back to the original principal.

## What Lemma Proves

- Authorization chain (principal → delegate → sub-delegate)
- Scope inherited at each step
- Inputs and decisions per node
- End-to-end auditability

## Problem

Modern AI workflows are inherently multi-agent: an orchestrator decomposes a task, delegates to specialists, aggregates results. Each specialist may invoke external tools via MCP, call APIs, or spawn sub-agents.

The problem is traceability. When something goes wrong — a wrong decision, a data leak, an unauthorized access — the audit trail is fragmented across logs, agent memory, and tool responses. No single party can reconstruct the full delegation chain:

- **Who** authorized the specialist agent to access that data?
- **What** scope was delegated at each step?
- **Which** inputs influenced the final decision?
- **How** did authority propagate from the original principal?

Current approaches rely on logging and monitoring — post-hoc reconstruction, not cryptographic proof. Logs are mutable, incomplete, and scattered across systems.

## How Lemma Changes It

Lemma provides **provable delegation chains** for multi-agent workflows:

1. **At delegation:** Each delegation step generates a ZK proof binding the delegator, the delegate, the scope, and the timestamp. The proof is anchored on-chain.
2. **At execution:** Each agent operation carries its delegation proof. Downstream tools and APIs can verify the caller's authority without trusting the agent's self-attestation.
3. **At aggregation:** The orchestrator assembles the final output with a complete proof chain — from the original principal through every delegation node to the final result.

The delegation chain is the audit trail. The proof is the evidence. The on-chain anchor is the guarantee that nothing was retroactively altered.