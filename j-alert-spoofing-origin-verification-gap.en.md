---
slug: "j-alert-spoofing-origin-verification-gap"
date: "2026.09.02"
category: "Industry"
audience: business
industries: [public-sector]
coverPhoto: /assets/covers/j-alert-spoofing-origin-verification-gap.jpg
section: "Essays"
title: "What the J-Alert reporting reveals: no proof of origin"
abstract: "On 30 August 2026, Kyodo News reported that the data J-Alert transmits over satellite carries neither encryption nor any function that guarantees its origin. The receiving end is managed in concrete detail by the operating rules, while nothing is provided that would let the receiving side establish whether an arriving warning is genuine. Less a fault in one system than a question common to the design of public infrastructure."
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

A J-Alert warning travels from the national government to a municipality's receiver by satellite. That receiver does not establish whether the data really came from the government. On 30 August 2026, Kyodo News [reported](https://www.tokyo-np.co.jp/article/512267) as much, and officials at the Ministry of Internal Affairs and Communications acknowledged it.

The work was done by Yudai Kirishiki of the cybersecurity firm Unknown Technologies, who obtained a receiver put on the second-hand market and analysed it: the satellite-delivered data carries no digital signature, and nothing else that would guarantee its origin. Transmit data in the same format from a height — using a drone, for instance — and the receiving side cannot tell it from a genuine warning.

This is less a fault in one system than a question common to the design of public infrastructure. The idea of proving who sent something was not among the assumptions the design started from. The same holds for telemetry from industrial equipment, exchanges between AI agents, the traffic that moves between financial institutions: wherever the truth of the data drives a decision and sender and receiver sit in different organisations, the same question comes up.

## The receiving end is managed in detail; the sending end cannot be checked

