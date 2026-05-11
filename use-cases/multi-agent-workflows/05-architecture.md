---
title: "Architecture in concept"
---


Lemma does not replace your orchestration framework or your MCP servers. We add a proof at each delegation node and a verification gate on the tool / API side.

<!-- TODO: replace with a Mermaid or SVG diagram. Hierarchical layout: Principal → Orchestrator → Sub-agents → Tools. -->
<!-- Temporary placeholder:

  [Original principal: organization / user]
       │ (delegation + signature → ZK proof)
       ▼
  [Orchestrator agent]
       │ (re-delegation + scope tightening)
       ▼
  [Sub-agent A]        [Sub-agent B]
       │                    │
       ▼                    ▼
  [MCP tools / APIs: verify caller's delegation proof]
       │
       ▼
  [Final output + complete proof chain]
       │
       ▼
  [On-chain anchored]
-->

Every node's delegation is bound as a ZK proof and passed downstream. Scope can narrow on re-delegation but never widen, and the proof chain grows with each handoff. Tools and APIs verify the caller's cryptographic authority directly — no reliance on agent self-attestation. End-to-end you get a continuous, verifiable path from the original principal to the final action.

Integration with existing orchestration frameworks (LangChain, AutoGen, Claude Computer Use, etc.), MCP / A2A protocol plug-in patterns, and delegation scope-tightening logic design are detailed in the whitepaper and the post-call technical kit.
