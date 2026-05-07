---
title: "Architecture"
---

# Architecture: Provable Delegation Chain

## Design Principle

**The delegation chain is the audit trail. The proof is the evidence. The on-chain anchor guarantees nothing was altered after the fact.**

```
┌─────────────────────────────────────────────────────────────┐
│                  ORCHESTRATOR (Principal)                     │
│                                                              │
│  ┌────────────────────┐    ┌────────────────────────────┐   │
│  │  Task Assignment    │───▶│  Lemma Delegation Prover    │   │
│  │  "Verify claim      │    │  - ZK proof: principal →    │   │
│  │   eligibility"      │    │    delegate, scope, time    │   │
│  └────────────────────┘    │  - On-chain anchor          │   │
│                             └─────────────┬──────────────┘   │
└───────────────────────────────────────────┼──────────────────┘
                                            │ delegation proof
                                            ▼
┌─────────────────────────────────────────────────────────────┐
│                  COMPLIANCE AGENT (Delegate)                   │
│                                                              │
│  ┌────────────────────┐    ┌────────────────────────────┐   │
│  │  Sub-delegation     │───▶│  Lemma Delegation Prover    │   │
│  │  "Retrieve patient  │    │  - ZK proof: delegate →     │   │
│  │   eligibility data" │    │    sub-delegate, scope      │   │
│  └────────────────────┘    │  - Scope: eligibility only  │   │
│                             └─────────────┬──────────────┘   │
└───────────────────────────────────────────┼──────────────────┘
                                            │ delegation proof
                                            ▼
┌─────────────────────────────────────────────────────────────┐
│                  DATA-RETRIEVAL AGENT (Sub-Delegate)           │
│                                                              │
│  ┌────────────────────┐    ┌────────────────────────────┐   │
│  │  MCP Server Call    │───▶│  Proof Carrier              │   │
│  │  "Query eligibility │    │  - Full delegation chain    │   │
│  │   fields only"      │    │  - Scoped to eligibility    │   │
│  └────────────────────┘    │  - Verification at server   │   │
│                             └─────────────┬──────────────┘   │
└───────────────────────────────────────────┼──────────────────┘
                                            │ scoped query + proof
                                            ▼
┌─────────────────────────────────────────────────────────────┐
│                  MCP SERVER (Tool Provider)                    │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Lemma Verification                                   │   │
│  │  1. Is the delegation chain intact (principal →        │   │
│  │     delegate → sub-delegate)?                          │   │
│  │  2. Does the requested scope fall within the            │   │
│  │     delegated scope at each level?                     │   │
│  │  3. Are all proofs still valid (not revoked)?          │   │
│  │  ✓ All checks pass → return eligibility data only      │   │
│  │  ✗ Scope exceeds delegation → reject query              │   │
│  └──────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

## Component Flow

1. **Delegation:** Each delegation step generates a ZK proof binding delegator → delegate → scope → timestamp. The proof is anchored on-chain via Poseidon hash.
2. **Sub-delegation:** When a delegate further delegates, the new proof references the parent proof. The scope can only narrow — it cannot exceed what was originally delegated.
3. **Execution:** The sub-delegate carries the full proof chain when calling MCP servers or tools. The server verifies the chain independently.
4. **Aggregation:** The orchestrator assembles the final output with a complete proof chain from original principal to final result. Every node in the chain is cryptographically bound.
