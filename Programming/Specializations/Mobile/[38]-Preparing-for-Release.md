[Previous](./[37]-App-Icons-and-Splash-Screens.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[39]-Publishing-to-App-Stores.md)

*Publishing & Distribution*

# Lesson 38 - Preparing for Release (Signing, Build Variants)

## 38.1 Why Signing Matters

Both platforms require every app to be **cryptographically signed** before it can be installed on a device (outside of local development). Signing proves the app came from a verified developer and hasn't been tampered with since it was built. Without a valid signature, neither iOS nor Android will install the app.

---

## 38.2 iOS Code Signing

iOS signing involves a few interlocking pieces managed through Apple's Developer portal:

- **Certificates** — identify you as a developer (a development certificate for testing, a distribution certificate for release).
- **App ID** — a unique identifier for your app (its bundle identifier, e.g. `com.example.myapp`).
- **Provisioning Profiles** — bind a certificate, an App ID, and (for development) a list of allowed test devices together, authorizing the app to run.

Xcode can manage most of this automatically ("Automatically manage signing"), but larger teams often manage certificates and profiles manually for tighter control over CI/CD pipelines.

---

## 38.3 Android Signing

Android apps are signed with a **keystore** — a file containing a private key you generate and must keep secure (losing it means you can never update your app under the same identity again).

```bash
keytool -genkeypair -v -keystore release-key.jks -keyalg RSA -keysize 2048 -validity 10000 -alias my-app
```

Google Play also offers **Play App Signing**, where Google manages your app-signing key on your behalf, letting you use an "upload key" locally that's more easily replaced if compromised.

---

## 38.4 Build Variants

Most apps maintain multiple **build variants** (also called build flavors/schemes) so the same codebase can target different environments without code changes:

- **Debug** — includes debugging symbols, verbose logging, and points at a development/staging API.
- **Release** — optimized, stripped of debug info, and points at the production API.

```kotlin
// Android build.gradle.kts
buildTypes {
    debug { buildConfigField("String", "API_URL", "\"https://staging.api.com\"") }
    release { buildConfigField("String", "API_URL", "\"https://api.com\"") }
}
```

iOS achieves the same result with **Schemes** and **Configurations** (typically `Debug` and `Release`) in Xcode.

---

## 38.5 Pre-Release Checklist

Before submitting a build, teams typically verify: version/build numbers have been bumped, all debug logging and test endpoints are disabled, required permission usage strings are present, and the release build has been smoke-tested on a real device — since optimizations in release builds occasionally surface bugs that never appear in debug.

[Previous](./[37]-App-Icons-and-Splash-Screens.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[39]-Publishing-to-App-Stores.md)
