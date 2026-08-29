[Previous](./[35]-Secrets-Management.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[37]-Event-Driven-Architecture-Basics.md)

*Security*

# Lesson 36 - Compliance & the Shared Responsibility Model

## 36.1 Shared Responsibility Recap

As introduced in Lesson 33, the **shared responsibility model** divides security duties between the cloud provider and the customer. The exact split shifts depending on the service model: for IaaS (raw VMs), you're responsible for almost everything above the hypervisor (OS patching, network config, application security); for PaaS, the provider manages the OS and runtime, narrowing your responsibility to application code and data; for SaaS, the provider manages nearly everything except your account security and how you use the data. Understanding exactly where this line falls for each service you use is essential — assuming the provider covers something it doesn't is a common cause of breaches.

---

## 36.2 Compliance Frameworks

Many industries and jurisdictions require adherence to specific **compliance frameworks** governing how data is handled:

- **GDPR** — EU regulation on personal data privacy and protection.
- **HIPAA** — US regulation for protecting health information.
- **PCI-DSS** — security standard for handling payment card data.
- **SOC 2** — an audit framework assessing a service organization's security controls.
- **ISO 27001** — an international standard for information security management systems.

Cloud providers undergo their own audits and hold certifications for many of these frameworks, covering the infrastructure they operate — but using a compliant provider does **not** automatically make your application compliant, since compliance also depends on how you configure and use their services.

---

## 36.3 Auditing and Certifications

Providers publish compliance documentation (e.g. AWS Artifact, Azure Trust Center, Google Cloud Compliance Reports) listing which certifications apply to which services and regions, useful evidence during your own compliance audits. Practical steps toward compliance on top of a compliant provider include: enabling detailed audit logging of all account activity, encrypting sensitive data (Lesson 34) and restricting access via least-privilege IAM (Lesson 5), documenting data flows and retention policies, and, for regulated industries, engaging a qualified auditor familiar with the specific framework you need to meet. Compliance is an ongoing responsibility, not a one-time checkbox — configurations and access should be reviewed regularly as your application evolves.

---

[Previous](./[35]-Secrets-Management.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[37]-Event-Driven-Architecture-Basics.md)
