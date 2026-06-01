---
title: "Worked example: sanctions and credit screening across a trading group"
---


The HQ of mid-sized trading group A runs sanctions and credit screening on a prospective counterparty. The basis: transaction history, credit data, third-party database queries. The same counterparty then gets re-screened independently by regional subsidiary B, overseas subsidiary C, and partner bank D handling settlement — that's the live operation.

Two problems. First, every group company and partner bank re-checks the same party. Second, even when HQ A tries to share the basis with the subsidiaries and the bank, leakage of the underlying data moves defamation and interference risk; withholding it pushes the receivers back to re-screening because they can't trust the decision alone.

With Lemma in place, the ZK proofs HQ A emits at decision time — "not on the sanctions list", "credit band acceptable", "not resident in a restricted jurisdiction" — are what go to subsidiaries B / C and bank D. The basis (transaction history, scores, the list that was queried, the query history) stays under HQ A; the receivers verify "does it meet the bar" independently without ever seeing it. When an audit or regulatory report lands later, the provenance anchor still shows when, by whom, and tamper-free the decision was issued.

The non-membership circuit selection, list-revocation and decision-withdrawal handling, and connector patterns for existing credit / sanctions data providers ride on the industry kit that follows the Discovery call.
