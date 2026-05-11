---
title: "A real-world example: an internal AI quoting a stale policy"
---


An internal AI at a financial institution retrieves customer-service SOPs, compliance policies, product specs, and contract templates via RAG to answer the field team's questions. Policies are revised every few months; each department manages them in its own format. The RAG index is rebuilt after each revision, but operationally verifying that prior-version chunks were fully replaced — and that no stale quotes leak through — is hard.

One day a field rep acts on an AI answer, and audit comes back: "That policy was revised three months ago." Tracing which document version backs the chunk the AI quoted means digging across logs and reconstructing index snapshots. There is no cryptographic path that guarantees the authenticity of the citation.

With Lemma in place, each chunk carries its docHash, issuer signature, and active-version metadata from ingestion. A chunk quoted in an AI answer can be cryptographically verified as coming from the version that was live at answer time. Revisions attach a new docHash; references to prior versions are structurally detectable. At audit, the correspondence between AI answers and document versions is presentable without reconstruction.

Industry-specific document class design, integration patterns with knowledge platforms (Confluence, Notion, Box, SharePoint, etc.), and regulatory mappings (FSA, FINRA, healthcare privacy) are shared in the sector-specific kit we send after the consultation call.
