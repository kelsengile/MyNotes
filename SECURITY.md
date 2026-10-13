# Security Policy

MyNotes is a collection of Markdown lessons, notes, and exercise files for educational purposes. It is not a deployed application and does not collect user data, but security still matters here: lessons should teach safe practices, and the repository should never contain harmful code or exposed secrets.

---

**Supported Versions**

This repository has no versioned releases. Security fixes are applied to the current `main` branch.

**Scope**

Please report issues such as:
- **Exposed secrets** — API keys, passwords, tokens, or private credentials committed in lessons, exercises, or history
- **Malicious or unsafe code** — Exercise files or snippets that run harmful commands, hide payloads, or could damage a learner's system when executed
- **Dangerous links** — Links in lessons, the README, or RESOURCES.md that point to malware, phishing pages, or compromised sites
- **Insecure teaching** — Lessons that present insecure practices (for example, hard-coded credentials or SQL built from raw user input) as recommended or correct
- **Personal information** — Private data about any individual included in the repository
- **Compromised external assets** — Image or icon sources used in the README that have been taken over or altered

Ordinary mistakes such as typos, broken links, or incorrect explanations are not security issues and should be reported through a regular GitHub issue.

**Reporting a Vulnerability**

For anything sensitive, such as an exposed credential or personal information, **please do not open a public issue**. Instead:

- Use GitHub's **private vulnerability reporting** under the repository's *Security* tab, if enabled, or
- Email the maintainer at **[kelsengile.dev@gmail.com]**

Please include:
- A description of the problem and where it is located (file and line, if possible)
- Why it is a concern
- Any suggested fix

For low-risk concerns, such as an insecure example in a lesson, a public issue or pull request is fine.

**What to Expect**

1. **Acknowledgment** — Reports are acknowledged within a few days.
2. **Assessment** — The issue is reviewed to confirm its scope and severity.
3. **Resolution** — The content is corrected or removed. Exposed credentials should also be revoked or rotated by their owner.
4. **Credit** — You will be credited for the report if you wish.

**Guidelines for Contributors**

- Never commit secrets, API keys, tokens, or real personal data. Use clearly fake placeholder values in examples.
- Test code snippets and exercise files before submitting, and make sure they do nothing harmful.
- Lessons on security topics (such as those in `Cybersecurity`) must stay educational and defensive in focus. Do not submit working malware or step-by-step guides for attacking systems you do not own.
- Cite and verify external links, and prefer reputable sources.
- If you accidentally commit a secret, report it right away so it can be removed and revoked. Deleting it in a later commit is not enough, because it remains in the Git history.

**Responsible Disclosure**

Please allow reasonable time for a fix before sharing a problem publicly, and act in good faith. Researchers who follow this policy will not face action from the maintainer.

---

*Thanks for helping keep this resource safe and trustworthy.*