---
title: "A real-world example: Kelp DAO $292M spoof"
---


In April 2026, Kelp DAO — a liquid restaking protocol on EigenLayer with ~$1.57B TVL at the time — was hit on the path that processes cross-chain messages via LayerZero OFT. The attacker compromised a 1-of-1 DVN, pushed a spoofed message into `lzReceive`, and drained 116,500 rsETH (~$292M) to a controlled address. DVN approval passed. Signatures were valid. The message was never actually emitted on the source chain.

Cascading losses followed. The drained rsETH was deposited as collateral into Aave V3, borrowing ~106,467 WETH and leaving Aave with ~$177M of bad debt. Ecosystem TVL bled out by more than $13B over the next two days. Kelp's `pauseAll` came 46 minutes later. After the attack, malware on the compromised RPC node deleted itself and its logs.

With Lemma's pre-execution attestation in place, the message would have been rejected at the boundary regardless of DVN approval: the origin proof simply does not verify. The OFT adapter never commits. The drained rsETH stays in escrow. The Aave collateralization never happens. No 46-minute manual pause needed. Even if RPC malware wipes logs, the on-chain anchored attestations survive — forensic evidence is preserved.

Per-incident forensics, DVN-topology-specific vulnerability analysis, and Lemma's verification logic design are shared in the protocol-specific kit we send after the consultation call.
