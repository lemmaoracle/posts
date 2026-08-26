---
slug: "incident-response-record-proof"
date: "2026.09.01"
category: "Solutions"
audience: business
industries: [retail]
coverPhoto: /assets/covers/incident-response-record-proof.jpg
section: "Essays"
title: "Prove the incident record was never rewritten"
abstract: "Hotels, restaurants, retailers and shopping centers all keep a record of when and how they handled a food-poisoning complaint or an injury. What no reader of that record can establish is whether it was edited afterwards. Fix the contents at the moment they are saved and head office no longer has to pull the original from the site: it can check the record in hand against the value registered at the time, on the spot, and confirm one thing only — that the response followed proper procedure and authority. The customer data and the details of the response are never disclosed."
tags:
  - provenance
  - audit-trail
  - compliance
  - incident-response
relatedLinks:
  - label: "Tamper-evident incident and complaint records (use case)"
    href: "https://lemma.frame00.com/solutions/use-cases/incident-response-record/"
  - label: "Store network compliance checks (licenses, hygiene, insurance)"
    href: "https://lemma.frame00.com/solutions/use-cases/store-network-compliance/"
  - label: "Tamper-evident internal control and approval flows"
    href: "https://lemma.frame00.com/solutions/use-cases/internal-control-approval-proof/"
  - label: "Proof for long-term contract records and quotes"
    href: "https://lemma.frame00.com/solutions/use-cases/long-term-contract-record/"
---

**TL;DR**

Hotels, restaurants, retailers and shopping centers all keep a record of when and how they handled a food-poisoning complaint or an injury. What no reader of that record can establish is whether it was edited afterwards. Even when the staff who handled it did everything right, being unable to show that the record has not been altered weakens the account you can give in a dispute or to a regulator.

Fix the contents at the moment they are saved and head office no longer has to pull the original — the record itself, customer data and response details included — from the site. It can check the record in hand against the value registered at the time, on the spot. What that confirms is one thing only: that the response followed proper procedure and authority. The contents stay closed.

## Can you say the record has not been rewritten since?

Say a restaurant receives a complaint that suggests food poisoning. The site reports to the public health office, issues a refund and puts preventive measures in place, all by the book. The response is written up and saved.

Six months later the same customer files a claim for damages. The operator's position is that it handled the matter exactly as the record says. But what is the answer to this question: "How do you prove that record was not edited after the fact?"

Separately from whether the response itself was correct, there is no way to demonstrate the reliability of the record. That is the real problem here.

## What today's records lack is proof that nothing was changed

Incident and complaint records usually sit in an internal system or a spreadsheet. There is nothing unusual about that.

The problem is that whoever wrote the record can edit it later. This is not a question of suspecting bad faith. As long as the mechanism permits editing, there is no way other than someone's word to show that nothing was edited. It is a structural problem.

There is a second gap: no way to demonstrate that a response was proper without producing the customer data and the details. Submitting the record in a dispute or to a regulator tends to mean disclosing personal and sensitive material along with it.

Both gaps close with two steps taken at the moment the record is finalized:

- Fix the contents of the record as a single unit
- Register that fixed value somewhere the author cannot edit

From then on, comparing the record you hold now against the value registered at the time is enough to establish whether anything changed. None of this replaces the records you already keep. The screen the site uses stays exactly as it is; saving simply adds one proof alongside it. The division of labor is plain — the site only saves, head office only checks, and only when it needs to.

## The site only saves

Nothing changes for the people on the floor. They enter the incident or complaint record as usual and save it.

On save, one proof issuance is requested automatically in the background. What travels is the hash of the record — a fixed-length value computed from the contents, which becomes a different value if even one character changes. The body of the record, customer data and response details included, is neither transmitted nor disclosed.

That single automatic request is the only thing added at the site. No forms to file, no extra fields to fill in for the sake of the proof. The value registered here becomes the reference point against which the record in hand is later checked.

## Head office only checks

When an incident record starts to matter — a dispute looks likely, a report to a regulator is due, or a routine compliance check comes around — the compliance or customer relations team at head office checks that record.

