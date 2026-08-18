---
slug: "ai-decision-record-with-inputs"
date: "2026.08.18"
category: "Solutions"
audience: business
industries: [manufacturing, public-sector, ai]
coverPhoto: /assets/covers/ai-decision-record-with-inputs.jpg
section: "Essays"
title: "Keep the AI decision together with the data it was looking at — records that still hold up six months later"
abstract: "After AI is introduced into equipment anomaly detection, false alarms often follow. If you cannot separate a problem in the input data from a problem in the AI's judgment, there is little left to do beyond dulling the threshold or switching the detection off. Keep the AI's decision and the data it referenced as a single record that nobody can alter afterwards, and you can show the path itself — in false-alarm triage and in incident reports."
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

After AI is introduced into equipment anomaly detection, false alarms often follow. If you cannot tell whether the cause sits in the input data or in the AI's judgment, there is little left to do beyond dulling the threshold or switching the automatic detection off. Keep the AI's decision and the data it referenced as a single record that nobody can alter afterwards, and both false-alarm triage and incident reporting can proceed by showing the path itself.

Dull the threshold, or switch off automatic detection for that line. Being pushed into that choice when false alarms keep coming is not caused by a shortage of records. The process historian, the maintenance management system, the daily logs — the records are all there. What is missing is **the connection: what the AI was looking at when it issued that judgment**.

## When a false alarm appears, do you suspect the sensor or the AI?

Recording the underlying data alongside an AI decision — what it was looking at — is the same idea as a dashcam: it captures what was happening at that moment. If you can go back and review the grounds, you can tell whether a false alarm came from a mistake in the AI's judgment or from a problem on the input side, such as a faulty sensor.

Once you can separate the two, the next move follows. If the sensor value was abnormal, you inspect the measurement chain. If the data was sound and the judgment was still wrong, you revisit the model. These are entirely different responses.

But when the record of the AI's judgment and the input data are kept separately, that separation takes work. You end up matching them by timestamp, and you usually cannot trace which version of the data was actually referenced, or whether a correction was applied after ingestion.

The same problem shows up outside false alarms.

When equipment fails or a quality defect occurs and you have to write the incident report, the persuasiveness of that report depends on whether each line of the timeline can carry a note on what the action was based on. It is the same when contracted operations or outsourced work raise the question of whose judgment it was. If the decision and what that decision was looking at are kept as a pair, you can show the path as it happened.

## What is missing from the records you already have

Existing records have two main weaknesses.

The first is that **whoever created the record can alter it later**. This is not a matter of suspecting bad faith. As long as the system allows rewriting, you have nothing but your own word to show that nothing was rewritten. It is a structural problem.

The second is that **the decision and the input are stored separately**. Even with both in hand, which input was actually referenced at that moment becomes an inference from timestamps.

The way to close both gaps is to keep two things for every single AI decision:

- Lock the content of the decision into a form that cannot be changed afterwards
- Embed the input data that decision referenced into the record of the decision itself

This is not about replacing the records you have. Your current systems stay as they are; you add one separate record at the moment the AI issues a decision.

## Lock the AI's decision itself, not the data

Start with the question of which one to fix first.

Locking only the input data lets you show that it matches the original data. That is necessary, but what you will be asked six months later is not whether the data matches — it is what the judgment was on that day.

So you lock the decision itself. You take “on this date, at this time, this judgment was made” as a single unit and compute a **hash value** from that content. A hash is a fixed-length value computed from the content; change even one character and the value changes. If someone rewrites the decision later, the hash no longer matches — so “nothing was rewritten” can be demonstrated by comparison rather than by assertion.

But a hash kept in the same place as the decision record buys you nothing: whoever created it can redo both. The hash is registered somewhere its creator cannot rewrite — in Lemma’s case, a registry. Only then can the receiving side compare the registered value against the record in hand, without asking the issuer for anything.

Hashes and registries are also language your IT department will already recognise, which makes them a workable place to start the conversation.

## Embed “what it looked at” into the record of the decision

Locking the decision alone still leaves the question of what informed it unanswered. So at the moment you create the record of the decision, you embed the input data that decision referenced along with it.

In practice, the moment a decision is finalized, you register the content of the decision and the inputs it referenced as a single entry. The default is automatic registration from the system running the AI, with no manual step in between. Attaching the inputs after the fact is no evidence of what was actually being looked at.

With this in place, anyone checking the record can trace from the decision back to the inputs.

### Two ways to tie “evidence” to an AI decision

There are two ways to embed an input. The data a site handles varies, so you use both side by side.

| How the input is embedded               | What can be confirmed                                                              |
| --------------------------------------- | ---------------------------------------------------------------------------------- |
| **Point to an already-registered item** | The referenced input can be matched, content and all, against the published source |
| **Store the input’s hash alongside**    | The referenced input has not changed since the record was created                  |

The first form requires the input to be registered beforehand. For external data pulled in on a schedule — a Japan Meteorological Agency (JMA) bulletin, say — the practical route is to register it as part of the ingest job. Data that arises on the spot, like raw sensor readings, is hard to register in advance, so it takes the second form. There you keep your own copy of the value in your records; if the copy and the hash agree, the value has not changed since the moment it was recorded.

