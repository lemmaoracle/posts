---
# ============================================================
# LEMMA ORACLE / Use Case Detail Page Template (English)
# ============================================================
# Purpose: Starting point for new Use Case detail pages.
# This file is excluded from builds (_ prefix convention).
# Copy this file, fill in frontmatter, and write each section.
#
# Editorial rules are documented at the bottom.
# ============================================================

title: <Use Case Name>
slug: <kebab-case-slug>
pillar: <P1 | P2 | P3 | P4>
pillar_label: <Verifiable Origin | Verifiable AI | Agent Trust Chain | Regulatory Attribute Proof>
descriptor: <Subtitle. e.g. "Permanent Decision Record", "Pre-Execution Attestation">
thesis: <X ≠ Y binary opposition. Derived from Pillar tagline>
locale: en
tags: [<keyword1>, <keyword2>, <keyword3>]
---

# <Use Case Name> — <descriptor>

**<Restate thesis here. Same string as frontmatter>**

<2–3 sentence lead. Declare what this use case solves. Not a question — an assertion.>

---

## Problem

<!-- Role: Define what is broken in the status quo.
     Structure: 1 paragraph introducing the problem → 3–4 structural defects as bullets → "why now" in the closing paragraph.
     Voice: Assertion. "We think" / "might" / "could" are forbidden.
     Length: 200–350 words -->

<1 paragraph describing what current operations depend on.>

Specifically, the following structural defects exist:

- **<Defect 1 label>**：<1–2 lines>
- **<Defect 2 label>**：<1–2 lines>
- **<Defect 3 label>**：<1–2 lines>
- **<Defect 4 label>**：<1–2 lines> (optional)

<1 paragraph closing with regulatory/market "why now." Name concrete frameworks: EU AI Act, GDPR, CBAM, etc.>

<Final line: a strong assertion. Nominal sentence ending preferred.>

---

## Scenario

<!-- Role: Reproduce the problem in a concrete industry and persona — not abstract argument.
     Structure: Industry → Persona → Situation → Consequence without Lemma → Difference with Lemma.
     Pattern: Before/After two-part structure (especially effective for incident/attack use cases).
     Industry: Avoid industries already used in other Use Cases. See table below.
     Length: 300–500 words -->

<Introduction of industry, persona, and situation. Use proper nouns and figures for specificity. Cite real incidents with sources.>

### Before Lemma — <incident name or situation>

<Situation unfolding. Table format when appropriate:>

| Stage | What happened | Why it wasn't detected |
|---|---|---|
| <Stage 1> | <Event> | <Reason> |
| <Stage 2> | <Event> | <Reason> |

<1 paragraph on consequences without Lemma.>

### After Lemma — <one-line summary of the difference>

<Lemma behavior in 1–2 paragraphs + table:>

| Stage | Behavior with Lemma |
|---|---|
| <Stage 1> | <Change> |
| <Stage 2> | <Change> |

<1-line summary of the difference.>

---

## Architecture

<!-- Role: Show how Lemma's standard 4 cryptographic layers materialize in this Use Case.
     Structure: ENCRYPT / PROVE / DISCLOSE / PROVENANCE — 4 layers with fixed roles.
     Implementation-specific names (e.g. "〇〇 Gateway") go under each layer heading.
     Length: 350–550 words -->

Lemma's four cryptographic layers correspond to the <this Use Case's lifecycle name>.

**1. ENCRYPT — <one-line subtitle for this Use Case's role>**

<Description of original data sealing/encryption. AES-GCM, docHash, CID must appear.
State where originals remain and what is exposed externally.>

**2. PROVE — <one-line subtitle for this Use Case's role>**

<Description of ZK proof generation. State the proposition being proved concretely.
Stay at the layer level (commitment / selective disclosure / ZK circuit). Do not name specific
crypto primitives (Poseidon, BBS+, Groth16, BN254, BLS12-381, etc.) on externally-published pages.>

```
proof(<param_1>, <param_2>, <param_3>, ...)
```

<How the generated proof is transported and who verifies it.>

**3. DISCLOSE — <one-line subtitle for this Use Case's role>**

<Selective disclosure description. Who sees what — bullets:>

- **<Stakeholder 1>**：<disclosed attributes>
- **<Stakeholder 2>**：<disclosed attributes>
- **<Stakeholder 3>**：<disclosed attributes>

**4. PROVENANCE — <one-line subtitle for this Use Case's role>**

