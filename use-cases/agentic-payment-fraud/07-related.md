---
title: "Related use cases"
---


### Delegated Treasury — agent-driven payments inside the perimeter
**Authorized ≠ attested**

Treasury operations where the agent acts on behalf of the company's own treasury function — payroll, supplier disbursement, intercompany settlement. The delegation pattern is internal but still needs a cryptographic attestation, not just a signed-off policy doc.

[See the use case →](/use-cases/delegated-treasury/)

### Multi-Agent Workflows — chains of delegation across services
**Delegated ≠ traceable**

When one agent calls another, the chain of authority compounds. Each hop needs its own attestation so the final settlement can be traced back to the original principal without manual reconstruction.

[See the use case →](/use-cases/multi-agent-workflows/)

### x402 Commerce — verifiable agent payments on the open rail
**Paid ≠ verified**

The general pattern for x402-rail agent commerce: payment goes through, but the receiving counterparty doesn't know whether the principal and the scope match what the contract requires. Trust402 closes that gap at the protocol layer.

[See the use case →](/use-cases/x402-commerce/)
