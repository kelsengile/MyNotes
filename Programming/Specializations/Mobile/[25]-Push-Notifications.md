[Previous](./[24]-Location-and-Maps.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[26]-Sensors-and-Permissions.md)

*Device Features*

# Lesson 25 - Push Notifications

## 25.1 How Push Notifications Work

Push notifications let a server alert a user even when the app isn't open, through a chain of services: your backend server sends a message to a **push notification service** (Apple's APNs for iOS, Firebase Cloud Messaging/FCM for Android), which relays it to the specific device. The device displays the notification in the system notification tray.

---

## 25.2 Registering for Push

The app requests notification permission from the user, then registers with the OS to receive a unique **device token** — an identifier that your backend uses to target that specific device:

```dart
FirebaseMessaging messaging = FirebaseMessaging.instance;
await messaging.requestPermission();
String? token = await messaging.getToken();
// send `token` to your backend to store against this user
```

---

## 25.3 Local vs Remote Notifications

- **Remote (push) notifications** originate from a server, as described above.
- **Local notifications** are scheduled directly by the app itself, without any server involvement — useful for reminders, alarms, or "your download is complete" messages triggered by something that happened on-device.

---

## 25.4 Handling Notification Taps

Notifications need to route the user somewhere meaningful when tapped — often into the specific screen related to the notification's content (e.g., tapping a "new message" notification opens that conversation). This ties directly into the deep linking concepts from Lesson 27, since a notification payload typically carries the same kind of route information a deep link URL would.

## 25.5 Notification Best Practices

Over-notifying is one of the top reasons users disable notifications entirely or uninstall an app. Best practice is to let users control notification categories individually (e.g., "marketing" vs. "order updates") rather than an all-or-nothing toggle, and to send notifications only when they carry real, timely value to the user.

[Previous](./[24]-Location-and-Maps.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[26]-Sensors-and-Permissions.md)
