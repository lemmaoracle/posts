---
slug: "stablecoin-payment-reconciliation"
date: "2026.08.21"
category: "Solutions"
audience: business
industries: [finance, supply-chain]
coverPhoto: /assets/covers/stablecoin-payment-reconciliation.jpg
section: "Essays"
title: "Reconcile without opening the books. A smoother month-end for stablecoin payments"
abstract: "With yen-denominated stablecoins, the transfer itself already settles in minutes. For a business that books hundreds or thousands of payments a month, scanning on-chain events is not enough. The totals do not agree, and you cannot tell which transaction is wrong — because the breakdown never travelled with the transfer. Lemma's reconciliation confirms a match without either side disclosing its data. The on-chain settlement record, the payer's ledger and the payee's ledger: it checks that all three point at the same payment, without showing the contents."
tags:
  - stablecoin
  - reconciliation
  - provenance
  - accounts-payable
relatedLinks:
  - label: "Finance and FinTech use cases"
    href: "https://lemma.frame00.com/solutions/use-cases/finance/"
  - label: "Put a proof on the rate — settlements you can still verify afterwards"
    href: "https://lemma.frame00.com/blog/verifiable-fx-rates-for-payments/"
  - label: "Adding a third layer to x402 — the options open to stablecoin operators"
    href: "https://lemma.frame00.com/blog/ppsi-stablecoin-aml-kyc-third-layer/"
  - label: "Proving resident activity records are genuine — without exposing personal data (MizuDAkO)"
    href: "https://lemma.frame00.com/blog/metawater-mizudako-resident-record-proof/"
---

Payments in yen-denominated stablecoins have started moving in real operations — subcontractor fees, transfers to overseas subsidiaries. The transfer itself already completes in minutes. What the on-chain record holds is how much moved, and no more: **which invoice, and which line item on it** does not travel with the transfer. For a business that books hundreds or thousands of payments a month, walking the on-chain events still will not tell you which transaction is off when the totals do not agree. Because that breakdown has to reach the other side by a separate route, the next thing that can get shorter is the month-end close. The time the team spends is not the matching work itself so much as the wait for the counterparty to write back.

## At month-end, the longest part is the waiting

Yen-denominated stablecoins now have issuers across all three regulatory types — funds-transfer, trust, and foreign-issued — and they are starting to be used for real payments such as subcontractor fees and transfers to overseas subsidiaries. The transfer itself completes in minutes. The question that comes up when adoption is on the table is whether the month-end close gets shorter in the same way.

With a bank transfer, matching your own accounts payable against the bank statement is usually enough. Pay in stablecoins and a third record joins the comparison: the counterparty's accounts receivable (the reason is in the next section). For a business that books hundreds or thousands of payments a month, scanning on-chain events is not enough. The totals do not agree, and you cannot tell which transaction is wrong — that is what pushes the close out by days. When something does not line up, you ask the counterparty, they go through their own books, and you wait for the answer — and that takes days.

Say you make 1,000 payments a month, 30 of them draw a query, and each query and confirmation takes 18 minutes. That is 9 hours of work. But what actually pushes the closing date is not those 9 hours; it is the several days of waiting that each individual item generates. Paying in stablecoins shortens the transfer itself to minutes, and leaves that waiting exactly where it was.

## Confirming automatically that the on-chain settlement and both companies' books point at the same payment

### How a bank transfer differs from a stablecoin payment

With a bank transfer, the bank records the same transaction on both sides. The bank statement is a shared reference, so whether the two sides are pointing at the same payment is something the bank underwrites.

Pay in stablecoins and a transaction is left on chain. The funds can be confirmed in minutes — faster than a bank, and along a transparent route.

The on-chain record, though, states only the fact that X tokens moved from A to B. Which invoice this payment settles is not part of the record. The confirmation the bank used to broker — "these are the same transaction" — is what a stablecoin lacks.

Once subcontractors number in the hundreds, several invoices to the same counterparty are often paid as one combined transfer, and even if you know their address in advance, the breakdown is not something you can read off it. What the address tells you is who it came from, and no more.

