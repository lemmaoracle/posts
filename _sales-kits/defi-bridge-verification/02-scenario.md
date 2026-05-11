---
title: "Scenario"
---


Kelp DAO is a liquid restaking protocol built on EigenLayer. Its flagship token rsETH represents deposited, staked, and restaked ETH, deployed across 20+ chains via LayerZero OFT. At the time of the attack, TVL was approximately $1.57B.

## Before Lemma — How the Attack Unfolded

**April 18, 2026 17:35 UTC**

| Time | Event |
|---|---|
| T-10h | Attacker funded 9 operational wallets via Tornado Cash |
| 17:35 | Attacker called `lzReceive` with a spoofed cross-chain message |
| 17:35 | 1-of-1 DVN approved the fake message |
| 17:35 | Kelp's OFT adapter released 116,500 rsETH (~$292M) to attacker-controlled address |
| 17:35–18:20 | Attacker deposited rsETH as collateral in Aave V3, borrowed ~106,467 WETH |
| 18:21 | Kelp emergency multisig executed `pauseAll` (46 minutes after the first malicious transaction) |
| Post-attack | Malware on compromised RPC nodes deleted itself and the logs |

Cascading impact:

- Direct loss (rsETH drained): ~$292M
- Aave V3 bad debt (uncollateralizable rsETH): ~$177M
- Ecosystem TVL outflow (2 days post-attack): $13B+
- rsETH depegged across 20+ chains
- ZRO token -22% (24h)

## After Lemma — The Same Attack Stops at the Boundary

| Time | Behavior with Lemma |
|---|---|
| 17:35 | Attacker calls `lzReceive` (same as above) |
| 17:35 | DVN approval still passes the fake message (DVN is not replaced) |
| 17:35 | Lemma pre-execution attestation activates. Origin proof verification fails — the message was not issued under verifiable conditions on the claimed source chain |
| 17:35 | OFT adapter does not commit. 116,500 rsETH remains in escrow |
| 17:35 onward | Conversion to Aave V3 collateral is impossible. No downstream cascading losses |
| Structural fact | Stops at the boundary. No manual emergency halt needed |

## What This Means Structurally

| Question | Without Lemma | With Lemma |
|---|---|---|
| Can a 1-of-1 DVN be exploited? | Yes (single point of failure) | Regardless of DVN approval, commit is rejected if origin proof cannot be verified |
| Can compromised RPCs inject fake messages? | Yes | No. Origin proofs are verified independently of RPC trustworthiness |
| Can log deletion erase traces? | Yes | On-chain anchored attestations survive |
| Exploit stop speed | 46 minutes (manual) | Instant (automatic rejection at write time) |

Lemma does not replace the DVN layer. It **adds a second independent verification layer** as defense in depth. Even if the DVN is compromised, the commit cannot succeed without a verified origin proof.
