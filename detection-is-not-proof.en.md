---
slug: "detection-is-not-proof"
date: "2026.05.22"
category: "Business Strategy"
section: "Essays"
title: "The last layer left in AI-era cyber defense"
cover: "assets/bMBGwXIAgC4.jpg"
abstract: "In ten days, Japan's AI cyber defense response cascaded from cabinet directive to direct implementation requests aimed at critical infrastructure operators and local governments. Every measure asked for sits squarely on the detection side. What current operating models still do not treat as a distinct layer is the provenance layer: a cryptographic record that proves, before a transaction settles, who delegated what authority, to whom, and how far."
tags:
  - provenance
  - verifiable-ai
  - audit-trail
  - agent-security
  - pre-execution-attestation
---

**TL;DR**

In ten days, Japan's AI cyber defense response cascaded from cabinet directive to direct implementation requests aimed at critical infrastructure operators and local governments. Every measure asked for — vulnerability assessments, patch rollouts, budget and headcount — sits squarely on the detection side. What current operating models still do not treat as a distinct layer is the *provenance layer*: a cryptographic record that proves, before a transaction settles, who delegated what authority, to whom, and how far. Lemma builds that layer across multiple domains, starting with payments (Trust402) and authentication.

**Detection ≠ Proof**

**Log monitoring ≠ Guardrail**

---

## ▸ Domestic momentum: AI cyber defense reaching the field

In May 2026, Japan's government walked the AI cyber defense staircase down to the field in ten days. On May 12, the Prime Minister issued the directive. On May 18, a multi-ministry council released a four-pillar package: intelligence aggregation, response capacity, international cooperation, vendor patching. On May 21, the Minister for Internal Affairs and Communications convened telecom operators, NHK, the commercial broadcasters' association, Japan Post, and the National Governors' Association — and asked them directly. The backdrop is Anthropic's Claude Mythos Preview, released on April 7 under Project Glasswing, a model whose automated zero-day discovery capability is now widely recognized as a dual-use risk.

What matters is the substance of those requests. Vulnerability assessments, prompt patching, budget and headcount allocation, intelligence aggregation, personnel development, vendor-side patching — every line item resolves to either detection-layer hardening or post-detection containment. Japan's Active Cyber Defense Act, taking effect on October 1 across 15 sectors and roughly 250 designated entities, runs on the same axis.

## ▸ The shared gap: detection alone cannot close it

The bridge from detection to legal proof has not yet emerged as a distinct layer in current security operating models. This is not specific to any single response — it reflects the worldview the industry has shared so far.

Three problems stack. First, Mythos-grade AI attacks leave no attribution residue. Second, a detection tool's confidence score is not evidence admissible in regulatory filings, administrative proceedings, or court. "99.7% probability of anomaly" does not establish that an unauthorized authority was exercised. Third, in an agent-mediated world, the delegation graph itself — who authorized what, to whom, to what limit — becomes the audit target, and logs alone cannot reconstruct it.

Across domains, the structural commonality is the same. MetLife Japan disclosed on May 1, 2026, that employees stationed across 36 bank-channel agencies had exfiltrated 2,476 internal files over four and a half years, undetected. SolarWinds-class supply-chain compromises went unnoticed for years. Municipal contractor-side leaks involving resident records and medical vendor intrusions follow the same pattern. The list runs across sectors and converges on one form: incidents that stayed hidden left no usable post-hoc trail.

## ▸ Why the gap widens at AI speed

So far this is the human-speed story. MetLife's four and a half years of zero detection unfolded in a world where attackers and operators were human, and API call rates were within human supervisory bandwidth.

With agents, delegation-to-execution collapses to seconds or minutes. A detection cycle that resolves "N hours after the event" cannot keep up with batch-delegated agent authority. The absence of usable audit trail — a state that took MetLife four and a half years to reach — can be reached in seconds.

