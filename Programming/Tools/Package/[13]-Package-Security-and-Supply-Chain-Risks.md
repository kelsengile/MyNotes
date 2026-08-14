[Previous](./[12]-Private-And-Internal-Registries.md) | [Table of Contents](./[0]-Introduction-to-Package.md) | [Next](./[14]-Dependency-Conflicts-And-Troubleshooting.md)

---

# Lesson 13 - Package Security And Supply-Chain Risks

## 13.1 What Is a Supply-Chain Risk?

When you install a package, you're not just trusting its author — you're trusting every package in its entire dependency tree, often hundreds of packages you never chose directly. A **supply-chain attack** happens when malicious code sneaks into that chain, and everyone who installs it (or a package that depends on it) is affected.

---

## 13.2 Common Types of Package-Based Attacks

- **Malicious package publication** — an attacker publishes a brand-new package designed to look legitimate, containing harmful code
- **Typosquatting** — a package named to look like a popular one (e.g. `reqeusts` instead of `requests`), hoping developers mistype the name
- **Account takeover** — an attacker compromises a legitimate maintainer's account and pushes a malicious update to an already-trusted package
- **Dependency confusion** — an attacker publishes a public package with the same name as your organization's *private* internal package, tricking misconfigured tools into installing the public (malicious) one instead
- **Compromised build/CI pipeline** — malicious code is injected during the package's build process rather than in its source code directly

---

## 13.3 Auditing Your Dependencies

Most package managers include a built-in vulnerability scanner:

```bash
npm audit
pip-audit             # Python (third-party tool)
cargo audit            # Rust (third-party tool)
```

These check your installed packages against databases of known vulnerabilities (like the National Vulnerability Database or GitHub Advisory Database) and report anything affected, often with a suggested fix.

---

## 13.4 Automated Dependency Update Tools

Tools like **Dependabot** (GitHub), **Renovate**, and **Snyk** automatically monitor your dependencies and open pull requests when updates — especially security patches — become available, so vulnerabilities don't sit unnoticed for months.

---

## 13.5 Reducing Your Attack Surface

- **Minimize dependencies** — every package you add expands your dependency tree and your risk surface
- **Pin versions and use lockfiles** — prevents an unexpected, unreviewed version from being silently installed
- **Review before updating major versions** — don't blindly auto-update everything
- **Check package provenance** — does it have a public repository, real maintainers, recent activity, and a reasonable number of other projects depending on it?
- **Use audit tools in CI** — fail builds automatically if a known-vulnerable package is detected

---

## 13.6 Verifying Package Integrity

Registries typically publish a checksum/hash for each package version, and lockfiles record it too. This lets package managers verify that what they downloaded matches exactly what was published — protecting against tampering in transit.

---

## 13.7 A Real Example: left-pad (2016)

In 2016, a developer unpublished a tiny npm package called `left-pad` that many other packages depended on transitively. Because so much of the JavaScript ecosystem depended on it — directly or indirectly — its removal broke builds across the internet for hours. This incident is a big part of why most registries now restrict or disallow fully deleting already-published versions (see file [10] on yanking vs. deleting).

---

[Previous](./[12]-Private-And-Internal-Registries.md) | [Table of Contents](./[0]-Introduction-to-Package.md) | [Next](./[14]-Dependency-Conflicts-And-Troubleshooting.md)
