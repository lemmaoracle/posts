# Pitch Deck: DeFi Bridge Verification — One-Slide

---

## $292M Stolen in 46 Minutes. Zero Origin Verification.

**The problem:** The Kelp DAO bridge was drained because the receiving system committed based on a single DVN signature. The signature was valid. The message was fake. "Cryptographically valid ≠ semantically right."

### The Structural Gap

```
Today's bridge:
  DVN signature ──────────────────▶  COMMIT
                      ↑ single point of trust

With Lemma:
  DVN signature  ──┐
  Origin proof   ──┼──────────────▶  COMMIT
                      ↑ two independent verifications
```

### Before Lemma

| Step | What happens | Gap |
|---|---|---|
| Attacker spoofs cross-chain message | Valid crypto, wrong semantics | No origin verification |
| 1-of-1 DVN approves | Single signature trusted | No independent check |
| OFT adapter releases $292M | Commits on DVN alone | No pre-execution attestation |
| 46 min later: emergency pause | Damage already done | Post-hoc only |

### After Lemma

| Step | What happens | Advantage |
|---|---|---|
| Attacker spoofs cross-chain message | Valid crypto, wrong semantics | Same |
| 1-of-1 DVN approves | Single signature trusted | Same |
| **Lemma origin proof: REJECT** | **Fake message fails independent verification** | **Exploit stopped at boundary** |
| **$292M stays in escrow** | **No commit, no cascade, no bad debt** | **Zero minutes** |

### The Layer

```
┌──────────────────────────────────────────────┐
│  Post-hoc    │  Forensics / Freezing / Recovery  │  ← Catches symptoms
├──────────────┼─────────────────────────────────┤
│  Pre-exec    │  Lemma Origin Verification       │  ← Prevents the commit
└──────────────┴─────────────────────────────────┘
```

**Post-hoc tools catch the symptom. Lemma prevents the commit.**

---

## Call to Action

> Your DVN says valid. Who proves it's real?

**Integration:** Lemma verifier as a pre-commit hook in your OFT adapter. 2-week PoC.

**Run the demo:** `git clone github.com/lemmaoracle/example-origin && pnpm install && pnpm demo`

Contact: [Lemma Oracle](https://lemma.frame00.com)