<On-chain anchoring / permanent record description. State explicitly: "data itself is never placed on-chain — only commitments."
What survives even if RAG, models, or operational infrastructure change.>

---

## Proven Facts

<!-- Role: Exhaustive list of facts Lemma cryptographically guarantees in this Use Case.
     Structure: Simple bullets only. No marketing language.
     Length: 6–9 items -->

Lemma cryptographically guarantees the following facts in <Use Case Name>:

- <Technical guarantee 1>
- <Technical guarantee 2>
- <Operational guarantee 1 (timestamp, actor, attribution, etc.)>
- <Operational guarantee 2>
- <Data non-disclosure / privacy guarantee>
- <Regulatory / third-party verifiability>
- <Guarantee unique to Lemma vs. alternatives>

---

## Related Use Cases

<!-- Role: Fixed 3-card format. Create reader navigation paths.
     Structure: H3 title + descriptor / bold thesis / 2–3 lines body / link.
     Selection: 1 card from same Pillar, 2 cards from different Pillars.
     Cross-Pillar selection criteria: (a) overlapping industry, (b) isomorphic structure, (c) agent-context linkage.
     Length: 3 × ~80 words -->

### <Related Use Case 1 Name> — <descriptor>
**<Related Use Case 1 thesis>**

<2–3 lines describing the relationship to this Use Case. "Same Pillar complement," "industry mirror," "operated as a pair" — state the nature of the relationship.>

[View use case →](/use-cases/<slug-1>/)

### <Related Use Case 2 Name> — <descriptor>
**<Related Use Case 2 thesis>**

<2–3 lines of relationship>

[View use case →](/use-cases/<slug-2>/)

### <Related Use Case 3 Name> — <descriptor>
**<Related Use Case 3 thesis>**

<2–3 lines of relationship>

[View use case →](/use-cases/<slug-3>/)

---

## Get Started

<!-- Role: CTA. Select secondary CTA based on Pillar.
     P1: Demo repo / P2: Whitepaper / P3: Trust402 waitlist / P4: Regulatory whitepaper -->

<1 sentence closing with the core question: "Ready to ...?">

Tell us about your use case. We'll respond within one business day.

