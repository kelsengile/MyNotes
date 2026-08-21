[Previous](./[26]-Sensors-and-Permissions.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[28]-Background-Tasks-and-Services.md)

*Device Features*

# Lesson 27 - Deep Linking & App Links

## 27.1 What is a Deep Link?

A **deep link** is a URL that opens your app directly to a specific screen, rather than just to its home screen — e.g., tapping `myapp://product/123` opens the product detail screen for item 123, or clicking a shared link in a text message launches the app straight to that content.

---

## 27.2 Custom URL Schemes vs Universal/App Links

- **Custom URL scheme** (`myapp://...`): simple to set up, but only works if the app is already installed, and any app can technically register the same scheme, creating potential conflicts.
- **Universal Links (iOS) / App Links (Android)**: use regular `https://` URLs tied to a domain you own and verify. These are more secure (verified ownership prevents hijacking) and gracefully fall back to opening the website if the app isn't installed.

---

## 27.3 Handling an Incoming Link

The app registers which URL patterns it can handle, then parses the incoming URL to figure out where to navigate:

```dart
void handleDeepLink(Uri uri) {
  if (uri.pathSegments.first == 'product') {
    final id = uri.pathSegments[1];
    navigatorKey.currentState?.pushNamed('/product/$id');
  }
}
```

---

## 27.4 Deferred Deep Linking

A **deferred deep link** solves a tricky case: a user without the app taps a shared link, gets sent to the app store, installs the app, and on first launch is taken directly to the originally-linked content — even though the app wasn't installed yet when they first tapped. This typically requires a third-party linking service (like Branch or Firebase Dynamic Links) that stores the intended destination and matches it to the new install.

[Previous](./[26]-Sensors-and-Permissions.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[28]-Background-Tasks-and-Services.md)
