[Previous](./[35]-Digital-Forensics-Basics.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[37]-Identity-and-Access-Management-Concepts.md)

*Identity & Access Management*

# Lesson 36 - Authentication Methods (MFA, SSO)

## 36.1 Authentication Factors

Authentication methods are typically categorized into three factor types:

- **Something you know** — a password, PIN, or security question.
- **Something you have** — a phone, hardware security key, or smart card.
- **Something you are** — biometrics, like a fingerprint or facial recognition.

A less common fourth category, **somewhere you are** (location-based) or **something you do** (behavioral patterns, like typing rhythm), is sometimes used to supplement the primary three.

---

## 36.2 Multi-Factor Authentication (MFA)

**MFA** requires two or more of the above factor types before granting access. Because it requires an attacker to compromise multiple, fundamentally different types of factors (not just guess or steal a single password), MFA dramatically reduces the impact of credential theft and is one of the single most effective, broadly recommended security controls available today.

Common MFA implementations:

- **SMS/voice codes** — convenient but vulnerable to SIM-swapping attacks, making it the weakest common MFA option.
- **Authenticator apps (TOTP)** — generate time-based one-time codes locally on a device, without relying on the phone network.
- **Push notifications** — approve or deny a login attempt directly from a trusted app.
- **Hardware security keys** (e.g., FIDO2/WebAuthn keys) — physical devices offering some of the strongest phishing resistance, since the cryptographic proof is tied to the specific legitimate website.

MFA fatigue attacks — where an attacker who already has a stolen password repeatedly triggers push notifications hoping the victim eventually approves one out of frustration or confusion — are a reminder that even strong controls need user awareness alongside them.

---

## 36.3 Single Sign-On (SSO)

**SSO** lets a user authenticate once and gain access to multiple, independent applications without logging in separately to each. This is achieved via a trusted central **Identity Provider (IdP)** that issues proof of authentication to other applications ("service providers") using protocols like **SAML** or **OpenID Connect (built on OAuth 2.0)**.

Benefits of SSO include a better user experience, centralized control (disabling one account can revoke access everywhere at once), and consistent enforcement of policies like MFA across all connected applications. The trade-off is that a compromised SSO account can potentially grant access to many systems at once, which is exactly why SSO accounts should always be protected with strong MFA.

---

## 36.4 Password Managers

Because strong, unique passwords for every account are difficult to remember, **password managers** securely generate and store credentials, requiring the user to remember only one strong "master password." This directly addresses password reuse — one of the most exploited human weaknesses discussed in Lesson 22 (credential stuffing).

[Previous](./[35]-Digital-Forensics-Basics.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[37]-Identity-and-Access-Management-Concepts.md)
