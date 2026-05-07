---
title: "Proven Facts"
---

# Proven Facts

When a seller accepts a payment from a delegated agent with Lemma, the following facts are cryptographically proven — not claimed:

- **Issuing principal (org / department)** — Who delegated this authority. Not "the agent says it's from Acme Corp" — a ZK proof attests that Acme Corp's signing key authorized this delegation.
- **Spend ceiling and scope** — The exact budget and category constraints. "Up to $10K/month, SaaS only" is encoded in the attestation, not in a prompt the agent can ignore.
- **Time-bounded validity** — When the delegation expires. The attestation carries a validity window; expired attestations fail verification automatically.
- **Revocation status** — Whether the delegation has been revoked. Sellers check on-chain revocation status in real-time. A revoked attestation is rejected before the transaction completes.
