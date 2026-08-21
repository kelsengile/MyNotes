[Previous](./[40]-App-Updates-and-Versioning.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[42]-In-App-Purchases-and-Monetization.md)

*Growth & Analytics*

# Lesson 41 - Analytics & Crash Reporting

## 41.1 Why Track Usage?

Once an app is published, developers lose the direct visibility they had during development — analytics and crash reporting tools restore that visibility by reporting how real users actually behave and where the app actually fails, in production, at scale.

---

## 41.2 Event Tracking

**Analytics events** are custom signals your app sends when something meaningful happens — a screen view, a button tap, a completed purchase — letting you measure what features are actually used.

```kotlin
Analytics.logEvent("purchase_completed") {
    param("item_id", "premium_plan")
    param("price", 9.99)
}
```

Common tools include **Firebase Analytics**, **Mixpanel**, and **Amplitude**. Most funnel this data into dashboards showing retention (do users come back?), engagement (which screens/features are used most?), and conversion (do users complete key actions like signing up or purchasing?).

---

## 41.3 Crash Reporting

Crash reporting SDKs automatically capture and upload details whenever the app crashes or throws an unhandled exception on a user's device — including the stack trace, device model, OS version, and steps leading up to the crash — without requiring the user to manually report anything.

- **Firebase Crashlytics** is the most widely used tool across both iOS and Android.
- Reports are typically grouped by root cause, so a single bug affecting thousands of users appears as one prioritized issue rather than thousands of separate reports.

---

## 41.4 Non-Fatal Errors and Logging

Beyond hard crashes, most crash reporting tools also let you log **non-fatal errors** — situations that were caught and handled gracefully (e.g. a failed network request) but are still worth tracking, since a spike in a particular non-fatal error often signals a real problem (like a broken backend endpoint) even though the app didn't technically crash.

---

## 41.5 Privacy Considerations

Analytics and crash data can include sensitive information, so most platforms require explicit user consent (particularly under regulations like **GDPR** and **CCPA**) before collecting tracking data, and app store privacy labels (see Lesson 39) require disclosing exactly what's collected and why.

[Previous](./[40]-App-Updates-and-Versioning.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[42]-In-App-Purchases-and-Monetization.md)