### Checking that all three records point at the same payment

A payment leaves a record in three places.

- The on-chain settlement record — how much moved from A to B
- The payer's ledger — invoice number, amount, payment date
- The payee's ledger — amount received, date recognized

Lemma confirms automatically that all three point at the same payment.

<figure class="figure--diagram">
  <img src="/assets/figures/stablecoin-reconciliation-fig1.en.svg" alt="On the left, the settlement record and the payer's ledger; in the centre, the reconciliation registry; on the right, the payee's ledger. Each payment is recorded at the time it is made, then matched by transaction hash or quote number. The contents stay private and are never registered. A match clears automatically; a mismatch identifies the field." />
  <figcaption>Figure 1 — Confirming automatically that the on-chain settlement and both companies' books point at the same payment</figcaption>
</figure>

Payee, amount, invoice number and payment date are recorded on each side at the moment the payment happens. The contents themselves are not handed to the other side, and they do not go onto the registry. Matching uses keys both sides already hold — a transaction hash, a quote number — to line up the same payment. You learn whether they agree without ever touching what is inside the counterparty's books.

> What Lemma confirms is not that a transfer happened — the chain already proves that — but that both companies' books point at the same payment. The latter is off-chain information, so it has to be bound cryptographically and matched without disclosure. This is not a plain comparison of values; it verifies that each side's record is bound to that payment.

They are who to start the conversation with internally — IT — and the call about where in the current accounting system to add one step is theirs to make.

### A match clears on the spot

A payment that matches needs no review by anyone.

Where there is a discrepancy, you also learn which field it is in. The amount matches and only the invoice number differs — the invoice number is the only thing to look at. One query to the counterparty is enough.

## From the moment a payment is approved to the moment the item clears

Rather than assembling everything afterwards, you register one item at a time, as each event happens.

**1. The payment is approved.** Payee, amount, invoice number. Record the contents as approved, without handing them over. One step added to the end of the existing approval flow is all it takes.

**2. The transfer goes out.** Link the on-chain transaction to step 1. Transfers between operators also trigger travel-rule notifications, and those can ride the same route.

**3. The counterparty recognizes it.** When the receiving side enters it into their own books, they record the same fields.

**4. Reconcile.** Match 1 and 3 by transaction hash, quote number, or similar keys, and if they match, the item clears.

If 30 of 1,000 items differ, those 30 are the only ones anyone looks at, and the remaining 970 clear untouched. Registration happens once per event, while there is no limit on how many times you reconcile — you can check again, later, as often as you like.

When a counterparty writes to say the amount received does not agree with their statement, instead of you digging out a copy and resending it, they can reconcile their own record on the spot. With 2,000 counterparties, the procedure is the same as with one.

In an audit, too, the work of gathering evidence and putting it in order gets lighter when the route is already there. It does not go as far as saying that a passing reconciliation removes the need to inspect originals altogether; how it fits into audit procedures is something to agree in advance.

## What a match confirms stops short of correctness

Reconciliation confirms three things.

- Whether both records point at the same contents (without disclosing them)
- Which record a given record is linked to (approval → settlement → recognition)
- Whether the contents have changed since they were registered

Whether the amount was what the contract said, and whether the acceptance decision was sound, sit outside that. Those remain the domain of people, and if both sides recorded the same error, the reconciliation itself passes as a match.

The scope is narrow, and being narrow is what makes it easy to hand to a machine — you can draw the line and say "this far a machine confirms; from here it is a matter of judgment." In reporting and in audits, that line coming across is more likely to land than making the thing look stronger than it is.

Regulation asks for records to be created and retained. Layer reconciliation on top of that, and you can design the waiting out.

## The procedure does not change across issuer types

