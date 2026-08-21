[Previous](./[4]-The-CIA-Triad-and-Security-Principles.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[6]-Common-Attack-Vectors-Overview.md)

*Core Concepts*

# Lesson 5 - Threats, Vulnerabilities & Risk

## 5.1 Defining the Terms

These three words are used precisely in security, and mixing them up leads to confused thinking:

- A **threat** is anything that could cause harm — an attacker, a piece of malware, a natural disaster, or even an careless employee.
- A **vulnerability** is a weakness that a threat could exploit — an unpatched server, a weak password policy, a misconfigured firewall.
- **Risk** is the potential for loss when a threat exploits a vulnerability, generally understood as a function of likelihood and impact: `Risk = Threat × Vulnerability × Impact` (conceptually, not a literal formula).

A threat with no matching vulnerability poses little risk. A vulnerability with no realistic threat targeting it also poses little risk. Risk emerges where the two meet.

---

## 5.2 Types of Threat Actors

Understanding *who* might attack a system helps predict *how* they might attack it:

- **Script kiddies** — low-skill attackers using pre-built tools, often for status or mischief.
- **Cybercriminals** — financially motivated, running ransomware or fraud operations.
- **Hacktivists** — motivated by political or social causes.
- **Insider threats** — employees or contractors who misuse legitimate access, intentionally or accidentally.
- **Advanced Persistent Threats (APTs)** — well-resourced, often nation-state-linked groups conducting long-term, stealthy campaigns.

---

## 5.3 Assessing and Prioritizing Risk

Organizations can't fix every vulnerability at once, so they prioritize. Common approaches include:

- **Qualitative risk assessment** — rating likelihood and impact as High/Medium/Low.
- **Quantitative risk assessment** — assigning dollar values to estimate expected loss (e.g., Annualized Loss Expectancy).
- **CVSS (Common Vulnerability Scoring System)** — a standardized 0–10 score used to rate the severity of specific software vulnerabilities, factoring in things like how easily a vulnerability can be exploited and what access it grants.

Prioritization typically focuses limited resources on vulnerabilities that are both severe and actively exploited in the wild.

---

## 5.4 Risk Treatment Strategies

Once a risk is identified, an organization generally chooses one of four responses:

- **Mitigate** — reduce the risk (e.g., patch the vulnerability, add a control).
- **Accept** — knowingly take no action, usually because the cost of mitigation outweighs the potential impact.
- **Transfer** — shift the risk to another party, such as through cyber insurance.
- **Avoid** — eliminate the risk entirely, often by discontinuing the risky activity or system.

There is no such thing as zero risk — the goal of a security program is to bring risk down to a level the organization is willing to accept.

[Previous](./[4]-The-CIA-Triad-and-Security-Principles.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[6]-Common-Attack-Vectors-Overview.md)
