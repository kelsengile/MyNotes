[Previous](./[41]-Analytics-and-Crash-Reporting.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[43]-Mobile-Accessibility.md)

*Growth & Analytics*

# Lesson 42 - In-App Purchases & Monetization

## 42.1 Common Monetization Models

- **Paid apps** — a one-time purchase price before download.
- **Freemium** — free to download, with optional paid upgrades (extra features, removing ads, premium content).
- **Subscriptions** — recurring payments for ongoing access (common for content and SaaS-style apps).
- **In-app advertising** — showing ads (banner, interstitial, rewarded video) and earning revenue per impression/click.

Most successful consumer apps today combine several of these — e.g. free with ads, plus a subscription to remove them.

---

## 42.2 In-App Purchases (IAP)

Both platforms require that digital goods and services be sold through their own in-app purchase systems (StoreKit on iOS, Google Play Billing on Android) — you generally cannot process payments for digital content through your own external payment system within the app.

```swift
// StoreKit (iOS)
let products = try await Product.products(for: ["com.example.premium"])
try await products.first?.purchase()
```

Purchases fall into a few categories:

- **Consumable** — can be bought repeatedly (e.g. in-game currency).
- **Non-consumable** — bought once, owned forever (e.g. unlocking a feature).
- **Subscriptions** — auto-renewing or non-renewing recurring access.

---

## 42.3 Receipt Validation

After a purchase, apps must verify it actually happened and wasn't forged, by validating the purchase **receipt** — ideally on a secure backend server rather than trusting the client alone, since client-side checks can be bypassed by a modified/jailbroken app.

---

## 42.4 Store Commission

Both Apple and Google typically take a **15–30% commission** on in-app purchase revenue (often reduced to 15% for smaller developers or subscriptions past their first year), which is an important factor when pricing digital goods sold through the app.

---

## 42.5 Advertising SDKs

Ad monetization typically goes through an SDK like **Google AdMob** or a mediation platform that auctions ad space across multiple ad networks to maximize revenue per impression. Ad placement and frequency require careful tuning — too many interruptive ads (especially interstitials) measurably hurt retention even as they increase short-term revenue.

[Previous](./[41]-Analytics-and-Crash-Reporting.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[43]-Mobile-Accessibility.md)
