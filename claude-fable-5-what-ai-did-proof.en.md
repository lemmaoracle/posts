---
slug: "claude-fable-5-what-ai-did-proof"
date: "2026.06.10"
category: "Industry"
section: "Essays"
title: "The more capable AI gets, the more you need proof of what it did — what Claude Fable 5 showed"
cover: "assets/16.jpg"
abstract: "On June 9, 2026, Anthropic released Claude Fable 5, its most capable model yet, and implemented at scale a safeguard that stops dangerous outputs at the model layer in high-misuse domains. But stopping a dangerous output and being able to prove, after the fact, what the AI actually did are two different layers. As capability and autonomy rise, enterprises increasingly need to show — to auditors and regulators — who acted, under whose authority, and how far they were permitted to go. Detection stops the intrusion; proof leaves an un-overturnable record of what happened. Lemma's trust infrastructure is designed to fill that step beyond detection."
tags:
  - ai-governance
  - agent-authority
  - verifiable-ai
  - regulatory-compliance
  - audit-trail
  - pre-execution-attestation
---

**TL;DR**

On June 9, 2026, Anthropic released a new AI model, Claude Fable 5. It is the most capable model the company has made generally available — strongest on long, complex work, and able to operate autonomously, without a human in the loop, for the longest stretch yet. Given that power, Anthropic shipped it with a safeguard: when a prompt touches high-misuse areas such as cyberattacks or biological and chemical questions, the model is kept from returning a dangerous answer.

Here is a line worth drawing. **Stopping a dangerous output and being able to prove, afterward, what the AI actually did are two different things.** The more capable AI becomes, the more work enterprises hand to AI agents. Did the agent they delegated to stay inside the authority it was granted — and can they show that in an audit or a regulatory review? This article is about that proof.

## 1. What happened — the strongest AI became available to everyone

Per Anthropic's announcement, Fable 5 is more capable than any model it has previously offered to the public: first on most benchmarks, with the gap widening the longer and more complex the task. It can keep working autonomously, without a human checkpoint, for longer than prior Claude models. In one company's early testing, it reportedly completed in a single day a large-scale code migration that would have taken more than two months by hand.

The flip side of that power is misuse risk. So Anthropic added a mechanism: when it detects prompts about cyberattacks, biology, chemistry, or illicit copying of the model, the response is handed off — not to Fable 5, but to a less-capable model (Claude Opus 4.8). This triggers on under 5% of traffic; the rest is answered by Fable 5 directly. Anthropic also released, to a limited set of partners, a higher-tier "Claude Mythos 5" with some capability limits removed for defenders.

The point is that Anthropic itself implemented, at scale, the judgment that "once capability crosses a threshold, dangerous outputs are stopped at the model layer." It is the moment AI governance became a concrete mechanism rather than a principle.

## 2. Dangerous outputs can be stopped. So who proves "under whose authority it acted"?

What the safeguard does is stop the AI from producing a dangerous answer. That is necessary. But stopping something and proving, afterward, an act that occurred are different jobs.

Suppose a financial institution delegates part of its transfers or reviews to an AI agent. Did that agent act within the authority it was given? Did it execute trades beyond its scope? On which data, and under which permissions, did it base its decisions? — None of these are answered by a mechanism that keeps the model from producing dangerous outputs. What a regulator, an auditor, or a court asks is not "did it utter something dangerous," but "**was it authorized.**"

What is more, the attacking side's AI gets just as smart. The smarter the attack, the more it behaves exactly like a legitimate user. At that point, detection that watches for "suspicious-looking behavior" becomes less and less reliable. When suspiciousness no longer distinguishes them, what remains is to record, before the act, "who was permitted and how far," in a form anyone can verify afterward.

Detection stops intrusions and dangerous outputs. Proof leaves, about what happened, an un-overturnable record of "who acted, under whose authority, and how far they were permitted." **Detection is not proof.**

## 3. Why proof, and why now

There are three reasons.

First, AI's autonomy has risen. Fable 5 has the longest autonomous operation yet, and enterprises are moving toward handing larger tasks to agents. The wider the delegation, the greater the need to have recorded "who was entrusted with what, and how far."

