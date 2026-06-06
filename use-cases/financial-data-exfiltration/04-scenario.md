---
title: "A real-world example"
---


In 2026, an insurance company seconding employees to agency partners (regional banks and securities firms) lost 2,476 customer records across 36 partner offices (MetLife Japan). The incident is a textbook case of the gap between detection and proof.

The root cause is straightforward: **an ambiguous trust boundary operated on mutable logs.** Access looked legitimate, logs were post-hoc editable, and there was no shared truth layer between origin and receiver. DLP and SIEM could detect anomalies, but no mechanism existed to fix detected events as tamper-evident facts.

Detailed timeline analysis, before-and-after comparison, and regulatory response time estimates are shared in the industry-specific kit we send after the consultation call.
