[Previous](./[13]-Local-Storage.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[15]-Testing-Flutter-Apps.md)

*Data And Platform Features*

# Lesson 14 - Packages And Platform Features

You rarely build everything yourself. This lesson covers adding packages, using device features like the camera and location, writing your own platform channel code, and getting started with Firebase.

---

## 14.1 pub.dev and dependencies

**pub.dev** is the official package repository for Dart and Flutter. Each package page shows a version, a score (likes, pub points, popularity), supported platforms, a changelog, and an example.

**Adding a package:**

```bash
flutter pub add http
flutter pub add --dev flutter_lints
```

This edits `pubspec.yaml` for you:

```yaml
dependencies:
  flutter:
    sdk: flutter
  http: ^1.2.0

dev_dependencies:
  flutter_test:
    sdk: flutter
  flutter_lints: ^4.0.0
```

- **`dependencies`** ship with your app, and **`dev_dependencies`** are only for development and testing.
- `^1.2.0` means "1.2.0 or newer, but below 2.0.0" (semantic versioning).
- `pubspec.lock` records the exact versions resolved. Commit it for apps.

**Common commands:**

| Command | Purpose |
|---|---|
| `flutter pub get` | Download packages listed in pubspec |
| `flutter pub upgrade` | Move to the newest allowed versions |
| `flutter pub outdated` | Show available newer versions |
| `flutter pub remove <name>` | Remove a package |

**Using a package:**

```dart
import 'package:http/http.dart' as http;
```

**Choosing packages wisely:**

- Check **platform support**, **recent releases**, open issues, and the publisher (a verified badge helps).
- Prefer **Flutter Favorite** packages where possible.
- Fewer dependencies mean fewer breaking changes and a smaller app.
- Packages can be **plugins** (include native Android/iOS code) or pure Dart. Plugins often need extra platform setup, as in the next section.

---

## 14.2 Permissions, camera, and location

Sensitive features need the user's **permission**, and each platform requires you to **declare** why you need it.

**Declaring permissions:**

Android, `android/app/src/main/AndroidManifest.xml` (above `<application>`):

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

iOS, `ios/Runner/Info.plist` (a missing description string crashes the app or gets it rejected):

```xml
<key>NSCameraUsageDescription</key>
<string>We use the camera to scan receipts.</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>We use your location to show nearby stores.</string>
```

**Requesting at runtime** with `permission_handler` (`flutter pub add permission_handler`):

```dart
import 'package:permission_handler/permission_handler.dart';

Future<bool> ensureCamera() async {
  var status = await Permission.camera.status;
  if (status.isDenied) {
    status = await Permission.camera.request();
  }
  if (status.isPermanentlyDenied) {
    await openAppSettings();   // send the user to system settings
    return false;
  }
  return status.isGranted;
}
```

**Camera and photos** with `image_picker` (`flutter pub add image_picker`) for the simplest case:

```dart
import 'package:image_picker/image_picker.dart';

final picker = ImagePicker();

Future<void> takePhoto() async {
  final XFile? photo = await picker.pickImage(source: ImageSource.camera);
  if (photo != null) {
    // display with Image.file(File(photo.path)) (not on web)
    print(photo.path);
  }
}
```

For a live camera preview, use the `camera` package.

**Location** with `geolocator` (`flutter pub add geolocator`):

```dart
import 'package:geolocator/geolocator.dart';

Future<Position?> getPosition() async {
  if (!await Geolocator.isLocationServiceEnabled()) return null;

  var permission = await Geolocator.checkPermission();
  if (permission == LocationPermission.denied) {
    permission = await Geolocator.requestPermission();
  }
  if (permission == LocationPermission.denied ||
      permission == LocationPermission.deniedForever) {
    return null;
  }
  return Geolocator.getCurrentPosition();
}
```

**UX rules:** ask for a permission **at the moment it is needed**, explain why first, and keep the app usable if the user says no.

---

## 14.3 Platform channels

When no package offers a native feature, you can call platform code yourself with a **`MethodChannel`**. Dart sends a named message, native code (Kotlin/Swift) replies.

**Dart side:**

