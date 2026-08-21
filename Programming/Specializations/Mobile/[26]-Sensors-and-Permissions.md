[Previous](./[25]-Push-Notifications.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[27]-Deep-Linking-and-App-Links.md)

*Device Features*

# Lesson 26 - Sensors & Permissions

## 26.1 Common Device Sensors

Phones carry a range of built-in sensors apps can read from:

- **Accelerometer** — measures movement/tilt, used for step counting or shake-to-undo gestures.
- **Gyroscope** — measures rotation, used in AR and gaming.
- **Magnetometer (compass)** — measures orientation relative to magnetic north, used in navigation apps.
- **Biometric sensors** — fingerprint/Face ID readers, used for secure authentication.
- **Proximity sensor** — detects when the phone is near the user's face, used to turn off the screen during calls.

```dart
accelerometerEvents.listen((event) {
  print("x: ${event.x}, y: ${event.y}, z: ${event.z}");
});
```

---

## 26.2 The Permission Model

iOS and Android both follow a **runtime permission** model for sensitive capabilities (camera, location, contacts, biometrics, notifications): the app must ask, and the user can grant or deny at the moment of use, and can revoke it later in system settings. This is a deliberate privacy protection — permissions aren't granted permanently just because the app is installed.

---

## 26.3 Requesting Permissions Gracefully

Best practice is to request a permission **contextually** — right when the feature that needs it is used, with a clear explanation of why — rather than requesting every permission upfront at first launch, which users are far more likely to deny reflexively.

```dart
var status = await Permission.camera.request();
if (status.isGranted) {
  openCamera();
} else if (status.isPermanentlyDenied) {
  openAppSettings();
}
```

---

## 26.4 Biometric Authentication

Fingerprint and Face ID sensors can be used to authenticate the user locally (e.g., unlocking the app or approving a payment) without ever handling raw biometric data yourself — the OS performs the actual biometric match and simply tells your app "yes, this is the enrolled user" or "no."

[Previous](./[25]-Push-Notifications.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[27]-Deep-Linking-and-App-Links.md)
