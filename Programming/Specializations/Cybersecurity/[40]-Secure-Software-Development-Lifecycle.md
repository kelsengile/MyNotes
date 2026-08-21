[Previous](./[39]-Cloud-Security-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[41]-Secure-Coding-Practices.md)

*Cloud & Application Security*

# Lesson 40 - Secure Software Development Lifecycle (SSDLC)

## 40.1 Why "Shift Left"?

Traditionally, security testing often happened only at the end of development, right before release — by which point fixing a discovered flaw is far more expensive and disruptive than if it had been caught earlier. **"Shifting left"** means integrating security considerations from the very beginning of the development process, rather than treating it as a final gate.

---

## 40.2 The SSDLC Phases

A typical Secure Software Development Lifecycle maps security activities onto standard development phases:

1. **Requirements** — defining security and compliance requirements alongside functional ones from the start.
2. **Design** — **threat modeling** (Lesson 47) to identify potential security issues in the architecture before any code is written.
3. **Development** — following secure coding practices (Lesson 41) and using automated tools to catch issues as code is written.
4. **Testing** — dedicated security testing, including the tools and techniques discussed in Lesson 43.
5. **Deployment** — secure configuration of the production environment, including the hardening principles from Lesson 14.
6. **Maintenance** — ongoing patching, monitoring, and periodic reassessment as the software and its threat landscape evolve.

---

## 40.3 Static and Dynamic Analysis

- **SAST (Static Application Security Testing)** — analyzes source code without executing it, looking for known insecure patterns (like the injection vulnerabilities in Lesson 20). Can be run automatically as part of the development pipeline.
- **DAST (Dynamic Application Security Testing)** — tests a running application from the outside, similar to how an attacker would interact with it, catching issues that only appear at runtime.
- **SCA (Software Composition Analysis)** — scans third-party libraries and dependencies for known vulnerabilities, addressing the "vulnerable and outdated components" risk from the OWASP Top 10 (Lesson 19).

---

## 40.4 CI/CD Pipeline Security

Modern development relies heavily on **CI/CD (Continuous Integration/Continuous Deployment)** pipelines, which automatically build, test, and deploy code. Security in this context includes integrating SAST/DAST/SCA scans directly into the pipeline (so vulnerable code is flagged before merging), securing the pipeline infrastructure itself (a compromised pipeline can inject malicious code into every future release), and carefully managing secrets (like API keys) used during automated builds and deployments.

---

## 40.5 Security Champions and Culture

Many organizations designate **security champions** — developers embedded within regular engineering teams who receive extra security training and act as a bridge between the security team and day-to-day development work. This helps scale security expertise across an organization without requiring every single developer to become a security specialist, while still building broader security awareness into everyday engineering culture.

[Previous](./[39]-Cloud-Security-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[41]-Secure-Coding-Practices.md)
