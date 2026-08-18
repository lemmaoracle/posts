---
slug: "ai-decision-record-with-inputs"
date: "2026.08.18"
category: "Solutions"
audience: business
industries: [manufacturing, public-sector, ai]
coverPhoto: /assets/covers/ai-decision-record-with-inputs.jpg
section: "Essays"
title: "Keep the AI decision together with the data it was looking at — records that still hold up six months later"
abstract: "After AI is introduced into equipment anomaly detection, false alarms often follow. If you cannot separate a problem in the input data from a problem in the AI's judgment, the only remaining move is to turn the automation off. Keep the AI's decision and the data that decision referenced as a single record, and you can show the path itself — in incident reports and in sorting out who was responsible."
tags:
  - verifiable-ai
  - provenance
  - audit-trail
  - ai-governance
relatedLinks:
  - label: "Manufacturing and critical infrastructure use cases"
    href: "https://lemma.frame00.com/solutions/use-cases/manufacturing/"
  - label: "AI adoption use cases (cross-industry)"
    href: "https://lemma.frame00.com/solutions/use-cases/ai/"
  - label: "Proving resident activity records are genuine — without exposing personal data (MizuDAkO)"
    href: "https://lemma.frame00.com/blog/metawater-mizudako-resident-record-proof/"
---

**TL;DR**

After AI is introduced into equipment anomaly detection, false alarms often follow. If you cannot tell whether the cause sits in the input data or in the AI's judgment, there is little left to do beyond dulling the threshold or switching the automatic detection off. The AI's decision, and the data that decision referenced. Keep those two as one record, in a form nobody can alter afterwards, and false-alarm triage, incident reporting, and questions of responsibility can all proceed by showing the path itself.

When false alarms keep coming, the first question on site is where to fix things. But if you cannot separate a problem in the input data from a problem in the AI's judgment, your options are narrow. In practice you dull the threshold, or you switch off automatic detection for that line — shutting down the system you deployed without ever learning why.

This dead end is not caused by a shortage of records. The historian, the maintenance management system, the daily logs — the records are all there. What is missing is **the connection: what the AI was looking at when it issued that judgment**.

## When a false detection appears, do you suspect the sensor or the AI?

Recording the underlying data alongside an AI decision — what it was looking at — is the same idea as a dashcam capturing what was happening at that moment. If you can go back and review the grounds, you can tell whether a false alarm came from a mistake in the AI's judgment or from a problem on the input side, such as a faulty sensor.

Once you can separate the two, the next move follows. If the sensor value was abnormal, you inspect the measurement chain. If the data was sound and the judgment was still wrong, you revisit the model. These are entirely different responses.

But when the record of the AI's judgment and the input data are kept separately, that separation takes work. You end up matching them by timestamp, and you usually cannot trace which version of the data was actually referenced, or whether a correction was applied after ingestion.

The same problem shows up outside false alarms.

When equipment fails or a quality defect occurs and you have to write the incident report, the persuasiveness of that report depends on whether each line of the timeline can carry a note on what the action was based on. It is the same when contracted operations or outsourced work raise the question of whose judgment it was. If the decision and what that decision was looking at are kept as a pair, you can show the path as it happened.

## What is missing from the records you already have

Existing records have two main weaknesses.

The first is that **whoever created the record can alter it later**. This is not a matter of suspecting bad faith. As long as the mechanism permits rewriting, there is no way other than your own word to show that nothing was rewritten. It is a structural problem.

The second is that **the decision and the input are stored separately**. Even with both in hand, which input was actually referenced at that moment becomes an inference from timestamps.

The way to close both gaps is to keep two things for every single AI decision:

- Fix the content of the decision in a form that cannot be changed afterwards
- Embed the input data that decision referenced into the record of the decision itself

This is not about replacing the records you have. Your current systems stay as they are; you add one separate record at the moment the AI issues a decision.

## Fix the AI's decision itself, not the data

Start with the question of which one to fix first.

Fixing only the input data lets you show that it matches the original data. That is necessary, but what you will be asked six months later is not whether the data matches — it is what the judgment was on that day.

So you fix the decision itself. You take "on this date, at this time, this judgment was made" as a single unit and compute a **hash value** from that content. A hash is a fixed-length value computed from the content; change even one character and the value changes. If someone rewrites the decision later, the hash no longer matches — so "nothing was rewritten" can be demonstrated by comparison rather than by assertion.

This is also the vocabulary that gets the conversation moving when you take it to your IT department.

## Embed "what it looked at" into the record of the decision

Fixing the decision alone still leaves the question of what informed it blank. So at the moment you create the record of the decision, you embed the input data that decision referenced along with it.