[Talk to us →](https://tally.so/r/EkBqDX)　　[<Pillar-specific secondary CTA> →](<URL>)


---

# Editorial Rules (Required Reading)

## Overall Structure

7 sections in fixed order:
1. **Hero** (H1 + thesis + lead) — TOC hidden
2. **Problem** (H2)
3. **Scenario** (H2)
4. **Architecture** (H2)
5. **Proven Facts** (H2)
6. **Related Use Cases** (H2)
7. **Get Started** (H2)

No additional sections. "Overview," "Sales talking points," "Product roadmap implications" are forbidden.

## Voice & Tone

### Required
- **Assertion**: "We believe that..." / "It could be argued that..." / "might" are forbidden
- **Binary opposition**: thesis must use "X ≠ Y" format. Derived from Pillar tagline
- **Technical precision**: AES-GCM / docHash / CID / ZK proof / ZK circuit / commitment / selective disclosure as context requires. Specific crypto primitive names (Poseidon, BBS+, Groth16, etc.) are not used in externally-published pages
- **Declarative thesis line**: thesis line uses plain declarative ("Indexed ≠ trustworthy")
- **Active voice**: Prefer active constructions. Avoid passive overuse

### Forbidden
- "cutting-edge" / "innovative" / "game-changing"
- "safe and secure" (emotional, generic)
- "We believe that..."
- Decorative metaphors
- Passive voice overuse

## Pillar-Specific Secondary CTAs

| Pillar | Secondary CTA | Rationale |
|---|---|---|
| P1 Verifiable Origin | Demo repo | Technical verification path |
| P2 Verifiable AI | Whitepaper | Audit / compliance resource |
| P3 Agent Trust Chain | Trust402 waitlist + Whitepaper | Developer and decision-maker dual paths |
| P4 Regulatory Attribute Proof | Regulatory whitepaper | Regulatory department audience |

## Thesis Grammar

| Structure | Example |
|---|---|
| Passive ≠ true state | Indexed ≠ trustworthy |
| Verb ≠ verb | Audited ≠ explainable |
| Verb ≠ past participle | Cited ≠ verified |
| Noun ≠ noun | Access logs exist ≠ untampered |

Pillar tagline derivation:

| Pillar tagline | Derived thesis examples |
|---|---|
| Cryptographically valid ≠ semantically right (P1) | Recorded ≠ untampered |
| Finding bugs ≠ proving decisions (P2) | Audited ≠ explainable |
| Paying ≠ trusting (P3) | Authorized ≠ attested |
| Compliance promised ≠ compliance proven (P4) | Declared ≠ verified |

## Industry Selection Guide (Deduplication)

Industries in use across completed 8 Use Cases:

| Use Case | Industry |
|---|---|
| AI Audit Log Proof | Regional bank (credit decisions) |
| RAG Content Provenance | Pharmaceutical development (clinical trial protocols) |
| RAG Source Attestation | Tax accounting firm |
| KYC/AML Selective Disclosure | Manufacturing (bank account opening) |
| Supply Chain ESG | Automotive / steel (CBAM) |
| Supply Chain Component Provenance | Aerospace components |
| DeFi Bridge Verification | DeFi (cross-chain bridges) |
| Financial Data Exfiltration | Life insurance / agency secondment |

New Use Cases must select non-overlapping industries. Overlap requires explicit editorial justification.

## Architecture 4-Layer Standard Vocabulary

Each layer's role is fixed. Use Case context determines the subtitle:

| Layer | Role | Subtitle examples |
|---|---|---|
| ENCRYPT | Original sealing | "Issuance-time sealing", "Access content invulnerability", "Per-process record sealing" |
| PROVE | ZK proof generation | "ZK proof generation", "Origin authentication gateway", "Per-attribute ZK proof" |
| DISCLOSE | Selective disclosure | "Only necessary attributes disclosed", "Stakeholder-specific verifiability", "Context-dependent selective disclosure" |
| PROVENANCE | Permanent record | "Permanent record", "On-chain attestation anchoring", "Multi-tier permanent chain" |

## NG List

Never write:

- ❌ "Overview" section (H2)
- ❌ "Sales talking points" / "Sales positioning" / "Sales narrative" sections
- ❌ "Product roadmap implications" — internal document vocabulary
- ❌ Thesis unrelated to Pillar tagline
- ❌ Regulatory framework lists unrelated to the reader's industry
- ❌ Implementation-specific architecture without going through Lemma's 4 cryptographic layers (implementation names go under the layer)
- ❌ More than 3 or fewer than 3 related use case cards
- ❌ Deep-dive comparative analysis inside related use cases (split into separate article)

## Length Guidelines

| Page type | Target read time | Word count guide |
|---|---|---|
| Standard Use Case detail | 8–12 min | 2,000–3,500 words (body only) |
| Technical Deep Dive | 15–20 min | Separate file |
| Executive Summary | ≤5 min | Separate file |

When standard length is exceeded, split into separate files with cross-links.

## Cross-Link Strategy

"Related Use Cases" 3-card selection rules:

1. **1 card from same Pillar**: sister relation
2. **2 cards from different Pillars**: any of
   - Overlapping industry (e.g. financial AI decisions ↔ financial data exfiltration)
   - Isomorphic structure (e.g. DeFi bridge ↔ supply chain component provenance)
   - Agent-economy linkage (include 1 from P3)

This lets readers both deepen within a Pillar and discover across Pillars.

## Frontmatter Required Fields

```yaml
title: # Use Case name (no descriptor)
slug: # URL-safe, kebab-case
pillar: # P1 / P2 / P3 / P4
pillar_label: # Pillar official name (English)
descriptor: # Subtitle
thesis: # X ≠ Y format
locale: # ja / en
tags: # Search / SEO keyword array
```

Optional:

```yaml
date: # Publication date (YYYY-MM-DD)
author: # Author (internal governance)
status: # draft / review / published
related: # Related Use Case slug array (for auto-link generation)
```

---

# Operational Flow

When creating a new Use Case:

1. Copy this template and save as a new file (e.g. `new-use-case.md`)
2. Fill in frontmatter (especially thesis — derive from Pillar tagline)
3. Check Industry Selection Guide for duplicates
4. Write all 7 sections in order
5. Self-check against NG List
6. Select 3 related use case cards (same Pillar 1 + different Pillar 2)
7. Review: voice & tone / 4 crypto layer presence / thesis–Pillar alignment / industry uniqueness
8. Add reciprocal links in the Use Cases you referenced
9. Add new card (title + thesis + 3-line body) to the Pillar parent page
10. Publish
