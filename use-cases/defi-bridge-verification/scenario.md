# Scenario: Kelp DAO / rsETH Bridge Exploit

## Before Lemma — How the Attack Unfolded

### Background

Kelp DAO is a liquid restaking protocol built on EigenLayer. Its flagship token, rsETH, represents ETH that has been deposited, staked, and restaked. rsETH was deployed across 20+ chains via a LayerZero OFT (Omnichain Fungible Token) adapter, with approximately $1.57B TVL at the time of the attack.

### Attack Timeline (April 18–19, 2026)

| Time (UTC) | Event | Detail |
|---|---|---|
| ~07:35 (T-10h) | Attacker preparation | 9 operational wallets funded via Tornado Cash (1 ETH mixing pool, ~0.0978 ETH each for gas) |
| 17:35 | Attack execution | Attacker calls `lzReceive` on LayerZero's EndpointV2 contract with a spoofed cross-chain message |
| 17:35 | DVN verification | 1-of-1 DVN verifier approves the fake message — only one signature required |
| 17:35 | Fund release | Kelp's OFT adapter releases 116,500 rsETH (~$292M) to attacker-controlled address |
| 17:35–18:20 | Collateral conversion | Attacker deposits rsETH as collateral on Aave V3, borrows ~106,467 WETH against it |
| 18:21 | Emergency pause | Kelp's emergency multisig executes `pauseAll` — 46 minutes after first malicious tx |
| 18:26, 18:28 | Follow-up attempts blocked | Same LayerZero packet aimed at another ~40,000 rsETH (~$100M) — pause holds |
| ~18:00–20:00 | Cascading freeze | Aave, SparkLend, Fluid freeze rsETH markets. Ethena pauses LayerZero OFT bridges as precaution |
| ~20:10 | First public acknowledgment | Kelp DAO posts on X — nearly 3 hours after the drain |
| Post-attack | Log deletion | Malware on compromised RPC nodes deletes itself and local log files |

### Why It Worked: Three Structural Failures

1. **1-of-1 DVN configuration** — Kelp DAO delegated the entire security of a bridge holding ~$300M in bridged collateral to a single signing key. No threshold, no redundancy, no fallback.

2. **RPC trust without verification** — The verifier trusted RPC nodes as the source of truth. The attacker compromised 2 of Kelp's RPC nodes, then DDoS'd the remaining honest nodes to force failover to attacker-controlled nodes. The verifier had no independent way to verify that the cross-chain message actually originated from the claimed source chain.

3. **No pre-execution origin check** — The OFT adapter received what appeared to be a fully attested cross-chain message and executed exactly as designed. It had no mechanism to ask: "Was this state mutation actually issued by the entity it claims to represent, on the chain it claims to come from, under conditions that were verifiable at issuance time?"

### Cascading Impact

| Impact | Scale |
|---|---|
| Direct loss (rsETH drained) | $292M |
| Aave V3 bad debt (unliquidatable rsETH collateral) | ~$177M |
| Ecosystem TVL exit (2 days post-hack) | $13B+ |
| LRT token depeg | rsETH lost peg across all 20+ chains |
| Protocol freezes | Aave V3/V4, SparkLend, Fluid, Ethena |
| ZRO token impact | -22% in 24h |

---

## After Lemma — How the Same Attack Is Prevented

### What Changes at Deployment

1. **Issuance-side origin proof** — When a cross-chain message is generated on the source chain, Lemma produces a ZK origin proof binding the action to its source: the issuing entity, the originating chain, and the conditions at issuance time.

2. **Proof travels with the message** — The origin proof is attached to the cross-chain message during transport. It does not rely on the sending side's continued participation or honesty.

3. **Pre-execution verification** — Before the receiving system (OFT adapter) commits, it runs Lemma's pre-execution attestation: verify the origin proof. The action commits only if the proof verifies. Otherwise, it is rejected at write time.

### Attack Timeline (With Lemma)

| Time (UTC) | Event | How Lemma Changes It |
|---|---|---|
| ~07:35 | Attacker preparation | Same |
| 17:35 | Attacker calls `lzReceive` with spoofed message | Same |
| 17:35 | DVN verification (still 1-of-1) | DVN approves (still compromised) |
| 17:35 | **Pre-execution attestation runs** | **Lemma verification rejects the fake message — origin proof does not verify. The message was not issued on the claimed source chain under verifiable conditions.** |
| 17:35 | **Fund release: DENIED** | **OFT adapter does not commit. 116,500 rsETH stays in escrow.** |
| 17:35–18:20 | No collateral conversion possible | Attack is stopped at the boundary. No rsETH released → no Aave deposits → no bad debt |
| 18:21+ | Optional: alert triggered | Lemma's failed verification can trigger an alert to the protocol's monitoring stack |

### What This Means Structurally

| Question | Without Lemma | With Lemma |
|---|---|---|
| Can a 1-of-1 DVN be exploited? | Yes — single point of failure | Still yes, but the fake message is rejected before commit regardless of DVN approval |
| Can compromised RPC nodes inject fake messages? | Yes — verifier trusts RPC | No — origin proof is verified independently of RPC trustworthiness |
| Can log deletion cover tracks? | Yes — malware deletes local logs | Partially — on-chain anchored attestations survive, but real-time alerting is the primary defense |
| What happens at the moment of commit? | Receiving system commits based on DVN signature alone | Receiving system commits only if origin proof + DVN signature both verify |
| How fast is the exploit stopped? | 46 minutes (manual emergency pause) | Instant (automated rejection at write time) |

### Key Insight: Defense in Depth

Lemma does not replace the DVN layer. It adds a second, independent verification layer:

```
Current:   DVN signature  ──────────────────▶  Commit
                                    ↑ single point of trust

With Lemma: DVN signature  ──┐
            Origin proof   ──┼──────────────▶  Commit
                                ↑ two independent
                                  verifications required
```

Even if the DVN is compromised (1-of-1, or N-of-M with dishonest majority), the origin proof must also verify. This is defense in depth — the two layers fail independently.
