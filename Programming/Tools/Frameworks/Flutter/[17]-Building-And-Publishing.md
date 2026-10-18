[Previous](./[16]-Debugging-And-Performance.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[18]-Next-Steps-With-Flutter.md)

*Quality And Delivery*

# Lesson 17 - Building And Publishing

Shipping an app means producing a **release build**, signing it, and uploading it to a store. Steps change over time, so treat this lesson as a map and confirm the details in the official Flutter and store documentation.

---

## 17.1 Release builds

Debug builds are big and slow. Release builds are optimized: ahead-of-time compiled, tree-shaken, and minified.

**Before building, prepare:**

1. **App name and identifier.** Set a unique ID such as `com.yourcompany.appname` (Android `applicationId`, iOS bundle identifier).
2. **Version** in `pubspec.yaml`:

```yaml
version: 1.2.0+7   # 1.2.0 is the version name, 7 is the build number
```

   The build number must increase with each store upload.
3. **App icon** and **splash screen.** The `flutter_launcher_icons` and `flutter_native_splash` packages generate them for every platform.
4. **Permissions** reviewed: remove ones you do not use, and ensure `INTERNET` is declared on Android.
5. **Remove debug leftovers:** test endpoints, verbose logging, secrets.

**Build commands:**

```bash
flutter build apk --release            # Android APK (direct install)
flutter build appbundle --release      # Android App Bundle (for Google Play)
flutter build ios --release            # iOS (then archive in Xcode)
flutter build ipa --release            # iOS archive for upload
flutter build web --release            # web files in build/web
flutter build windows --release        # also: macos, linux
```

**Useful flags:**

```bash
flutter build appbundle --obfuscate --split-debug-info=build/symbols
```

`--obfuscate` makes reverse engineering harder, and `--split-debug-info` writes symbol files that you **keep** to decode crash stack traces later.

**Environments:** use `--dart-define=API_URL=https://api.example.com` and read it with `const String.fromEnvironment('API_URL')` to switch between development and production configurations. Do not put real secrets in `--dart-define` values, because they are compiled into the app.

**Test the release build** on real devices, since behavior (like missing permissions or minification issues) can differ from debug.

---

## 17.2 Android signing and Play Store

**1. Create an upload keystore** (keep it safe and **back it up**):

```bash
keytool -genkey -v -keystore ~/upload-keystore.jks \
  -keyalg RSA -keysize 2048 -validity 10000 -alias upload
```

**2. Reference it** in `android/key.properties` (never commit this file or the keystore):

```
storePassword=<your-store-password>
keyPassword=<your-key-password>
keyAlias=upload
storeFile=/path/to/upload-keystore.jks
```

**3. Configure Gradle signing.** In `android/app/build.gradle(.kts)`, load `key.properties`, define a `signingConfigs { release { ... } }` block from those values, and set `buildTypes { release { signingConfig = signingConfigs.release } }`. The exact syntax depends on whether your project uses Groovy or Kotlin Gradle files, so follow the current "Build and release an Android app" page on docs.flutter.dev.

**4. Build the bundle:**

```bash
flutter build appbundle --release
```

Output: `build/app/outputs/bundle/release/app-release.aab`

**5. Publish on Google Play:**

1. Create a **Google Play Developer account** (one-time registration fee).
2. In **Play Console**, create the app and fill in the store listing: title, descriptions, screenshots, icon, feature graphic.
3. Complete the required **declarations**: content rating, privacy policy URL, data safety form, target audience.
4. Upload the `.aab` to an **Internal testing** track first, then Closed/Open testing, then Production.
5. Opt in to **Play App Signing**, where Google holds the final app signing key and you sign uploads with your upload key.
6. Submit for review. Early reviews may take days.

**Notes:**

- New personal developer accounts may have extra testing requirements before production release, so check Play Console's current rules.
- Keep **target SDK** levels up to date, because Google Play enforces minimums.
- **Losing your upload keystore** is a serious problem, so back it up securely.

---

## 17.3 iOS builds and App Store

Building for iOS **requires a Mac with Xcode**.

**Prerequisites:**

- An **Apple Developer Program** membership (paid, annual).
- A registered **Bundle ID** and an app record in **App Store Connect**.

**Steps:**

1. Open `ios/Runner.xcworkspace` in Xcode (always the workspace, not the project file).
2. In **Runner → Signing & Capabilities**, select your Team and enable automatic signing, which manages certificates and provisioning profiles for you.
3. Set the Bundle Identifier, display name, deployment target, and app icons (`Assets.xcassets`).
4. Confirm `Info.plist` has a usage description string for every permission you request.
5. Build the archive:

```bash
flutter build ipa --release
```

   or in Xcode: **Product → Archive**.
6. Upload using Xcode's **Organizer → Distribute App → App Store Connect**, or the Transporter app.
7. In **App Store Connect**: complete app info, screenshots for required device sizes, description, keywords, age rating, privacy details ("App Privacy" nutrition labels), and a privacy policy URL.
8. Distribute through **TestFlight** first for beta testing.
9. Submit for review, which is typically a day or two but can vary.

**Common rejection reasons:**

- Crashes or incomplete features.
- Missing or unclear permission explanations.
- Apps that are just a website wrapper with no extra value.
- No way to delete an account when sign-up exists in the app.
- Missing privacy information.
- Not using Apple's in-app purchase system where required for digital goods.

Read the **App Store Review Guidelines** before submitting.

---

## 17.4 CI/CD for Flutter

**Continuous Integration (CI)** automatically runs checks on every push. **Continuous Delivery (CD)** automatically builds and distributes releases. This catches problems early and removes manual, error-prone steps.

**A basic GitHub Actions workflow** (`.github/workflows/ci.yml`):

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: subosito/flutter-action@v2
        with:
          channel: stable
          cache: true

      - run: flutter pub get
      - run: dart format --output=none --set-exit-if-changed .
      - run: flutter analyze
      - run: flutter test --coverage

  build-android:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: subosito/flutter-action@v2
        with:
          channel: stable
      - run: flutter pub get
      - run: flutter build apk --release
      - uses: actions/upload-artifact@v4
        with:
          name: app-release
          path: build/app/outputs/flutter-apk/app-release.apk
```

**What a good pipeline does:**

1. **Format check** (`dart format`)
2. **Static analysis** (`flutter analyze`)
3. **Tests** (`flutter test`)
4. **Build** for each target platform
5. **Distribute** to testers or stores

**Handling secrets:** store keystores, passwords, and API keys in the CI system's **encrypted secrets**, never in the repository. A common trick is to store the keystore as a base64 secret and decode it during the job.

**iOS builds** need a **macOS runner** (`runs-on: macos-latest`), with signing certificates provided via secrets.

**Automation tools:**

| Tool | Purpose |
|---|---|
| **Fastlane** | Scripts build, signing, screenshots, and uploads to both stores |
| **Codemagic** | CI/CD service built specifically for Flutter |
| **GitHub Actions / GitLab CI / Bitrise** | General CI systems with Flutter support |
| **Firebase App Distribution** | Send test builds to testers quickly |

**Versioning tip:** automatically increment the build number from the CI run number so uploads never collide.

---

[Previous](./[16]-Debugging-And-Performance.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[18]-Next-Steps-With-Flutter.md)