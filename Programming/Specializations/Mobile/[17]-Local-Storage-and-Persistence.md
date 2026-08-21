[Previous](./[16]-Handling-User-Input-and-Forms.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[18]-Working-with-Databases.md)

*State & Data*

# Lesson 17 - Local Storage & Persistence

## 17.1 Why Persist Data Locally?

In-memory state (Lesson 15) disappears the moment the app closes. **Persistence** means saving data to the device's disk so it survives app restarts — user preferences, login tokens, cached content for offline viewing, or draft text the user hasn't submitted yet.

---

## 17.2 Key-Value Storage

For small, simple pieces of data, apps use lightweight key-value stores:

- **Flutter**: `shared_preferences` package.
- **iOS**: `UserDefaults`.
- **Android**: `SharedPreferences` / Jetpack `DataStore`.

```dart
final prefs = await SharedPreferences.getInstance();
await prefs.setBool('darkMode', true);
bool darkMode = prefs.getBool('darkMode') ?? false;
```

Key-value storage is meant for simple settings and flags — not for large amounts of structured data, which belongs in a database (Lesson 18).

---

## 17.3 File Storage

Apps can also read and write raw files to a sandboxed directory on the device — useful for caching downloaded images, saving exported PDFs, or storing user-generated content like photos. Every app has its own private storage area that other apps cannot access, which is a core part of mobile security (Lesson 45).

---

## 17.4 Secure Storage

Sensitive data — authentication tokens, passwords, API keys — should never be stored in plain key-value storage, since it isn't encrypted. Instead, use platform-provided secure storage backed by hardware encryption:

- **iOS**: Keychain.
- **Android**: EncryptedSharedPreferences / Android Keystore.
- **Flutter**: `flutter_secure_storage` package, which wraps both under one API.

## 17.5 Caching Strategy

A common pattern is **cache-then-network**: show cached data instantly on screen load for a fast, offline-friendly experience, then fetch fresh data in the background and update the UI once it arrives. This is explored further alongside networking in Lesson 22.

[Previous](./[16]-Handling-User-Input-and-Forms.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[18]-Working-with-Databases.md)
