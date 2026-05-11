# Financial Data Exfiltration — sales-kit archive

Material moved off the public use-case page (`use-cases/financial-data-exfiltration/`) on 2026-05-12 as part of the progressive-disclosure refactor.

Files preserved here are the source for the post-inquiry industry kit shared after the first 30-minute consultation call:

- `01-problem.{ja,}.md` — full problem statement with the four trust-boundary failure modes.
- `02-scenario.{ja,}.md` — full MetLife Japan 2026 timeline, before/after tables, and metric comparison.
- `03-architecture.{ja,}.md` — four-layer ENCRYPT / PROVE / DISCLOSE / PROVENANCE explanation.
- `04-proof.{ja,}.md` — full eight-item list of cryptographically guaranteed facts.

These files are **outside** the `use-cases/` directory and are not picked up by the website build (`packages/web/src/data/useCases.ts` only scans `use-cases/<slug>/`).
