[Previous](./[25]-Social-Engineering-and-Phishing.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[27]-Introduction-to-Penetration-Testing.md)

*Malware & Threats*

# Lesson 26 - Ransomware & Data Exfiltration

## 26.1 How Ransomware Works

**Ransomware** is malware that encrypts a victim's files, rendering them inaccessible, and demands a ransom payment (usually in cryptocurrency) for a decryption key. A typical ransomware attack chain looks like:

1. **Initial access** — often via phishing, an exposed remote access service (like RDP with a weak password), or exploiting an unpatched vulnerability.
2. **Lateral movement** — the attacker spreads across the network, often seeking high-value systems and backups first.
3. **Privilege escalation** — gaining administrative access to maximize the attack's reach (Lesson 31).
4. **Deployment** — the ransomware encrypts files across as many systems as possible, often deliberately targeting or deleting backups first, so recovery isn't a simple option.
5. **Extortion** — a ransom note demands payment for a decryption key.

---

## 26.2 Double and Triple Extortion

Modern ransomware groups often go beyond simple encryption:

- **Double extortion** — before encrypting files, attackers first steal (exfiltrate) sensitive data, then threaten to publish it publicly if the ransom isn't paid — this pressures victims even if they can restore from backups without paying.
- **Triple extortion** — adds further pressure, such as directly contacting the victim's customers or partners, or launching DDoS attacks against the victim, to increase pressure to pay.

---

## 26.3 Ransomware-as-a-Service (RaaS)

Much modern ransomware operates as **Ransomware-as-a-Service**: a criminal group develops the ransomware and infrastructure, then "affiliates" pay to use it (often via a share of ransom profits) to actually carry out attacks. This division of labor has significantly lowered the technical skill required to launch a ransomware attack, contributing to its prevalence.

---

## 26.4 Data Exfiltration

**Data exfiltration** is the unauthorized transfer of data out of a network, whether as part of a ransomware attack, espionage, or straightforward theft for resale. Common exfiltration channels include uploading data to cloud storage, sending it over encrypted channels to evade inspection, or hiding it within otherwise normal-looking traffic (like DNS tunneling, mentioned in Lesson 7).

Detecting exfiltration relies heavily on monitoring for unusual data movement — a workstation suddenly uploading gigabytes of data to an unfamiliar external destination, or large amounts of access to file shares outside a user's normal pattern.

---

## 26.5 Prevention and Response

- **Regular, tested, offline/immutable backups** — the single most important defense, since backups that attackers can also reach or delete provide no real protection.
- **Patch management and reducing exposed remote access** (Lesson 14) to limit initial access opportunities.
- **Network segmentation** (Lesson 9) to slow or prevent lateral movement.
- **An incident response plan** (Lesson 34) practiced in advance, since decisions made during an active ransomware event are far better made calmly ahead of time.
- Law enforcement and most security experts generally advise against paying ransoms where possible, since payment doesn't guarantee data recovery and funds further criminal activity — though the decision in practice is often complex and organization-specific.

[Previous](./[25]-Social-Engineering-and-Phishing.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[27]-Introduction-to-Penetration-Testing.md)
