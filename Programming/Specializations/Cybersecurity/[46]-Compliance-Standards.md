[Previous](./[45]-Security-Policies-and-Frameworks.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[47]-Building-a-Security-Mindset-and-Threat-Modeling.md)

*Governance & Compliance*

# Lesson 46 - Compliance Standards (GDPR, PCI-DSS, HIPAA Overview)

## 46.1 Compliance vs. Security

**Compliance** means meeting a specific set of external requirements — legal, regulatory, or contractual — while **security** is the broader, ongoing practice of actually protecting systems and data. The two overlap significantly but aren't identical: an organization can be technically compliant with a specific standard's checklist while still having meaningful security gaps outside that standard's scope, which is why compliance should be treated as a floor, not a ceiling.

---

## 46.2 GDPR (General Data Protection Regulation)

The **GDPR** is a European Union regulation governing how organizations collect, process, and protect the personal data of individuals in the EU, regardless of where the organization itself is based. Key concepts include requiring a lawful basis for processing personal data, giving individuals rights over their own data (like the right to access or delete it), and mandating that qualifying data breaches be reported to regulators within a strict timeframe (72 hours). Non-compliance can carry significant financial penalties.

---

## 46.3 PCI-DSS (Payment Card Industry Data Security Standard)

**PCI-DSS** is an industry (not governmental) standard that applies to any organization that stores, processes, or transmits credit card data. It defines specific technical and operational requirements, such as encrypting cardholder data, restricting access on a need-to-know basis, and maintaining strong network segmentation (Lesson 9) to isolate systems that handle card data from the rest of the network. Compliance is typically enforced through contractual agreements with payment card brands and processors rather than government law.

---

## 46.4 HIPAA (Health Insurance Portability and Accountability Act)

**HIPAA** is a U.S. law that governs the privacy and security of health information ("Protected Health Information," or PHI). Its Security Rule requires administrative, physical, and technical safeguards to protect electronic PHI, while its Privacy Rule governs how and when patient information can be used or disclosed. Organizations subject to HIPAA include healthcare providers, insurers, and their business associates who handle PHI on their behalf.

---

## 46.5 Common Threads Across Compliance Standards

Despite covering different industries and jurisdictions, most compliance standards share recurring themes that echo concepts throughout this course: strong access controls (Lesson 13), encryption of sensitive data (Lessons 15–17), logging and monitoring (Lesson 32), incident response capability (Lesson 34), and regular risk assessment (Lesson 5). Understanding the underlying security principles makes navigating any specific compliance requirement significantly easier, since the requirements are largely applications of the same fundamentals to a specific legal or contractual context.

[Previous](./[45]-Security-Policies-and-Frameworks.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[47]-Building-a-Security-Mindset-and-Threat-Modeling.md)
