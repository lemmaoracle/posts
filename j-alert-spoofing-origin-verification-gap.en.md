---
slug: "j-alert-spoofing-origin-verification-gap"
date: "2026.09.02"
category: "Industry"
audience: business
industries: [public-sector]
coverPhoto: /assets/covers/j-alert-spoofing-origin-verification-gap.jpg
section: "Essays"
title: "What the J-Alert reporting reveals: no proof of origin"
abstract: "On 30 August 2026, Kyodo News reported that the data J-Alert transmits over satellite carries neither encryption nor any function that guarantees where it came from. Receivers are registered and tightly managed; the mechanism for verifying the authenticity of the sender sits outside the public record. That asymmetry is what a lack of resistance to impersonation looks like in national infrastructure."
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
  - label: "Lemma API — provenance, authentication, authorization, inference, attributes"
    href: "https://lemma.frame00.com/pillars/"
  - label: "Glossary"
    href: "https://lemma.frame00.com/glossary/"
---

**TL;DR**

On 30 August 2026, Kyodo News reported that the data J-Alert transmits over satellite carries neither encryption nor any function that guarantees its origin. On the receiving side, everything is tightly managed: where a receiver may be installed, its control number, its registration. How the authenticity of the sender is established sits outside the public record.

That asymmetry is not a defect peculiar to J-Alert. It is the result of public warning systems as a class prioritising availability and deferring authenticity. 3GPP's technical report has named the US and Japan, for more than a decade, as regions unlikely to broadcast signed warning notifications. Build the layer that lets a receiver cryptographically verify the sender into the next generation of alerting infrastructure from the start — that is the design question this reporting raises.

"Did the information that arrived really come from the sender it claims?" When the receiving side cannot establish that, is a warning still functioning as a warning?

