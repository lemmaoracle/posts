# Lemma Oracle — Posts

Blog posts, essays, guides, and media assets for
[Lemma Oracle](https://lemma.frame00.com).

Markdown 管理専用リポジトリ。lemma のブログ（`lemma/packages/web`）が
ビルド時に GitHub API でこのリポジトリの `.md` を取得して表示する。

## Read the posts

- 🌐 **Website**: https://lemma.frame00.com
- ✍️ **Blog**: https://lemma.frame00.com/blog
- ❓ **FAQ**: https://lemma.frame00.com/blog/faq

## About Lemma

Lemma is a cryptographically verified truth layer for agent AI —
delivering proven facts via zero-knowledge proofs, selective disclosure,
and on-chain provenance.

Learn more at [lemma.frame00.com](https://lemma.frame00.com) or see the
main repository at [lemmaoracle/lemma](https://github.com/lemmaoracle/lemma).

# フォーマット

Markdown 管理専用リポジトリ。lemma のブログ（[lemma/packages/web](https://github.com/lemmaoracle/lemma/tree/main/packages/web)）がビルド時に GitHub
API でこのリポジトリの `.md` を取得して表示する。

## フォーマット

Markdown は Prettier で統一する。`pnpm install` のあと、`pnpm format`
でフォーマット、`pnpm format:check` でチェックのみ。

## 記事の frontmatter

各 `.md` は YAML frontmatter を持つ。lemma の `blog.ts`
が参照する項目は次のとおり。

- **必須**: `title`
- **任意**: `slug`（省略時はファイル名から `.md`
  を除いた値）、`date`、`category`、`section`、`abstract`、`categoryColor`

例:

```yaml
---
title: "Example Post"
date: "2025-02-24"
category: "Research"
section: "Essays"
abstract: "Short summary for listings."
---
```

本文は frontmatter の直後から Markdown で記述する。
