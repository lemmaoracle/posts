---
slug: "metawater-mizudako-resident-record-proof"
date: "2026.08.05"
category: "Announcements"
audience: business
industries: [public-sector]
coverLines:
  - "Prove the record"
  - "without the data."
section: "Essays"
title: "Proving residents' activity records are genuine — without exposing personal data"
abstract: "Lemma has been integrated into MizuDAkO, which supports community building around resident-led maintenance of water and sewerage infrastructure. Records created in the field can prove their authenticity without revealing any personal data, becoming dependable data that AI can rely on as evidence. The design runs comfortably under demanding conditions, including legacy devices and limited connectivity. The plan is Lemma Civic, the base plan for local government."
tags:
  - case-study
  - zk-proof
  - public-sector
  - privacy
  - provenance
relatedLinks:
  - label: "Keep the AI decision together with the data it was looking at"
    href: "https://lemma.frame00.com/blog/ai-decision-record-with-inputs/"
---

**TL;DR**

Lemma has been integrated into MizuDAkO, which supports community building around resident-led maintenance of water and sewerage infrastructure. Records created in the field can now prove their authenticity without revealing personal data, becoming dependable data that AI can rely on as evidence. The design runs comfortably under demanding conditions, including legacy devices and limited connectivity.

| Summary        |                                                                                  |
| -------------- | -------------------------------------------------------------------------------- |
| Customer       | Metawater Co., Ltd.                                                              |
| Project        | MizuDAkO (resident-participation project)                                        |
| Sector         | Local government and public services                                             |
| Plan           | Lemma Civic                                                                      |
| What is proved | The authenticity of residents' activity records, without revealing personal data |

## The problem

MizuDAkO, proposed by Metawater, is a programme that works together with local residents to address the shortage of people available to maintain water and sewerage infrastructure as the population declines. In March 2025 the project signed a business partnership agreement with the town of Nishikawa, Yamagata Prefecture.

To record this resident-led activity continuously — and to put it to work in administrative procedures and in AI — one thing has to come first: being able to retain who did what, and when, in a way that holds up.

## The solution: Lemma Civic

Lemma, our own AI proof infrastructure, was newly integrated into MizuDAkO, which the two companies have been developing together. Residents' activity is recorded with proof that it is genuine, while personal data stays hidden. It runs comfortably on legacy devices and over limited connectivity, and participants do not have to install a special app or prepare any particular device.

The plan is Lemma Civic, the base plan for local government. It is the standard-template configuration — issuing proofs against records that already follow a fixed format — so where the conditions fit, it can be up and running quickly.

## How it works

What makes this trustworthy is cryptography that proves correctness alone, without handing over the contents of the data. Much like an IC card that confirms your identity without ever showing your PIN, it confirms only that something is genuine, without revealing what it contains — and one of the core techniques behind this is the zero-knowledge proof. Because the raw data does not have to travel back and forth, it remains practical to operate where connectivity is limited, and records are handled as short codes from which the original cannot be reconstructed. Without disclosing sensitive details such as the nature of the work, a third party can later verify that a correct record genuinely exists and has not been tampered with.

Whichever AI model is used, and however it evolves, the proof of authenticity holds.

The same structure extends to the AI's own judgments. Keeping a decision and the data it referenced as a single record is covered in "[Keep the AI decision together with the data it was looking at](https://lemma.frame00.com/blog/ai-decision-record-with-inputs/)."

## What it delivers

- **No change to what already works.** Proof is layered onto the record afterwards, without changing operations or the way the field works.
- **No data to hoard.** Because raw data is neither held nor transmitted, records can feed AI without adding to exposure risk.
- **Even under demanding conditions.** A robust design that runs on legacy devices and constrained networks.
- **Verifiable any number of times.** Proof is issued once. Verification is free, needs no key and no account, and can be performed from anywhere in the world, as often as you like.

## Talk to us

For those responsible for digital transformation in local government and public infrastructure, for systems integrators, and for companies that want to put AI to work without letting data leave their control. What can be proved, and which process to start from — we will work through it with you, starting from a 30-minute conversation.

[Talk to us about adoption →](https://tally.so/r/EkBqDX)

## Resources

- [Press release (PR TIMES, in Japanese)](https://prtimes.jp/main/html/rd/p/000000072.000018679.html)
- [Use cases across industries](https://lemma.frame00.com/solutions/use-cases/)
- [Plans and pricing](https://lemma.frame00.com/pricing/)
