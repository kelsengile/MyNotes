[Previous](./[38]-Preparing-for-Release.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[40]-App-Updates-and-Versioning.md)

*Publishing & Distribution*

# Lesson 39 - Publishing to the App Store & Google Play

## 39.1 Developer Accounts

Publishing to either store requires enrolling in a paid developer program:

- **Apple Developer Program** — a $99/year individual or organization membership, required to submit to the App Store.
- **Google Play Console** — a one-time $25 registration fee per developer account.

---

## 39.2 Store Listing Requirements

Both stores require a listing beyond just the app binary itself: an app name, description, category, privacy policy URL, screenshots (in specific required sizes per device type), and, increasingly, a **privacy "nutrition label"** disclosing exactly what data the app collects and how it's used (Apple's App Privacy details, Google Play's Data Safety section).

---

## 39.3 Apple's App Review Process

Every iOS app submission goes through **App Review** — a combination of automated and human review against Apple's App Store Review Guidelines, checking for crashes, broken functionality, misleading metadata, and policy violations (e.g. inappropriate content, privacy issues). Review typically takes anywhere from a few hours to a couple of days, and apps can be rejected with specific feedback requiring a fix and resubmission.

---

## 39.4 Google Play's Review Process

Android apps go through a similarly automated and policy-driven review, generally faster than Apple's for most submissions, though new developer accounts and apps in sensitive categories (finance, children's apps) often face closer scrutiny and longer review windows.

---

## 39.5 Staged Rollouts and Beta Testing

Both platforms support releasing to a limited audience before a full public launch:

- **TestFlight** (iOS) — lets you distribute beta builds to internal team members or up to 10,000 external testers before public release.
- **Google Play's internal/closed/open testing tracks** — similar staged testing tiers, plus a **staged rollout** feature that gradually increases the percentage of users receiving a production update (e.g. 5% → 20% → 100%), so a bad release can be halted before reaching everyone.

[Previous](./[38]-Preparing-for-Release.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[40]-App-Updates-and-Versioning.md)
