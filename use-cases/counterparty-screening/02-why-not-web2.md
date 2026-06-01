---
title: "Why not Web2 / signatures / a database"
---

This is a job where **three properties are required at once**: prove without revealing the contents; let a third party verify independently; make it tamper-proof. Every conventional tool is missing one.

- **Spreadsheet / DB**: an admin can rewrite it, and sharing it leaks the contents.
- **Signed PDF**: the signer is known, but the reasons and scores are exposed.
- **Encryption**: hides, but can't prove "this decision is correct" without decrypting.

**Only a ZK proof satisfies all three.** Where one property is dispensable, Web2 is enough and Lemma isn't needed — we say so plainly.