```dart
import 'package:flutter/services.dart';

class BatteryService {
  static const _channel = MethodChannel('com.example.app/battery');

  Future<int?> getBatteryLevel() async {
    try {
      return await _channel.invokeMethod<int>('getBatteryLevel');
    } on PlatformException catch (e) {
      print('Failed: ${e.message}');
      return null;
    }
  }
}
```

**Android side** (`android/app/src/main/kotlin/.../MainActivity.kt`):

```kotlin
import android.content.Context
import android.os.BatteryManager
import io.flutter.embedding.android.FlutterActivity
import io.flutter.embedding.engine.FlutterEngine
import io.flutter.plugin.common.MethodChannel

class MainActivity : FlutterActivity() {
    private val channel = "com.example.app/battery"

    override fun configureFlutterEngine(flutterEngine: FlutterEngine) {
        super.configureFlutterEngine(flutterEngine)
        MethodChannel(flutterEngine.dartExecutor.binaryMessenger, channel)
            .setMethodCallHandler { call, result ->
                if (call.method == "getBatteryLevel") {
                    val bm = getSystemService(Context.BATTERY_SERVICE) as BatteryManager
                    val level = bm.getIntProperty(BatteryManager.BATTERY_PROPERTY_CAPACITY)
                    result.success(level)
                } else {
                    result.notImplemented()
                }
            }
    }
}
```

**iOS side** is the same idea in Swift (`AppDelegate.swift`), registering a `FlutterMethodChannel` with the same channel name and handling the `getBatteryLevel` call.

**Key points:**

- The **channel name must match** exactly on both sides.
- Messages carry standard types (numbers, strings, lists, maps, null).
- Channel calls are **asynchronous**, so always `await` them.
- Unhandled methods should return `notImplemented`.
- For type-safe generated code, use **Pigeon**. For calling C libraries directly, use **FFI** (`dart:ffi`).
- To share a native integration, package it as a **plugin**: `flutter create --template=plugin my_plugin`.

---

## 14.4 Firebase basics

**Firebase** is Google's backend platform. Common services: **Authentication**, **Cloud Firestore** (NoSQL database), **Cloud Storage**, **Cloud Messaging** (push notifications), **Analytics**, and **Crashlytics**.

**Setup** using the FlutterFire CLI:

```bash
# one-time
dart pub global activate flutterfire_cli
# 1. Create a project at console.firebase.google.com
# 2. In your Flutter project folder:
flutterfire configure
flutter pub add firebase_core firebase_auth cloud_firestore
```

`flutterfire configure` generates `lib/firebase_options.dart` and links your platforms.

**Initialize** before `runApp`:

```dart
import 'package:firebase_core/firebase_core.dart';
import 'firebase_options.dart';

Future<void> main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp(
    options: DefaultFirebaseOptions.currentPlatform,
  );
  runApp(const MyApp());
}
```

**Authentication (email/password):**

```dart
import 'package:firebase_auth/firebase_auth.dart';

final auth = FirebaseAuth.instance;

await auth.createUserWithEmailAndPassword(email: email, password: password);
await auth.signInWithEmailAndPassword(email: email, password: password);
await auth.signOut();

// React to login state
auth.authStateChanges().listen((User? user) {
  print(user == null ? 'Signed out' : 'Signed in: ${user.uid}');
});
```

Enable the sign-in method in the Firebase console first.

**Firestore (database):**

```dart
import 'package:cloud_firestore/cloud_firestore.dart';

final notes = FirebaseFirestore.instance.collection('notes');

await notes.add({'title': 'Hello', 'created': FieldValue.serverTimestamp()});

// Live updates in the UI
StreamBuilder<QuerySnapshot>(
  stream: notes.orderBy('created', descending: true).snapshots(),
  builder: (context, snapshot) {
    if (!snapshot.hasData) return const CircularProgressIndicator();
    final docs = snapshot.data!.docs;
    return ListView(
      children: [
        for (final d in docs)
          ListTile(title: Text((d.data() as Map<String, dynamic>)['title'])),
      ],
    );
  },
)
```

**Security:** write **Firestore Security Rules** that only allow signed-in users to read or write their own data. Leaving the default open "test mode" rules in production is dangerous.

---

[Previous](./[13]-Local-Storage.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[15]-Testing-Flutter-Apps.md)