In practice, the moment a decision is finalized, you register the content of the decision and the inputs it referenced as a single entry. The default is automatic registration from the system running the AI, with no manual step in between. Attaching the inputs after the fact is no evidence of what was actually being looked at.

With this in place, anyone checking the record can trace from the decision back to the inputs.

### Two ways to tie "evidence" to an AI decision

There are two ways to embed inputs, depending on the nature of the data.

| Approach                     | Data it suits                                            | What it proves                                           |
| ---------------------------- | -------------------------------------------------------- | -------------------------------------------------------- |
| **Point to the source data** | Published data, such as a meteorological agency bulletin | Anyone can check the referenced information itself       |
| **Store a hash of the data** | Transient data streaming in from sensors                 | That it has not changed since the moment it was recorded |

"Pointing to the source data" suits published data — anything anyone can go and look up later.

"Storing a hash of the data" suits values like sensor readings — transient data that cannot be looked up after the fact.

In either form, being able to say in an audit or a report that "this data can be matched against the publishing source" and "this one can be confirmed as unchanged since the record was created" conveys reliability well enough.

## From a seismic bulletin to the completed inspection record

Using disaster response as an example, let us separate **the order in which records are created** from **the occasions on which those records are checked**.

### One record per event, as it happens

At the moment each event happens, you leave one record, in order.

**1. Record the news or bulletin:** the earthquake information received from the meteorological agency is stored exactly as it arrived.

**2. The AI makes a judgment:** the AI's decision — "a major earthquake bulletin was received, so inspection begins" — is recorded, linked to the record from step 1.

**3. Inspect on site:** the actual inspection results are stored, linked to the AI's decision from step 2.

These three records form a chain. Trace back from the inspection record and you get a single path: which bulletin triggered it, when, and on what grounds.

### Being able to check any time is what lets you keep using AI

The records are created once, but their contents can be checked as many times as you like afterwards. That matters because it means a false alarm no longer forces you to stop the AI in order to improve it.

The dead end described at the start — you cannot isolate the cause, so all you can do is dull the threshold or drop automatic detection — does not arise when the decision and its inputs are kept as a pair. You can fix exactly what needs fixing.

This chain of records helps in a range of everyday situations.

**When you are called out to the site:** you can immediately confirm why the AI acted. If it was a false alarm, you can tell without hesitation whether the cause lay in the data or in the AI's judgment.

**In monthly reporting:** you can hand over the means to verify the records along with the report. The recipient can check for themselves, which spares you the work of preparing supplementary documents.

**When investigating the cause of an incident:** you can explain not only the order of events but the grounds for each action taken.

**In audits and inspections:** even six months or a year later, you can trace back smoothly to the judgments and events of the time.

All four are covered by the same single trail. The scramble to gather evidence for every report and audit goes away, and **the scope you had been holding back — "we can't delegate that, we couldn't explain it" — can be widened with something to base the decision on.**

What can be confirmed here is the connection: that the judgment was made with reference to that bulletin. Whether the meteorological agency actually published it is confirmed by following the trail to the record and matching it against the publisher's own release.

The same structure applies directly to other operations. For contracted operations or outsourced maintenance, the work events of inspection, maintenance, and adjustment; for manufacturing processes, inspection records and measured values. Leave each of them in this form as it occurs, and the receiving side can check them the same way.

In public infrastructure, [MizuDAkO](https://lemma.frame00.com/blog/metawater-mizudako-resident-record-proof/) is a case where a proof layer was built into records of resident activity.

## The receiving side can confirm it alone

Kept in this form, the party receiving the records can confirm them without contacting the issuer. Three things can be confirmed:

- That the content of the AI's decision has not changed since it was recorded
- Which inputs that decision referenced
- That the referenced inputs are registered

What this means is that **the report no longer has to be believed**. In an audit, in an incident report, in sorting things out with the party that outsourced the work, the recipient can judge it on the spot. No supplementary submission of originals, no waiting for a phone inquiry.

What the record covers is the content and the path of the decision, not an evaluation of whether the decision was right. That belongs to the people on site. The same holds for the inputs: the record's job extends as far as letting you follow the reference and match it against the publishing source.

## Not "the AI is right," but "the path is on record"

The range of judgments delegated to AI will keep widening. What sites will be asked for is surely not that people believe "the AI is right." Which inputs it looked at, when, and what it decided. Keeping that path available for later confirmation is, we believe, the foundation that supports incident reporting and the sorting out of responsibility.

Applications in critical infrastructure and manufacturing are covered in [Manufacturing and critical infrastructure](https://lemma.frame00.com/solutions/use-cases/manufacturing/), and AI adoption more broadly in [AI adoption (cross-industry)](https://lemma.frame00.com/solutions/use-cases/ai/).
