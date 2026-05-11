---
title: "Problem"
---


In financial institutions, personnel movement across organizations is routine. Secondments, outsourced operations, agency partnerships, joint ventures — in every case, the moving employee holds valid credentials from both the origin and host organizations, with legitimate access to customer data in both.

At that point, the trust boundary becomes structurally ambiguous:

- **Access itself appears legitimate:** The employee accesses official systems with valid credentials.
- **Access logs are mutable:** Both organizations store logs in standard databases with no cryptographic integrity guarantee.
- **No shared proof layer exists:** The origin and host organizations have no means to independently verify each other's logs.
- **Deterrence is weak:** Employees know that "logs can be disputed," so psychological deterrence does not function.

When the problem surfaces — customer complaints, regulatory audits, internal whistleblowing — neither organization can precisely prove what was accessed and when. Mutual recrimination, regulatory penalties, and reputational damage occur simultaneously.

Regulatory pressure is intensifying. Japan's revised Act on the Protection of Personal Information, the FSA's cybersecurity guidelines, AI governance frameworks, GDPR extraterritorial application, and national data protection laws worldwide are all shifting requirements from "logs exist" to "tamper-proof grounds can be proven."

Detection already exists (DLP, SIEM). What is missing is a layer that ensures detected events survive as tamper-proof facts.
