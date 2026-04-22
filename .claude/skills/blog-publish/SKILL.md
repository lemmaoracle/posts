---
name: blog-publish
description: Publish a blog post to the lemmaoracle/posts repository. Use when given a manuscript draft to publish: generates frontmatter (slug, date, category, title, abstract, cover), handles cover image download and saving, cleans meta-commentary from the body, creates both English and Japanese versions, and commits to GitHub.
---

# Blog Publish Skill

## Overview

You are a blog publishing assistant for the `lemmaoracle/posts` GitHub repository. When given a draft manuscript, you automate the full publishing workflow: generating frontmatter, handling cover images, creating the file in the correct naming format, and producing a translated version in the other language.

---

## Repository Structure

```
lemmaoracle/posts/
├── {slug}.en.md # English post
├── {slug}.ja.md # Japanese post
├── assets/ # Cover images (11-char alphanumeric filenames)
├── README.md
└── package.json
```

---

## Frontmatter Schema

Every post requires a YAML frontmatter block at the top of the file:

```yaml
---
slug: "kebab-case-slug"
date: "YYYY.MM.DD"
category: "Category Name"
section: "Essays"
title: "Full title of the post"
abstract: "One-paragraph summary (2–5 sentences)."
cover: "assets/xxxxxxxxxxx.jpg"   # omit if no cover image
---
```

### Known `category` values (infer the best fit from content):

- `Business Strategy` — enterprise, B2B, API economy, compliance, KYC/AML
- `Tech Insight` — architecture, cryptographic design, RAG, ZK systems
- `Guides` — how-to, step-by-step developer content
- `Human Impact` — use cases affecting people: supply chain, travel, public services
- `Foundations` — specs, protocol definitions, core concepts
- `FAQ` — question-and-answer format

### `section` values:

- `Essays` — all narrative/analysis posts (default)
- `FAQ` — FAQ-format posts only

---

## Workflow

Execute the following steps **in order** whenever you receive a manuscript draft.

### Step 1 — Infer Frontmatter

Analyze the manuscript body and generate all required frontmatter fields:

| Field      | How to infer                                                                                       |
| ---------- | -------------------------------------------------------------------------------------------------- |
| `slug`     | kebab-case English phrase that captures the core topic. Must be URL-safe, lowercase, hyphens only. |
| `date`     | Use today's date in `YYYY.MM.DD` format unless the manuscript specifies a date.                    |
| `category` | Match to the known values above based on content theme.                                            |
| `section`  | `"Essays"` by default; `"FAQ"` only for FAQ-format posts.                                          |
| `title`    | Extract or refine from the manuscript's heading.                                                   |
| `abstract` | Write a concise 2–5 sentence summary of the post's key argument and value proposition.             |
| `cover`    | See Step 2. Omit this field if no image is available.                                              |

### Step 2 — Handle Cover Image

1. Search the manuscript for any referenced image (URL, embed, or filename hint).
2. If an image URL is found:
   - Download the image file.
   - Generate an **11-character alphanumeric filename** (mixed case, no symbols) — e.g., `rdFbtbFObPk`.
   - Preserve the original file extension (`.jpg`, `.png`, `.webp`, etc.).
   - Save the file to the `assets/` directory in the repository.
   - Set `cover: "assets/{generated-filename}.{ext}"` in the frontmatter.
3. If no image is referenced, omit the `cover` field entirely.

### Step 3 — Clean Manuscript Body

Remove any content that appears to be **meta-commentary** rather than the actual article body. This includes:

- Author notes or instructions to the editor (e.g., "Add a section about X here", "TODO:", "Note to self:")
- Publishing instructions embedded in the draft
- Placeholder text or bracketed annotations like `[insert example]`
- Internal review comments

Keep all content that is genuinely part of the article for readers.

### Step 4 — Determine Filename and Language

Detect the manuscript's language (English or Japanese):

- English manuscript → save as `{slug}.en.md`
- Japanese manuscript → save as `{slug}.ja.md`

### Step 5 — Create the Primary File

Write the cleaned manuscript with the generated frontmatter as `{slug}.[en|ja].md` in the repository root.

File format:

```
---
{frontmatter fields}
---

{cleaned manuscript body}
```

### Step 6 — Create the Translated Version

Translate the cleaned manuscript body into the other language:

- English original → create `{slug}.ja.md` with a Japanese translation
- Japanese original → create `{slug}.en.md` with an English translation

**Translation guidelines:**

- The translated file uses the **same frontmatter** as the original, except `title` and `abstract` are translated into the target language.
- `slug`, `date`, `category`, `section`, and `cover` remain identical in both files.
- Translate with natural, professional prose appropriate for a technical business audience.
- Preserve all Markdown formatting: headings, tables, bold, code blocks, lists.
- Do not add or remove content — translate faithfully.
- For Japanese → English: use clear, precise technical English.
- For English → Japanese: use polished business Japanese (です/ます, clear technical terms).

### Step 7 — Commit and Push

After creating both files (and saving any cover image to `assets/`), commit everything to the `main` branch:

```sh
git add .
git commit -m "publish: {slug}"
git push origin main
```

---

## Output Summary

After completing the workflow, report:

1. **Primary file** created: `{slug}.[en|ja].md`
2. **Translated file** created: `{slug}.[en|ja].md`
3. **Cover image** saved as: `assets/{filename}.{ext}` (or "No cover image")
4. **Frontmatter** used (display the full block for review)
