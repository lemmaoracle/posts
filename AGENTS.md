# posts — editing guide

Markdown blog articles for the Lemma site. The Lemma web app
(`lemmaoracle/lemma` → `packages/web`) fetches this repo **at build time**
via the GitHub API. A change is live only after the PR is merged **and**
the Lemma site is redeployed.

## Repository layout

Articles are **flat in the repository root** — no per-language folders.

```
<slug>.en.md      English article
<slug>.ja.md      Japanese article
assets/           cover / inline images
use-cases/        separate data source — not blog articles
_sales-kits/      not blog articles
```

- Filename = `<slug>.<locale>.md`, where `<locale>` is `en` or `ja`.
  A suffix-less `<slug>.md` is treated as `en` (legacy; always use an
  explicit `.en.md` / `.ja.md` for new files).
- `<slug>` is the URL slug: `lemma.frame00.com/blog/<slug>/` (EN) and
  `/ja/blog/<slug>/` (JA).
- Pair EN and JA under the **same slug** so the language switch works.

## Frontmatter

YAML frontmatter fenced by `---`. Parsed by `packages/web/src/data/blog.ts`.

| Field           | Required | Notes |
|-----------------|----------|-------|
| `title`         | **Yes**  | An article with no title is dropped at build. Do **not** put an `# H1` in the body — the title lives only here. |
| `slug`          | Strongly recommended | Falls back to the filename. Must match across the EN/JA pair. |
| `date`          | Recommended | `"YYYY.MM.DD"` (dot-separated). Drives ordering and the SchemaOrg date. |
| `category`      | Recommended | e.g. `"Business Strategy"`. Drives category colour and default section. |
| `section`       | Recommended | e.g. `"Essays"`. Defaults from `category`, ultimately `"Essays"`. |
| `abstract`      | Recommended | Meta description / card copy. Directly affects SEO. |
| `cover`         | Optional | OG image. Repo-relative path — see Assets. |
| `tags`          | Optional | List of strings. |
| `categoryColor` | Optional | Auto-derived from `category` if omitted. |
| `relatedLinks`  | Optional | List of `{ label, href }`. |

Example:

```yaml
---
slug: "verifiable-ai-financial-agents-2026"
date: "2026.05.07"
category: "Business Strategy"
section: "Essays"
title: "AI agents in financial operations: the case for the judgment-trail layer"
cover: "assets/kF7nQ3rXa2P.jpg"
abstract: "On April 24, 2026, Japan's FSA convened an emergency session..."
tags:
  - verifiable-ai
  - financial-services
  - ai-agents
---
```

Body starts right after the closing `---`. Headings are `##` / `###`
(no `# H1`). Code blocks highlight only known languages (bash, ts,
json, solidity, yaml, …). For Japanese terminology follow existing
articles (e.g. ゼロ知識証明, not 零知識証明).

## Assets (cover / OG images)

- Put image files in `assets/` (the existing naming convention is a
  short random alphanumeric stem, e.g. `assets/kF7nQ3rXa2P.jpg`).
- Reference them with a **repo-relative path**: `cover: "assets/<file>"`.
  At build time this resolves to
  `https://raw.githubusercontent.com/lemmaoracle/posts/main/assets/<file>`.
- A full `https://…` URL is also accepted and used as-is.

## Branching

`main` is not branch-protected on GitHub, but the team convention is:

- **Always work on a feature branch + open a PR.** Do not push to `main`
  directly.
- **Do not merge** — a human merges after review.
- Branch name convention: `content/<topic>` for articles,
  `docs/<topic>` for repo docs.

## Worked example

Updating an already-published article (EN + JP), the standard flow:

```bash
cd /Users/mayumi/github/posts

# 1. branch from the latest main
git fetch origin main
git checkout -B content/<topic> origin/main

# 2. edit the article .md files
#    - change frontmatter title / abstract as needed
#    - keep slug / date / cover / category / section unchanged when
#      updating an existing post (preserves the URL and its SEO standing)

# 3. commit
git add <slug>.en.md <slug>.ja.md
git commit -m "content: <summary>"

# 4. push
git push -u origin content/<topic>

# 5. open a PR (do not merge)
gh pr create --base main --head content/<topic> --title "content: ..." --body "..."
```

Golden rule for edits to an existing article: **never change the
`slug`** — it is the URL and carries the SEO history. A title change
goes in the frontmatter `title` field only.
