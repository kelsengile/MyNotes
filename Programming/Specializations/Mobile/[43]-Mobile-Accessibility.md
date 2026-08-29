[Previous](./[42]-In-App-Purchases-and-Monetization.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./%5B44%5D-Performance-Optimization%20%281%29.md)

*Best Practices*

# Lesson 43 - Mobile Accessibility

## 43.1 Why Accessibility Matters

Accessibility means designing apps that people with visual, motor, auditory, or cognitive disabilities can actually use. It's not a niche concern — a meaningful share of any user base relies on some form of assistive technology, and both Apple and Google enforce accessibility standards as part of their store review guidelines.

---

## 43.2 Screen Readers

Screen readers narrate on-screen content aloud for users with visual impairments — **VoiceOver** on iOS, **TalkBack** on Android. Every interactive element needs a meaningful **accessibility label**, or the screen reader will announce nothing useful (or just "button").

```swift
Image(systemName: "trash")
    .accessibilityLabel("Delete item")
```

```kotlin
IconButton(onClick = { delete() }) {
    Icon(Icons.Default.Delete, contentDescription = "Delete item")
}
```

---

## 43.3 Touch Target Sizes

Interactive elements (buttons, icons, links) need to be large enough to tap reliably, especially for users with motor impairments:

- **iOS** recommends a minimum touch target of **44x44 points**.
- **Android** recommends a minimum of **48x48 dp**.

Elements smaller than this should have their tappable area padded out even if the visible icon stays small.

---

## 43.4 Color Contrast and Dynamic Type

- **Color contrast** — text needs sufficient contrast against its background (the **WCAG** standard recommends a minimum 4.5:1 ratio for normal text) so it remains legible for users with low vision or color blindness. Never rely on color alone to convey information (e.g. "red = error") — pair it with an icon or text label.
- **Dynamic Type / Font Scaling** — both platforms let users increase their system-wide text size; apps should use scalable font units (not fixed pixel sizes) so layouts adapt rather than clipping or overlapping when text grows.

---

## 43.5 Testing Accessibility

Both platforms include built-in accessibility auditing tools — **Xcode's Accessibility Inspector** and **Android Studio's Accessibility Scanner** — which flag missing labels, low contrast, and undersized touch targets automatically. The most reliable check, though, is manually navigating your own app with the screen reader turned on.

[Previous](./[42]-In-App-Purchases-and-Monetization.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./%5B44%5D-Performance-Optimization%20%281%29.md)
