---
slug: "j-alert-spoofing-origin-verification-gap"
date: "2026.09.02"
category: "Industry"
audience: business
industries: [public-sector]
coverPhoto: /assets/covers/j-alert-spoofing-origin-verification-gap.jpg
section: "Essays"
title: "Who did this information come from? — Proof of origin for AI, seen through the J-Alert reporting"
abstract: >-
  Kyodo News reported that the data J-Alert sends over satellite carries no function that guarantees its origin.
  The same question matters when a company hands external information to an AI and lets it carry out work: who did this come from?
  Confirm the origin, confirm nothing was altered, and keep records of the decision and the execution that can be verified later.
  Confirming the origin, however, does not make the content — or the AI's judgement — correct.
tags:
  - j-alert
  - origin-verification
  - emergency-alerts
  - cybersecurity
  - infrastructure
relatedLinks:
  - label: "What authenticity means — verifying data, content and AI through provenance"
    href: "https://lemma.frame00.com/authenticity/"
  - label: "The last layer left to cyber defense in the age of AI"
    href: "https://lemma.frame00.com/blog/detection-is-not-proof/"
  - label: "Keeping an audit trail of MCP tool calls that can be verified later"
    href: "https://lemma.frame00.com/blog/mcp-tool-call-audit-trail/"
  - label: "@lemmaoracle/sdk (npm)"
    href: "https://www.npmjs.com/package/@lemmaoracle/sdk"
---

## Before the AI acts, can you confirm where the information came from?

Picture a notice from a supplier — "please change our bank account details" — that an AI reads and applies to your payment run.

Even if the AI understands the text correctly, whether that notice really came from the supplier is a separate thing to confirm. And whether that sender has the authority to change bank details is another.

This matters once you go beyond letting an AI read outside information and start letting it carry out the work: placing orders, making payments, updating customer records. Plausible-looking information fed straight into execution can lead to the wrong action.

The J-Alert reporting deals with the same question from the receiving side: how do you establish who something came from? Public warning systems and corporate AI differ in purpose and in mechanism, but they share this — information that arrives becomes the basis for acting.

It arrived ≠ its origin was confirmed

## What the J-Alert reporting pointed out