Second, the attacking side gets stronger too. This time Anthropic limited its strongest capability to defenders and constrained the general release. Even so, comparable capability spreads on a lag. Cases where an AI agent autonomously executed the bulk of an attack, and cases where the method is rebuilt for each attack, have already been observed in the wild (we decompose the structure in Lemma's Critical Briefs). In situations where detection that matches fixed patterns can't keep up, what remains is proof of "does this act carry legitimate authority."

Third, regulatory enforcement is intensifying. In finance, DORA has entered its first year of full enforcement, and under the EU AI Act the obligations for high-risk AI apply in full from August 2. Being able to show "what the AI you use actually did," in a form that holds up in audit and regulatory reporting, is itself becoming an internal-control requirement.

## 4. Where Lemma stands — the step *beyond* detection

Lemma does not replace the model's safeguards. *Beyond* them, it places an independent step that bridges detection and "legal and regulatory proof." Concretely, four layers.

- **Provenance proof:** make it verifiable that a piece of data, code, or communication has the right origin.
- **Verifiable AI:** fix the material an AI used for a decision in a form that can be checked afterward, without revealing its contents.
- **Agent authority proof:** have the agent prove, before a transaction, "in which role, up to what amount, how far," and stop out-of-scope acts before execution.
- **Regulatory attribute proof:** prove only that conditions are met — KYC, sanctions-list checks — without handing over the raw data.

The safeguard ensures the AI does not emit dangerous outputs. Outside of that, Lemma leaves, for a legitimately operating agent, evidence that it "stayed within the permitted scope" — evidence you can produce in an audit, a regulatory report, or litigation. They are layers with different jobs.

## 5. What it means for audit and control teams

Brought down to practice, three things remain.

First, alongside relying on the model-side safeguard, hold "proof of authority and attributes" before the transaction. The more sophisticated the attack, the less detection alone leaves evidence you can put into a regulatory report.

Second, make the delegation visible. The more you place AI agents in production, the lighter your audit and regulatory response becomes when you have recorded in advance "under whose authority, and how far permitted, it ran" — rather than digging back through logs afterward.

Third, make it verifiable across companies. Cryptographic proof can be checked across organizational lines, so against interconnected risk you can harden your defenses with proof rather than reconciling logs.

## Conclusion

On the day the strongest AI became available to everyone, Anthropic implemented, at scale, the judgment that "dangerous outputs are stopped at the model layer." That fact itself illuminates a structure. **The more capable AI becomes, the more you need not only to stop, but a layer that proves "what it did."**

Detection stops dangerous outputs; proof leaves, in an un-overturnable form, "under whose authority, and how far permitted, it acted." Lemma's trust infrastructure is designed to fill this step beyond detection, in a form that holds up to the accountability of audit and regulatory reporting.

### Read the incidents — Critical Brief

The argument here — "detection can't keep up; what remains is proof of whether it was authorized" — already shows up, line for line, in the structure of real incidents. From Lemma's Critical Briefs, which decompose real incidents through the "detection ≠ proof" lens, here are three:

- [No.009 GTG-1002 — AI agent ran 80–90% of an attack autonomously](https://lemma.frame00.com/critical/briefs/009-gtg1002-ai-orchestrated-espionage/) — the first reported case where "the shift from automation to autonomy" was actually observed. The structure where agent authority is not independently verified.
- [No.031 SHADOW-AETHER — AI executed from initial access through exfiltration](https://lemma.frame00.com/critical/briefs/031-vibe-hacking-shadow-aether/) — the structure where signature-based detection can't track attack tooling built per target.
- [No.018 hackerbot-claw — the first AI-vs-AI attack](https://lemma.frame00.com/critical/briefs/018-hackerbot-claw-ai-vs-ai/) — a case that tried to rewrite the defending AI's own instructions. The integrity of AI judgment.

Index: [Lemma Critical Brief](https://lemma.frame00.com/critical/briefs/)

### Learn more about this "proving layer"

The four layers touched on here (provenance proof, verifiable AI, agent authority proof, regulatory attribute proof) are each written up — how they work, and where in your operations they apply — on their own pages.

- Start from the whole picture: [**Trust Infrastructure — the four pillars**](https://lemma.frame00.com/pillars)
- The center of this article, the "under whose authority it acted" layer: [**Agent Authority Proof**](https://lemma.frame00.com/pillars/agent-authority-proof/)
- See it mapped to your own operations: [**Use cases**](https://lemma.frame00.com/solutions/use-cases)

### Get the beyond-detection story, monthly

Lemma Critical Monthly is built on a backbone of structural incident analysis (Critical Brief), delivering — once a month — the kind of "boundary between detection and proof" arguments this article makes. For audit, control, and security leaders who want to keep tracking the step that detection logs don't reach.

[**Subscribe to the newsletter →**](https://tally.so/r/rjvN2X)

If you are already weighing a concrete application in your operations, a 30-minute Discovery Call can map it to your specific workflows. You do not need to disclose any sensitive data (personal or confidential).

[Book a Discovery Call →](https://tally.so/r/Pd2Rl5)

**Sources (external only)**

- Anthropic, "Claude Fable 5 and Claude Mythos 5" (June 9, 2026) — https://www.anthropic.com/news/claude-fable-5-mythos-5
- Anthropic, "Project Glasswing" — https://www.anthropic.com/glasswing