On 30 August 2026, Kyodo News published [a report](https://www.tokyo-np.co.jp/article/512267): the data transmitted via satellite in the J-Alert nationwide instant warning system has no encryption and no function guaranteeing its origin. Officials at the Ministry of Internal Affairs and Communications acknowledged as much.

What the report exposes is not simply a technical vulnerability. It is a structure in which the idea of proving where information came from was absent from the design of national infrastructure from the very beginning.

## The one function J-Alert never had

[J-Alert](https://www.fdma.go.jp/about/organization/post-18.html) delivers information that leaves no time to react — ballistic missile launches, earthquake early warnings — from the national government to municipalities in an instant. The Fire and Disaster Management Agency transmits over both satellite and terrestrial lines; receivers at each municipality pick up the signal and automatically trigger local disaster radio and other channels. Delivery is confirmed four times a year in nationwide tests, and each receiver is registered per municipality by control number and MAC address.

Yutaka Kiriashiki of the cybersecurity firm Unknown Technologies obtained a receiver that a local government had owned and later put on the second-hand market, and analysed it. The analysis confirmed that J-Alert's satellite-delivered data carries no digital signature or other mechanism guaranteeing its origin.

In other words, the receiver accepts data without establishing whose data it is. Transmit data in the same format to a receiving antenna from a height — using a drone, for instance — and the receiving side has no way to tell a forgery from the real thing.

## There is still no way to tell a fake disaster warning from a real one

Management of the receiving end is strict. Receivers may only be installed in designated offices or fire headquarters, and reports of decommissioning or suspension must carry the organisation name, control number, MAC address and reason. The rules even distinguish between the two: "decommissioning" deletes the registration entirely and makes reinstallation impossible, while "suspension" keeps the registration but removes the unit from monitoring.

And the sending end?

Article 12 of the operating rules requires the Fire and Disaster Management Agency and the receiving organisations to "ensure the necessary level of security", but the substance of that requirement is to be set separately by the head of the agency's Civil Protection Operations Office, and it is not published. The agency's answer — that it cannot disclose details for security reasons — is consistent with how the rules are built.

The management of the receiving end can be read out of public documents in concrete terms. How the authenticity of the sender is established cannot. That asymmetry is where this reporting sits.

## Spoofing satellite data — a blind spot since 2007

J-Alert entered service on 9 February 2007 with a limited set of local governments. Attacks on satellite communications were outside the assumptions of the time. Technical standardisation, however, has carried the problem forward unresolved for a long time.

3GPP has produced successive technical reports studying the security of Public Warning Systems (PWS). The latest, Release 19 (published October 2025), continues to accumulate work on protecting warning notifications with digital signatures. Yet the requirements for PWS Security are explicitly stated to be optional.

One sentence goes further: "As PWS Security is an optional feature and several regions (US, Japan) have made clear that broadcast of signed Warning Notifications are unlikely, PWS Security may be deployed locally but not globally." The same sentence appears in the Release 12 version published in September 2014, and can be read in the [Release 13 version](http://www.arib.or.jp/english/html/overview/doc/STD-T63V12_00/5_Appendix/Rel13/33/33969-d00.pdf) published by ARIB, Japan's own standardisation body. A judgement recorded more than a decade ago has been carried forward at every revision since.

In the United States, a research team at the University of Colorado Boulder [demonstrated emergency alert spoofing](https://dl.acm.org/doi/10.1145/3307334.3326082) in 2019. Using a commercially available software-defined radio costing under $1,000 and open-source software, they showed that a one-watt transmitter could deliver false alerts to devices within roughly a one-kilometre radius. In 2026, an [open-source implementation for 5G standalone](https://arxiv.org/abs/2604.24404) followed, together with a proposed defence based on cross-checking against neighbouring cells.

Research into the attack is advancing. The standards documents keep studying signature schemes. What Japanese operations lack is the layer that would turn that study into something real.

## The heart of the problem is a design choice, not a technical limit

Why is there no authentication? The research team's explanation runs as follows.

To reach devices that have not yet attached to a network, and devices roaming on an unfamiliar operator, a design that demands no authentication was chosen. On top of that, because most alerts originate from local governments, centralised key management is hard to sustain.

It is a design that prioritised availability and paid for it with authenticity.

This is not a judgement to be condemned. Within the technical constraints and operational limits of the day, it put saving lives first. The problem is that the judgement has been carried forward at every revision for nineteen years, and remains unchanged now that the technical options have widened enormously.

The operating rules include a provision requiring that improvements to J-Alert be studied in a planned manner and that the resulting information be shared with receiving organisations. A framework for continued improvement is written into the institution. The question is how far that framework extends to verifying authenticity.

## "It arrived" can be proven. "Who sent it" cannot

J-Alert has two transmission paths: satellite and terrestrial line. The operating rules define both and require receivers to be connected to a terrestrial line. Whether current receivers cross-check the two to judge authenticity cannot be established from public documents, but the fact that multiple paths exist is material for anyone thinking about countermeasures.

Nor, at the time of the reporting, is there a clear route for getting a finding like this to the right place. The public-private framework under the Act on Enhancement of Cyber Response Capabilities, enacted in May 2025, begins operating on 1 October 2026 — but where does a finding go when it concerns an undisclosed area of specification in warning infrastructure the state itself operates? The debate over how vulnerability information should be handled is still running.

## The design new infrastructure gets to choose

This is not a proposal to retrofit the existing J-Alert system. But the gap this reporting exposes — the absence of any mechanism for proving origin — is a design question common to every information delivery system still to be built.

A digital signature lets the receiving side verify mathematically that data came from a given sender and has not been altered. Where encryption conceals contents, a signature carries authenticity. For information meant to be read as widely as a warning, authenticity is the part that matters.

One way to implement it: the sender signs the data with its private key, and the receiver verifies with the public key. The contents stay in the clear, and they carry a mark only the sender can issue. The receiver accepts the data only after verifying that mark. With that in place, forged data transmitted from a drone carries no signature, and the receiver can refuse it.

Signed warnings have been studied at 3GPP for more than a decade, and the schemes exist. The problem is that Japanese operations did not choose that layer.

When you design a new system, build in from the start the layer that lets the receiving side cryptographically verify that the organisation which sent the data is the organisation it claims to be. This is technically feasible, and the existing standardisation work already holds a body of study on it.

National infrastructure has spent decades making delivery certain. Making it structurally checkable that what arrived is genuine is the next thing — and for what gets built next, that design is available to choose.

## Resources

- Exclusive: J-Alert "false warnings possible", experts say no origin guarantee (30 August 2026, Kyodo News) — [Tokyo Shimbun Digital](https://www.tokyo-np.co.jp/article/512267)
- J-Alert: receivers are registered and managed; verification of the sender sits outside the public record — [Innovatopia](https://innovatopia.jp/cyber-security/cyber-security-news/117374/)
- Overview of the J-Alert nationwide instant warning system — [Fire and Disaster Management Agency](https://www.fdma.go.jp/about/organization/post-18.html)
- 3GPP TR 33.969 Study on security aspects of Public Warning System (PWS) — [ARIB-published Release 13](http://www.arib.or.jp/english/html/overview/doc/STD-T63V12_00/5_Appendix/Rel13/33/33969-d00.pdf)
- This is Your President Speaking: Spoofing Alerts in 4G LTE Networks (MobiSys '19) — [ACM Digital Library](https://dl.acm.org/doi/10.1145/3307334.3326082)
- From Spoofing to Trust: Emergency Alerts Spoofing Testbed and Cross-Cell Verification (2026) — [arXiv](https://arxiv.org/abs/2604.24404)

*Facts are as of 2026-09-02. Amendments to the J-Alert operating rules and developments in 3GPP standardisation may change.*