On 30 August 2026, Kyodo News [reported](https://www.tokyo-np.co.jp/article/512267) that the data J-Alert transmits over satellite carries no function guaranteeing its origin, leaving open the possibility of sending a false warning.

According to the report, Yudai Kirishiki of the cybersecurity firm Unknown Technologies analysed a second-hand receiver and found no mechanism — a digital signature, for instance — for confirming where the data came from.

The distinction worth drawing here is between "the information arrives" and "the information can be confirmed as having come from the legitimate sender." Being able to receive does not mean being able to establish the origin of what you received.

Emergency alerts on mobile networks have their own precedent: a [4G LTE spoofing study](https://dl.acm.org/doi/10.1145/3307334.3326082) published in 2019. That work targets cellular networks; it did not test J-Alert's satellite path. Keeping cases on different mechanisms distinct — rather than treating one as a demonstration against the other — matters.

Brought back to an enterprise AI rollout, the question is plain. When information arrives from outside, on what basis does the system confirm who sent it?

## Separate "who sent it" from "is the content correct"

One technique for confirming origin is the digital signature. The sender signs the data; the receiver checks it with a verification key already established as belonging to that sender. That establishes two things: it was signed with the corresponding signing key, and the content has not changed since.

Encryption keeps content from being read by third parties. Signatures are for confirming origin and detecting changes to content. Some workflows need both.

The reach of a signature is limited, though. If the supplier themselves sends the wrong account number, the information carries a valid signature and is still wrong. And if a signing key is stolen, verifying the signature alone will not distinguish the result from an action by the legitimate staff member.

Confirming the origin ≠ guaranteeing the content is correct

When you hand work to an AI, designing these checks as separate things helps business owners and developers hold the same picture.

| What to confirm                     | In the bank-details example                             | What it takes                                           |
| ----------------------------------- | ------------------------------------------------------- | ------------------------------------------------------- |
| Who it came from                    | Is this a notice from a registered supplier?            | A signature or authentication bound to the sender       |
| Whether it changed in transit       | Was the account number altered after signing?           | Verification of tampering, via signatures or similar    |
| Whether they may instruct it        | Is this person allowed to request the change?           | A check against authority and approval rules            |
| Whether the content may be acted on | Is the new account right, and the effective date sound? | A business-side check, with human approval where needed |

Beyond adopting signatures, you also have operational decisions: how a counterparty is bound to a key, how a leaked key is revoked, how a replayed old notice is spotted.

## Connect the check before execution to the record after it

There is a second thing worth thinking through: the explanation you owe once the processing is done.

Asked "why were the bank details changed to this account?", can you trace the notice you received, the result of the checks, the approval, and what the AI executed?

Logs are the starting point. Add a way to separately confirm that the stored log has not changed since it was registered, and the records you use for investigation and explanation become verifiable.

For instance: record the content as received along with the result of the checks, then bind the approval and execution records to the same transaction number. If each of those records can also be shown to be unchanged after the fact, everyone involved can work from the same record.

Being able to verify records afterwards, however, will not necessarily stop a fraudulent action before it executes. The check before execution and the verification after it each have their own role.

## The work at Lemma: records that can be verified later

Lemma's [audit trail for MCP tool calls](https://lemma.frame00.com/blog/mcp-tool-call-audit-trail/) publishes a worked implementation: register the record of an AI calling an external tool, then match against it later. MCP is the connection standard through which AI uses external tools and data.

In that example, a matching value computed cryptographically from the content of the record is registered. Later, the same computation is run on the record in hand and compared with the registered value. Technically, this uses a commitment built on Poseidon over BN254 — a value that lets content be matched after the fact. The method and the code are set out at the link.

What that match confirms is whether the record presented agrees with the content at the time of registration. It does not automatically vouch for information that was already wrong before registration, nor for the identity of the actor named in the record. Signatures or authentication to confirm the origin are still needed separately, and matching depends on retaining the original record and whatever the recomputation requires.

Updating your models does not remove the need to explain past processing. What information came in, what checks it passed, what was executed. Keeping that record in a verifiable form is the ground on which AI stays usable in the business over time.

Models change. Proofs remain.

## What's next — five things to confirm in your next AI rollout

Start by picking one workflow that takes in outside information and executes something, then work through this order.

- Map the entry points. Email, external services, supplier systems — lay out where the information the AI uses for its decisions comes from.
- Decide how origin is confirmed. On what basis you establish the counterparty, and who updates that when staff or keys change.
- Decide the scope of execution. Separate the authority to send information from the authority to execute and approve work, and set approval conditions by amount and by type of processing.
- Decide what happens when you cannot confirm. Hold the processing, route it to a person — matched to the business impact.
- Keep records, and try matching them. Bind what was received, the result of the checks, the approval and the outcome, then confirm you can detect later changes to those records.

For companies operating AI agents, developers building on MCP or x402, and teams automating work that crosses organisations: which workflows should carry origin confirmation and verifiable records? Our [PoC consultation](https://tally.so/r/xX0VYv) is a place to work that out from a concrete workflow.

Built for decisions that matter.

## Resources

- "J-Alert: false warnings possible" — expert finds no guarantee of origin (30 August 2026, Kyodo News) — [Tokyo Shimbun Digital](https://www.tokyo-np.co.jp/article/512267)
- Overview of the J-Alert nationwide instant warning system — [Fire and Disaster Management Agency](https://www.fdma.go.jp/about/organization/post-18.html)
- This is Your President Speaking: Spoofing Alerts in 4G LTE Networks (MobiSys '19) — [ACM Digital Library](https://dl.acm.org/doi/10.1145/3307334.3326082)
