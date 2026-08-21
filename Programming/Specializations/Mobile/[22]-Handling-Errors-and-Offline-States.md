[Previous](./[21]-Async-Programming.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[23]-Camera-and-Media-Access.md)

*Networking*

# Lesson 22 - Handling Errors & Offline States

## 22.1 Why Mobile Networking Fails Often

Unlike a desktop app on stable wifi, mobile apps run on cellular connections that drop, throttle, or disappear entirely (elevators, subways, airplane mode). Treating the network as unreliable by default — rather than assuming every request succeeds — is essential for a mobile app that doesn't frustrate users.

---

## 22.2 Try/Catch and Error Types

Wrap network and other fallible operations in error handling, and distinguish between different failure types so you can respond appropriately:

```dart
try {
  final user = await fetchUser();
  setState(() => profile = user);
} on SocketException {
  showError("No internet connection");
} on TimeoutException {
  showError("Request timed out");
} catch (e) {
  showError("Something went wrong");
}
```

A generic "Something went wrong" message for every failure is a poor user experience — telling the user specifically that they're offline (and letting them retry) is far more useful than a vague error.

---

## 22.3 UI States: Loading, Error, Empty, Success

Every screen that loads remote data should explicitly design for **four states**, not just the "happy path":

1. **Loading** — show a spinner or skeleton placeholder.
2. **Error** — show a clear message and a retry button.
3. **Empty** — the request succeeded but returned no data (e.g., an empty cart); this is distinct from an error and should say so.
4. **Success** — the actual content.

Skipping the error or empty states is one of the most common gaps between a polished app and an unfinished one.

---

## 22.4 Detecting Connectivity and Offline Support

Apps can actively check connectivity status (e.g., using the `connectivity_plus` package in Flutter) to show a persistent "You're offline" banner, and can queue actions performed offline (like a chat message) to send automatically once the connection returns. Combined with the local caching from Lesson 17, this lets an app remain partially usable even without a connection.

[Previous](./[21]-Async-Programming.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[23]-Camera-and-Media-Access.md)