Yen-denominated stablecoins now have issuers across all three types: funds-transfer, trust, and foreign-issued. In July 2026, AZ-COM MARUWA Holdings announced that it would adopt JPYC for subcontractor payments to roughly 2,300 counterparties, including partner firms and individual truck drivers — positioned as the first case of a large Japanese company using a regulated yen-denominated digital payment instrument for everyday business-to-business settlement. The jointly issued trust-type coin under discussion at MUFG Bank, Mizuho Bank and SMBC is targeting live transactions during fiscal 2026, with cross-border settlement between Japanese and overseas offices among the anticipated uses.

Yano Research Institute expects outstanding balances to reach ¥14.7 trillion by fiscal 2030. Actual B2B and B2C usage is projected to stay under 10% of the total even then, so what is moving right now is one part of the payments workload.

Companies transacting across more than one type, though, is about to become ordinary. A payment sent through a funds-transfer issuer, a receipt through a trust-type one, a settlement routed through a foreign issuer. The issuers differ and so do the intermediaries, but **the reconciliation procedure does not change.** Not having to rebuild the matching procedure per type is what makes a practical difference while several of them run in parallel.

The same shape is common enough in regulated industries: on a factory floor, shipments stall on paperwork going back and forth. The inspection records exist, but the receiving side has no way to confirm them, so originals get submitted and queried again and again. What is being handled is different; where it jams is the same.

## When the other side of the match is a machine

Everything so far assumes a person doing the confirming.

Agents taking on the decision to pay, and the execution of it, is going to become more common. In Japan, Pacific Meta began supporting adoption of B2B payments and AI agent payments in yen-denominated stablecoins in April 2026. Once that happens the matching side is a machine too, and instead of a person reading the books and being satisfied, you need a form that can be decided by computation alone.

It is still some way off — and the earlier the shape of the record is settled, the cheaper it is later. Changing the format once operations are running usually costs more.

What Lemma handles is the contents of the record and its route. It is designed so that reconciliation takes the same form regardless of which chain issued the coin, which type of issuer it came from, or which operator sat in between. Whether that agent was allowed to make that payment — the authority side — is handled by [Trust402](https://lemma.frame00.com/trust402/). Reconciliation, and authority to act. Two different proofs, on the same API.

## What gets shorter next is the wait on matching

The time a transfer takes has already come down, so if anything shortens next, it is the wait on matching.

There is one thing to add. Put the records you already hold into a form where they can be reconciled without being shown to each other. That alone starts taking time out of the wait for the counterparty's reply first.

To size this up for your own operation, start by counting three things. How many payment statements you send outside the company each month. How many of them draw a query. And how many round trips each query takes to settle.

Issuing a proof happens once. Verification is free, needs no key and no account, and can be run as many times as you like.

Putting a proof on the settlement rate itself is covered in [Put a proof on the rate](https://lemma.frame00.com/blog/verifiable-fx-rates-for-payments/). Where this lands in finance and FinTech is set out in the [finance and FinTech use cases](https://lemma.frame00.com/solutions/use-cases/finance/), and an implementation in public infrastructure in the [MizuDAkO case study](https://lemma.frame00.com/blog/metawater-mizudako-resident-record-proof/).

## Resources

- AZ-COM MARUWA Holdings adopts JPYC for subcontractor payments to ~2,300 counterparties (July 2026) — [Zaikei Shimbun](https://www.zaikei.co.jp/article/20260721/862143.html)
- Three megabanks' jointly issued trust-type stablecoin, live transactions during fiscal 2026 — [Nikkei](https://www.nikkei.com/article/DGXZQOUB0966P0Z00C26A6000000/) / [Impress Watch](https://www.watch.impress.co.jp/docs/news/2116013.html)
- JPYSC (SBI Shinsei Trust Bank, available from 24 June 2026) — [SBI Holdings](https://www.sbigroup.co.jp/news/2026/0624_16425.html)
- Stablecoin market survey (2026) — [Yano Research Institute](https://www.yano.co.jp/press-release/show/press_id/4062)
- Pacific Meta begins adoption and development support for stablecoin payments and AI agent payments (April 2026) — [Pacific Meta](https://pacific-meta.co.jp/news/press-release/6710/)