The receiving end is settled in the text of the [operating rules](https://www.fdma.go.jp/mission/protection/item/protection001_05_J-ALERT_gyomu_kitei_280322.pdf). Receivers may only be installed in an office, a branch office or a fire headquarters (Article 3); to begin receiving, an organisation files a form to apply for registration and must report promptly whenever the filed details change (Article 5).

And the sending end? On security the rules have just one provision, Article 12: appropriate measures shall be taken to ensure the necessary level of security, as prescribed by the head of the agency's Civil Protection Operations Office. What counts as ensuring it is set separately by that office, and the substance is not in the rules. The agency's answer — that it cannot disclose details for security reasons — is consistent with how the rules are built.

The receiving end is settled in this much detail. Nothing, meanwhile, is provided that would let the receiving side establish whether an arriving warning is genuine. That asymmetry is where this reporting sits.

## Spoofing satellite data — a blind spot since 2007

J-Alert entered service in 2007 in a limited number of local governments. Attacks on satellite communications were outside the assumptions of the time. Technical standardisation, however, has carried the problem forward unresolved for a long time.

3GPP has produced successive technical reports studying the security of Public Warning Systems (PWS). The latest of them, the Release 19 version (published in October 2025), carries a further accumulation of work on protecting warning notifications with digital signatures, yet it states plainly that the requirements for PWS Security are optional. One sentence in the same report goes further: "As PWS Security is an optional feature and several regions (US, Japan) have made clear that broadcast of signed Warning Notifications are unlikely, PWS Security may be deployed locally but not globally." The same sentence appears in the Release 12 version published in September 2014, and can be read in the [Release 13 version](http://www.arib.or.jp/english/html/overview/doc/STD-T63V12_00/5_Appendix/Rel13/33/33969-d00.pdf) published by ARIB, Japan's own standardisation body.

In the United States, a research team at the University of Colorado Boulder [demonstrated emergency alert spoofing](https://dl.acm.org/doi/10.1145/3307334.3326082) in 2019. Building a rogue base station from a commercially available software-defined radio ($500 to $1,300) and open-source software, they showed that a single unit at 0.1 watts delivers false alerts with a 90% success rate within a 23.4-metre radius outdoors and a 55.2-metre radius indoors; four units at one watt reach 49,300 of the 50,000 seats in a stadium. In 2026, an [open-source implementation for 5G](https://arxiv.org/abs/2604.24404) followed, together with a proposed defence based on cross-checking against neighbouring cells.

Research into the attack is advancing. The standards documents keep studying signature schemes. The layer that would bring that study down into Japanese operations has yet to be provided.

## The heart of the problem is a design choice, not a technical limit

Why is there no authentication? The research team's explanation runs as follows. To reach devices that have not yet attached to a network, and devices roaming on an unfamiliar operator, a design that demands no authentication was chosen. On top of that, because most alerts originate from local governments, centralised key management is hard to sustain.

It is a design that put arrival first and left the question of genuineness further back.

Within the technical constraints and operational limits of the day, it was a judgement that put saving lives first. It has been carried forward at every revision for nineteen years. Now that the technical options have widened so far, whether the same assumptions still hold is a question worth setting down once more. Article 15 of the operating rules requires that improvements to J-Alert be studied in a planned manner and shared with registered receiving organisations. A framework for continued improvement is written into the institution. What remains is how far that framework reaches toward checking whether what arrived is genuine.

## The design new infrastructure gets to choose

This is not a proposal to retrofit the existing J-Alert system. But the gap this reporting exposes — the absence of any mechanism for proving origin — is a design question common to every information delivery system still to be built.

A digital signature lets the receiving side verify mathematically that data came from a given sender and has not been altered. Where encryption conceals contents, a signature carries authenticity. For information meant to be read as widely as a warning, authenticity is the part that matters. The sender signs with its private key and the receiver verifies with the public key; the contents stay in the clear and carry a mark only the sender can issue. Forged data transmitted from a drone carries no signature, and the receiver can refuse it.

If you are adding this layer to a system whose delivery cannot stop, the practical place for it is outside the existing path. Splitting it by role shows where each piece goes.

| Layer | When | What it does |
|---|---|---|
| Issue | The moment the warning goes out | Normalise the warning, sign it with the issuer's key, fix the record |
| Reception | The moment the signal arrives | Verify the signature against the pre-distributed public key; refuse anything that fails |
| Collation | Afterwards, any number of times | Recompute the values from the warning text in hand and match them against the record made at issue |

The middle layer — reception — belongs to the radio stack, and it is exactly where 3GPP has schemes ready. Solve key distribution and the standard mechanisms suffice. The top and the bottom are needed separately: fix "who issued what, and when" as a single record at the moment of issue, and keep that record in a state where anyone can collate it without obtaining the original. This is what decides whether, immediately after a receiver has failed to reject a forgery over the air, you can establish what the genuine issue actually was.

We wrote the bottom layer in working code in [an audit trail for MCP tool calls](https://lemma.frame00.com/blog/mcp-tool-call-audit-trail/).

For what gets built next, that design is available to choose. National infrastructure has spent decades making delivery certain; the time has come to make it structurally certain that what arrived is genuine.

## Resources

- Exclusive: J-Alert "false warnings possible", experts say no origin guarantee (30 August 2026, Kyodo News) — [Tokyo Shimbun Digital](https://www.tokyo-np.co.jp/article/512267)
- J-Alert operating rules — [Fire and Disaster Management Agency](https://www.fdma.go.jp/mission/protection/item/protection001_05_J-ALERT_gyomu_kitei_280322.pdf)
- Overview of the J-Alert nationwide instant warning system — [Fire and Disaster Management Agency](https://www.fdma.go.jp/about/organization/post-18.html)
- 3GPP TR 33.969 Study on security aspects of Public Warning System (PWS) — [ARIB-published Release 13](http://www.arib.or.jp/english/html/overview/doc/STD-T63V12_00/5_Appendix/Rel13/33/33969-d00.pdf)
- This is Your President Speaking: Spoofing Alerts in 4G LTE Networks (MobiSys '19) — [ACM Digital Library](https://dl.acm.org/doi/10.1145/3307334.3326082)
- From Spoofing to Trust: Emergency Alerts Spoofing Testbed and Cross-Cell Verification (2026) — [arXiv](https://arxiv.org/abs/2604.24404)

*Facts are as of 2026-09-02. Amendments to the J-Alert operating rules and developments in 3GPP standardisation may change.*
