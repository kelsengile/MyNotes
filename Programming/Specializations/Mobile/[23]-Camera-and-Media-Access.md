[Previous](./[22]-Handling-Errors-and-Offline-States.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[24]-Location-and-Maps.md)

*Device Features*

# Lesson 23 - Camera & Media Access

## 23.1 Requesting Camera Access

Accessing the camera requires two things: declaring the permission in your app's configuration (Lesson 3.3) with a usage description, and requesting it from the user at runtime — the OS shows a system prompt the user can allow or deny.

```dart
final image = await ImagePicker().pickImage(source: ImageSource.camera);
```

Always handle the "denied" case gracefully — explain why the feature needs the permission and offer a way to open system settings, rather than silently failing.

---

## 23.2 Picking from the Photo Library

Apps commonly let users choose an existing photo instead of taking a new one, using the same picker API with a different source:

```dart
final image = await ImagePicker().pickImage(source: ImageSource.gallery);
```

On both iOS and Android, the OS-provided picker UI runs in a sandboxed process — the app only receives the specific photo the user selected, not access to their entire photo library, which is an important privacy protection.

---

## 23.3 Displaying and Uploading Media

Once captured, media is typically displayed locally (`Image.file(imageFile)`) and optionally uploaded to a server as part of a form submission, usually using `multipart/form-data` requests rather than plain JSON since binary files can't be embedded directly in JSON text.

---

## 23.4 Video and Audio

Beyond still photos, frameworks provide dedicated packages for video recording/playback (`camera` + `video_player` in Flutter) and audio recording/playback (`record`, `audioplayers`). These involve additional considerations like managing device storage for large files and handling interruptions (e.g., a phone call interrupting audio playback).

[Previous](./[22]-Handling-Errors-and-Offline-States.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[24]-Location-and-Maps.md)
