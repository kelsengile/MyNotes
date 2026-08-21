[Previous](./[39]-Publishing-to-App-Stores.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[41]-Analytics-and-Crash-Reporting.md)

*Publishing & Distribution*

# Lesson 40 - App Updates & Versioning

## 40.1 Version Numbers vs Build Numbers

Mobile apps track two separate numbers:

- **Version name** (e.g. `2.4.1`) — the human-facing version, usually following semantic versioning, shown to users in the store listing.
- **Build number** (e.g. `147`) — an internal, ever-incrementing identifier for each individual submitted build, used to distinguish builds that share the same version name (e.g. during beta testing multiple builds of `2.4.1`).

Both must increase with every new submission — stores reject a build with a version/build number equal to or lower than one already published.

---

## 40.2 Handling Breaking Changes

When an update changes something a previous version of the app relied on — a removed API endpoint, a changed data format — older installed versions still on users' devices can break unless the backend maintains backward compatibility or the app enforces a **minimum supported version**, prompting users on outdated versions to update before continuing.

---

## 40.3 Force Updates vs Optional Updates

- **Optional update** — the store shows an "Update available" badge, but the app still functions on the old version.
- **Force update** — the app checks its own version against a server-provided minimum on launch, and blocks usage entirely until the user updates, typically reserved for critical security fixes or breaking backend changes.

```swift
if currentVersion < minimumRequiredVersion {
    showForceUpdateScreen()
}
```

---

## 40.4 Over-the-Air (OTA) Updates

Some cross-platform frameworks support pushing certain code/asset changes directly to users **without** a full store review cycle:

- **React Native** — via **CodePush** (or similar services), which can patch JavaScript bundle changes instantly.
- **Flutter** — offers limited OTA capability for non-native-code changes through certain third-party tools.

Both app stores' guidelines restrict OTA updates to non-binary changes (JS/asset patches) — you cannot bypass review to ship new native functionality this way.

---

## 40.5 Rollback Strategy

If a released update introduces a serious bug, the fastest mitigation is often a **staged rollout halt** (stopping further distribution of the bad version) combined with an expedited fix submission, rather than waiting for the standard review timeline — both stores offer expedited review requests for critical issues.

[Previous](./[39]-Publishing-to-App-Stores.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[41]-Analytics-and-Crash-Reporting.md)
