---
slug: "metawater-mizudako-resident-record-proof"
date: "2026.08.05"
category: "Solutions"
audience: business
industries: [public-sector]
coverPhoto: /assets/covers/metawater-mizudako-resident-record-proof.jpg
section: "Essays"
title: "Case study: proving residents' activity records are genuine — without exposing personal data"
abstract: "Lemma has been integrated into MizuDAkO, which supports community building around resident-led maintenance of water and sewerage infrastructure. Records created in the field can now prove their authenticity without revealing any personal data, making them dependable input for both administrative procedures and AI. Zero-knowledge proofs let a third party verify correctness without ever receiving the underlying data, and the design runs on legacy devices and constrained networks."
tags:
  - case-study
  - zk-proof
  - public-sector
  - privacy
  - provenance
---

**Deployed in Metawater's MizuDAkO.**

Lemma, the AI proof infrastructure for data trustworthiness, has been integrated into MizuDAkO — a programme that supports community building around resident-led maintenance of water and sewerage infrastructure. Records created in the field can prove their authenticity without revealing personal data, becoming dependable data that AI can rely on as evidence. The design runs comfortably under demanding conditions, including legacy devices and limited connectivity.

| Item             | Detail                                                                                                                                               |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Customer**     | Metawater Co., Ltd.                                                                                                                                  |
| **Project**      | MizuDAkO — supporting community building around resident-led maintenance of water and sewerage infrastructure                                        |
| **Lemma's role** | Attaching proof of authenticity to activity records without exposing personal data (zero-knowledge proofs)                                           |
| **Goal**         | Prove residents' activity records are genuine without exposing personal data, making them dependable enough for administrative procedures and AI use |

## About the project

MizuDAkO, proposed by Metawater, is a programme that works together with local residents to address the shortage of people available to maintain water and sewerage infrastructure as the population declines. In March 2025 the project signed a business partnership agreement with the town of Nishikawa, Yamagata Prefecture.

## The problem: recording activity in a way that holds up

To record this resident-led activity continuously — and to put it to work in administrative procedures and in AI — one thing has to come first: being able to retain _who did what, and when_ in a way that holds up.

## The approach: layering proof onto the record afterwards

Lemma was integrated into MizuDAkO, which the two companies have been developing together. Residents' activity is recorded with proof that it is genuine, while personal data stays hidden. It runs comfortably on legacy devices and over limited connectivity, and participants do not have to install a special app or prepare any particular device.

## The technology: proving it is genuine without handing over the contents

What makes this trustworthy is cryptography that proves _correctness alone_ without handing over the contents of the data. Much like an IC card that confirms your identity without ever showing your PIN, it confirms only that something is genuine, without revealing what it contains — and one of the core techniques behind this is the zero-knowledge proof. Because the raw data does not have to travel back and forth, it remains practical to operate where connectivity is limited, and records are handled as short codes from which the original cannot be reconstructed. Without disclosing sensitive details such as the nature of the work, a third party can later verify that a correct record genuinely exists and has not been tampered with.

## What this delivers

The design satisfies four things at once.

- **No change to what already works.** Proof is layered onto the record afterwards, without changing operations or the way the field works.
- **No data to hoard.** Because raw data is neither held nor transmitted, records can feed AI without adding to exposure risk.
- **Even under demanding conditions.** A robust design that runs on legacy devices and constrained networks.
- **Verifiable any number of times.** Proof is issued once. Verification is free, needs no key and no account, and can be performed from anywhere in the world, as often as you like.

## Document checks that hold; procedures that move

Without exposing personal data, each resident's activity accumulates alongside proof that it is genuine. It does not stall under difficult network conditions, and anyone — an administrative office, or an AI — can later confirm the record through the same proof.

What slows procedures down in the public sector is, more often than not, the work of checking documents. With a record that carries proof, the office receiving it can judge it genuine on the spot rather than waiting on an enquiry, and the same proof carries over to secondary use of open data. Only records that can be confirmed move on to the next procedure, and the next decision. MizuDAkO is the first example of this.

Related: [Use cases across industries](https://lemma.frame00.com/solutions/use-cases/)
