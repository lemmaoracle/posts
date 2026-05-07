# Pitch Deck: Financial Data Exfiltration — One-Slide

---

## 2,476 Records Stolen. Zero Proof.

**The problem:** When a seconded employee walks out with customer data, you can't prove what happened. Your DLP flagged the anomaly. Your logs say they accessed the records. But your logs are mutable, siloed, and disputable.

**The regulator doesn't want your spreadsheet. They want proof.**

### Before Lemma

| Step | What happens | Gap |
|---|---|---|
| Employee seconded | Credentials at both orgs | No trust boundary enforcement |
| Data accessed | Logs stored in modifiable DB | Logs can be altered |
| Exfiltration detected | DLP flags anomaly | "Something happened" — not *what* |
| Audit requested | 2 weeks of manual log forensics | No cross-org verifiability |

### After Lemma

| Step | What happens | Advantage |
|---|---|---|
| Employee seconded | Lemma issues time-bound access credentials | Trust boundary is explicit |
| Data accessed | ZK attestation generated per access | Cryptographic proof, privacy-preserving |
| Exfiltration detected | DLP + Lemma = anomaly + proof | Undeniable evidence in minutes |
| Audit requested | Export verifiable attestation set | Hours, not weeks |

### The Stack: Complementary, Not Competitive

```
┌──────────────────────────────────┐
│  Detection  │  DLP / SIEM / CASB │  ← "Something happened"
├─────────────┼────────────────────┤
│  Proof      │  Lemma Oracle      │  ← "Here's the cryptographic proof"
└─────────────┴────────────────────────┘
```

**Your existing tools detect. Lemma proves.**

---

## Call to Action

> The FSA just proved they'll enforce. Can you prove your audit trail?

**PoC timeline:** 2 weeks to deploy Lemma Attestation Gateway alongside your existing DLP.

Contact: [Lemma Oracle]
