[Previous](./[27]-Introduction-to-Penetration-Testing.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[29]-Scanning-and-Enumeration.md)

*Offensive Security*

# Lesson 28 - Reconnaissance & Information Gathering

## 28.1 What is Reconnaissance?

**Reconnaissance** ("recon") is the first phase of an attack or authorized penetration test: gathering as much information as possible about a target before attempting any direct interaction. Good recon often determines the success of everything that follows, since it reveals what technologies, people, and weaknesses a target actually has.

---

## 28.2 Passive Reconnaissance

**Passive recon** gathers information without directly interacting with the target's systems, making it undetectable to the target. Sources include:

- **OSINT (Open-Source Intelligence)** — publicly available information: company websites, job postings (which often reveal internal technologies), social media, news articles, and public records.
- **WHOIS lookups** — reveal domain registration information, such as who registered a domain and when.
- **DNS reconnaissance** — enumerating subdomains and DNS records can reveal the scope of an organization's internet-facing infrastructure.
- **Search engine techniques ("Google dorking")** — using advanced search operators to find exposed files, login pages, or misconfigured systems indexed by search engines.
- **Public code repositories** — searching for accidentally committed credentials or internal information in public GitHub repos.

---

## 28.3 Active Reconnaissance

**Active recon** involves direct interaction with the target's systems, which carries a higher risk of detection. It includes techniques like directly querying a target's DNS servers, visiting a target's website to observe its technology stack, or lightweight probing that overlaps with the scanning phase (Lesson 29). Because active recon can be logged and detected, penetration testers are careful to stay strictly within the authorized scope.

---

## 28.4 Social Engineering Reconnaissance

Recon also supports social engineering attacks (Lesson 25): identifying employee names, roles, and email address formats from LinkedIn or a company website makes a phishing email far more convincing and targeted (spear phishing).

---

## 28.5 Why This Matters for Defense

Understanding reconnaissance techniques helps defenders too — this is sometimes called **attack surface management**. Organizations regularly perform their own OSINT against themselves to discover what an attacker could find: exposed subdomains, leaked credentials, employee information available online, and misconfigured public-facing assets, so these can be addressed proactively rather than discovered by an actual attacker first.

[Previous](./[27]-Introduction-to-Penetration-Testing.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[29]-Scanning-and-Enumeration.md)