Which form was used is visible in the record itself. Being able to say in a report or an audit that “this input can be matched against the published value” and “this one goes as far as showing it was not altered” carries further than making the record sound stronger than it is.

<figure class="figure--diagram">
  <img src="/assets/figures/judgment-input-chain-fig1.en.svg" alt="Input records — an official bulletin and a sensor reading — embedded into the decision record at record time, with the checking side tracing back from the decision to the inputs" />
  <figcaption>Figure 1 — How input records relate to the decision record</figcaption>
</figure>

## From an earthquake bulletin to the completed inspection record

Using disaster response as an example, let us separate **the order in which records are created** from **the occasions on which those records are checked**.

### One record per event, as it happens

Rather than assembling them afterwards, you register each one as it happens.

**1. Ingest the official bulletin.** The JMA epicentre and seismic-intensity report is registered exactly as it stood when it was retrieved — one extra step inside the scheduled ingest job.

**2. The AI issues a decision.** “An intensity of upper 5 was reported, so emergency inspection of the affected area is triggered” is recorded, pointing at the registration from step 1.

**3. The field carries out the inspection.** The record of the inspection actually performed is registered, pointing at the decision from step 2.

These three records form a chain: each record carries the hash of the one before it, so swapping out a single link leaves the records after it out of step. Trace back from the inspection record and you get a single path: which bulletin triggered it, when, and on what grounds.

### A second case: a false alarm with a sensor reading as the input

The anomaly detection from the opening works the same way. If the AI flags a vibration reading as abnormal, then at that moment you register the content of the decision and the hash of the sensor reading it referenced as one record, and keep your own copy of the value in your records.

When the alarm later turns out to be false, that single record is what you open. If the copy and the hash agree, the AI was indeed looking at that value. An abnormal value sends you to the measurement chain; a sound one sends you to the judgment. The triage from the opening comes down to opening one record.

### Checking as often as you like is what keeps the AI running

Registration happens once per event; checking can happen any number of times. That is what pays off, because **a false alarm no longer forces you to stop the system to fix it**.

The dead end described at the start — you cannot isolate the cause, so all you can do is dull the threshold or drop automatic detection — does not arise when the decision and its inputs are kept as a pair. You can repair exactly what needs repairing.

The same path pays off in each of the following situations.

**Right after a call-out.** The field can confirm on the spot what the AI was looking at when it triggered. If a false alarm is suspected, tracing back to the input shows whether to go at the measurement chain or at the judgment.

**In monthly reporting.** You can attach a way to check to the report you send the contracting party. They can judge it themselves, without asking you to submit originals.

**After an incident or a defect.** Every line of the timeline in the incident report can carry its grounds, so “why things happened in that order” is written with the records behind it.

**In an audit.** Six months or a year later, the same path runs from the inspection record back to the official bulletin.

All four are covered by the same single trail. The scramble to gather evidence for every report and audit goes away, and **you can widen the scope you had been holding back — “we can’t explain it, so we can’t delegate it” — and now you have something to base that call on.**

What can be confirmed here is the connection: that the judgment was made with reference to that bulletin. Whether the JMA actually published it is confirmed by following the trail to the record and matching it against the publisher’s own release.

The same structure applies directly to other operations. For contract operations and outsourced maintenance, that means the work events — inspection, maintenance, adjustment. For manufacturing, it means inspection records and measured values. Leave each of them in this form as it occurs, and the receiving side can check them the same way.

Layering proof onto the input records is already running in public infrastructure: [MizuDAkO](https://lemma.frame00.com/blog/metawater-mizudako-resident-record-proof/) built a proof layer into records of resident activity.

## The receiving side can confirm it on its own

Kept in this form, the party receiving the records can confirm them without contacting the issuer. Three things can be confirmed:

- That the content of the AI's decision has not changed since it was recorded
- Which inputs that decision referenced
- That the referenced inputs are registered

What this means is that **you no longer need anyone to take the report on faith**. In an audit, in an incident report, in sorting things out with the party that outsourced the work, the recipient can judge on the spot whether the records line up. No submitting originals on the side, no waiting on a phone call to confirm.

What the record covers is the content and the path of the decision, not an evaluation of whether the decision was right. That belongs to the people on site. The same holds for the inputs: the record's job extends as far as letting you follow the reference and match it against the publishing source.

## Not “the AI is right,” but “the path is on record”

The range of judgments delegated to AI will keep widening. What the field will be asked for is not that people believe the AI is right. What matters is which inputs it looked at, when, and what it decided — and keeping that path available for later confirmation is, we believe, the foundation under incident reporting and the give-and-take with whoever contracted the work.

Applications in critical infrastructure and manufacturing are covered in [Manufacturing and critical infrastructure](https://lemma.frame00.com/solutions/use-cases/manufacturing/), and AI adoption more broadly in [AI adoption (cross-industry)](https://lemma.frame00.com/solutions/use-cases/ai/).