The history of security operations is a history of where humans sit on the loop. Human In The Loop (HITL) places human approval after machine judgment. Human Off The Loop (HOTL) removes individual approval and asks machines to verify machine decisions, machine-to-machine. At AI-agent operating speeds, HITL is implementable but not operable; HOTL is the only option that scales. The honest question follows: why would a world without per-transaction human approval be safe? The honest answer: because the system is cryptographically attesting compliance on the human's behalf.

## ▸ Lemma's answer: guardrails inside the system

Strengthening detection further does not close the structural gap. What closes it is moving guardrails inside the system. Generate cryptographic proof — of who, to whom, what authority, to what limit — *before* the transaction settles. Not post-hoc forensic recovery, but pre-execution attestation. Two conditions define this layer: verifiability (any third party can re-check) and pre-emptiveness (the record exists before the trade clears).

Lemma builds this inside-the-system guardrail across multiple domains, rolling it out in sequence. The axes currently public are payments and authentication.

**Payments — Trust402.** When AI agents pay or delegate authority across the x402 economy, Trust402 embeds cryptographic proof inside the payment protocol itself. Who, with what authority, up to what limit, paid to whom — proven before settlement, with Groth16 over BN254 and Poseidon-hashed commitments, circuits built in Circom. Verification is independently reproducible by third parties. The design does not chase the trade after the fact; it ensures only authorized payments execute, from inside the system.

**Authentication.** Lemma is building a guardrail on the same structural principle in the authentication domain. Attribute proofs (BBS+ over BLS12-381) and provenance proofs verified machine-to-machine replace the per-interaction "human visual check" with a machine ZK check. Details to follow. The inside-the-system guardrail that supports HOTL does not stop at payments, nor at authentication. Lemma will continue to extend this provenance layer into additional domains.

Models change. Proofs remain.

## ▸ Where to start

| Step | What | URL |
|---|---|---|
| 1. See the demo | Trust402 (payments guardrail) and provenance proofs in action | demo.lemma.frame00.com |
| 2. Build with us | Trust402 API beta / waitlist | https://tally.so/r/kd0bZR |
| 3. Talk to us | Whitepaper / enterprise PoC consultation | https://tally.so/r/xX0VYv |

For critical infrastructure CISOs, financial regulators, cyber policy operators, AI agent platforms, and x402 / MCP builders — the entry point is here, not as a detection-layer complement, but as the guardrail that sits inside the system.

Built for decisions that matter.

## ▸ Resources

- Demo: https://demo.lemma.frame00.com
- Microsite: https://lemma.frame00.com
- Whitepaper waitlist (enterprise): https://tally.so/r/xX0VYv
- Developer waitlist (Trust402 / API): https://tally.so/r/kd0bZR
- PM directive (Jiji, 2026-05-12): https://www.jiji.com/jc/article?k=2026051200308&g=pol
- Multi-ministry package briefing (NISC): https://www.cyber.go.jp/pdf/press/20260518_AI_CS_kankeishouchoukaigi.pdf
- Multi-ministry package coverage (Nikkei, 2026-05-17): https://www.nikkei.com/article/DGXZQOUA170RS0X10C26A5000000/
- 5/21 industry-wide meeting (Kobe Shimbun, 2026-05-21): http://www.kobe-np.co.jp/news/zenkoku/compact/202605/0020369858.shtml
- MetLife Japan 4.5-year / 2,476-file exfiltration (Diamond, 2026-05-01): https://diamond.jp/articles/-/388708
- MetLife Japan coverage (Toyo Keizai, 2026-05-01): https://toyokeizai.net/articles/-/942994?display=b
- MetLife Japan precursor coverage (Nikkei, 2026-04-30): https://www.nikkei.com/article/DGXZQOUB301MD0Q6A430C2000000/
- Project Glasswing (Anthropic, 2026-04-07): https://www.anthropic.com/glasswing
- Japan's Active Cyber Defense Act (Cabinet Secretariat): https://www.cas.go.jp/jp/seisaku/cyber_anzen_hosyo_torikumi/index.html