What head office actually wants to establish is not the substance of the record but a single point: whether the response followed proper procedure and authority. Until now, establishing that meant calling the site to ask, or requiring a detailed written report.

"Followed proper procedure and authority" here means exactly what the staff who handled the matter wrote down at the time: when, by what steps, by whom and under what authority. What Lemma proves is not that those statements are true, but that they have not been altered in any way since the record was written. This is not a mechanism for auditing from outside whether the internal manual was followed. It is a mechanism that fixes the record the site produced in a state nobody can amend afterwards.

So head office can confirm on the spot that the record has not been rewritten, without pulling the original from the site. Checking means comparing the hash computed from the record in hand against the hash registered with Lemma at the time of saving, and within seconds it shows:

- What was recorded, at that point in time, about proper procedure and authority
- That the record has not been tampered with (NOT TAMPERED)
- That there is no need to call the site to confirm

Very little data moves. The proof itself is around 200 bytes — about the size of a short text message — orders of magnitude smaller than having the site send the whole original file.

How that result reaches the checker is a choice. It can be a shared link opened on the spot, needing no login and no integration, or the result surfaced in an existing console or dashboard by API. Either way, the two values being compared are the same.

Once head office is confident here, the account it later gives to opposing counsel or to a regulator can follow whatever format and procedure those settings require. Lemma's verification result is not intended to be submitted to the other side as it stands. Being confident internally first is what supports the account you give externally.

<figure class="figure--diagram">
  <img src="/assets/figures/incident-response-record-fig1.en.svg" alt="The site saves the response record and its contents are turned into a code registered with the reconciliation registry; the checking side, the team at head office, matches the code made from the record in hand against the code as saved" />
  <figcaption>Figure 1 Checking the response record without exposing its contents</figcaption>
</figure>

## Why access control and redacted submissions cannot prove it

The cases this mechanism is for are the ones that need three things at once: hand something over without exposing the contents, let the other side verify independently, and detect any edit.

| Approach                               | Hand over without exposing | Independent verification | Detects edits |
| -------------------------------------- | -------------------------- | ------------------------ | ------------- |
| Access control and permissions         | △                          | ✗                        | ✗             |
| Masking or redaction before submission | △                          | ✗                        | ✗             |
| Encryption at rest and in transit      | ✓                          | ✗                        | ✗             |
| Incident monitoring alone              | △                          | ✗                        | ✗             |
| Issue a proof and verify independently | ✓                          | ✓                        | ✓             |

Access control limits who inside the company can read a record, but it has no answer to the suspicion that someone inside the company edited it. Redaction narrows what gets disclosed, but it adds work and leaves head office without the reference it needs — a registered hash — so it still cannot show that the original record is unaltered. Only when the right-hand two columns are both satisfied can head office check without pulling the original from the site.

## The procedure does not change across sites or formats

The more locations an operation spans — hotels, restaurants, retail, shopping centers — the more this pays. Head office does not have to review every site's records one by one: a proof is issued the moment each site saves, and checking happens only when something calls for it.

The same approach carries directly over to [internal control and approval flow records](https://lemma.frame00.com/solutions/use-cases/internal-control-approval-proof/) and to [proof for long-term contract records and quotes](https://lemma.frame00.com/solutions/use-cases/long-term-contract-record/). Looked at from the store network side, licenses, hygiene and insurance are covered in [store network compliance](https://lemma.frame00.com/solutions/use-cases/store-network-compliance/). Pick one target and start with a single flow.

## Not "the response was correct" but "the record has not been rewritten"

Whether a response was correct is, in the end, for people to judge. What Lemma can show is only that the record those people are judging from has not changed since it was written.

Head office can establish that on the spot, without calling the site. What gets asked in a dispute or a regulatory review is not that people take the correctness of the response on trust — it is that they be able to check the reliability of the record. Having settled that internally, in advance, is what supports the account you give when the moment comes.

Issuing a proof happens once per save. Checking is free, needs no key and no account, and can be run as many times as you like. Lawful handling of personal data and retention periods remains a precondition throughout.
