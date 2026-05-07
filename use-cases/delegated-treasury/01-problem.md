---
title: "Problem"
---

# Problem: Authorized ≠ Attested

When an enterprise delegates spending authority to an AI agent, the constraints are typically enforced as soft prompts: "don't spend more than $500." This is a suggestion, not a guarantee. Prompt injection, context overflow, or adversarial negotiation can override it.

Platform guardrails enforce limits centrally — but they're opaque and not portable. A seller has no way to independently verify the buyer's authority without trusting the platform.

Without cryptographic proof that the agent's spend authority is real, current, and bounded, sellers must either trust the agent's claim or reject the transaction. Both are suboptimal.

**Authorized ≠ attested.** That is the structural gap Lemma closes.
