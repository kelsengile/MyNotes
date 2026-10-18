[Previous](./[16]-Performance-Optimization.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[18]-Next-Steps-With-React-Native.md)

*Quality And Delivery*

# Lesson 17 - Building And Publishing

Once your app works, you need to package it into a signed file and submit it to the **Apple App Store** and **Google Play**. Requirements and fees change over time, so confirm the current details on the official store pages.

## 17.1 Release builds and EAS

A **development build** is for debugging. A **release build** is optimized (minified JavaScript, Hermes bytecode, no dev tools) and is what users install.

**EAS (Expo Application Services)** builds your app in the cloud, so you can create an iOS build without owning a Mac.

**Setup**

```bash
npm install -g eas-cli
eas login
eas build:configure
```

This creates an `eas.json` file with build profiles:

```json
{
  "cli": { "version": ">= 12.0.0" },
  "build": {
    "development": {
      "developmentClient": true,
      "distribution": "internal"
    },
    "preview": {
      "distribution": "internal"
    },
    "production": {
      "autoIncrement": true
    }
  },
  "submit": {
    "production": {}
  }
}
```

| Profile | Purpose |
|---|---|
| `development` | Includes the dev client, for daily development with native code |
| `preview` | Internal testing builds you can share with testers |
| `production` | The build you submit to the stores |

**Build commands**

```bash
eas build --profile preview --platform android      # .apk for easy sharing
eas build --profile production --platform ios        # .ipa for the App Store
eas build --profile production --platform android    # .aab for Google Play
eas build --profile production --platform all
```

Before building, set your **app identity** in `app.json`:

```json
{
  "expo": {
    "name": "My App",
    "slug": "my-app",
    "version": "1.0.0",
    "ios": { "bundleIdentifier": "com.mycompany.myapp", "buildNumber": "1" },
    "android": { "package": "com.mycompany.myapp", "versionCode": 1 },
    "icon": "./assets/icon.png"
  }
}
```

- The **bundle identifier / package name** is a permanent unique ID. Choose it carefully.
- **`version`** is shown to users. **`buildNumber` / `versionCode`** must increase with every upload to a store.
- You can also build locally with `eas build --local` or `npx expo run:ios --configuration Release`.

---

## 17.2 App signing

Stores require apps to be **digitally signed**. This proves the app comes from you and has not been tampered with.

**iOS**

- Needs an **Apple Developer Program** membership (paid yearly).
- Uses a **distribution certificate** and a **provisioning profile**, tied to your bundle identifier.
- Push notifications need an additional **APNs key**.

**Android**

- Uses a **keystore** file with a private key.
- With **Play App Signing**, Google holds the final signing key, and you sign uploads with an **upload key**.
- **If you lose your upload key, you can lose the ability to update your app** (although Google offers a reset process for upload keys).

**Let EAS manage credentials**

```bash
eas credentials
```

EAS can generate, store, and reuse your certificates, profiles, and keystores. During `eas build`, it asks for permission and does the setup automatically.

Security rules:

- **Never commit** keystores, `.p8` keys, certificates, or passwords to Git.
- **Back up** your Android keystore and its passwords in a secure place (such as a password manager) if you manage it yourself.
- Use environment variables or EAS secrets for sensitive values:

```bash
eas secret:create --name API_KEY --value "..."
```

(The exact secrets command may differ in newer EAS CLI versions, so check `eas --help`.)

---

## 17.3 App Store and Google Play submission

**Accounts**

| Store | Account | Cost (check current pricing) |
|---|---|---|
| Apple App Store | Apple Developer Program | Yearly fee |
| Google Play | Google Play Console | One-time fee |

**Prepare your store listing**

- **App name, subtitle/short description, and full description**
- **App icon** (iOS requires 1024x1024 with no transparency)
- **Screenshots** for required device sizes
- **Privacy policy URL**
- **Category, age rating, and contact information**
- **Data safety / privacy details** describing what data you collect

**Submit with EAS**

```bash
eas submit --platform ios
eas submit --platform android
```

You can also build and submit in one step:

```bash
eas build --platform all --auto-submit
```

**Typical process**

1. Upload the build (iOS appears in **App Store Connect** / TestFlight, Android in the **Play Console**).
2. **Test first.** Use TestFlight (iOS) or an internal/closed testing track (Android). New personal Google Play accounts may have testing requirements before production release, so read the current rules.
3. Complete the store listing and questionnaires.
4. Submit for **review**. Apple review commonly takes a day or two. Google review time varies.
5. Release manually or automatically after approval.

**Common reasons for rejection**

- Crashes or obvious bugs.
- Missing or unclear permission explanations.
- No privacy policy, or inaccurate data declarations.
- Broken login for reviewers. Provide a **demo account** in the review notes.
- Placeholder content or an app that is just a website wrapper.
- Using non-standard payment methods for digital goods. Both stores have in-app purchase rules.

**Before every release**

- Increase the version and build number.
- Test the release build on real devices.
- Write release notes.
- Check that production API URLs and keys are used, not development ones.

---

## 17.4 Over-the-air updates

**Over-the-air (OTA) updates** let you ship JavaScript and asset changes directly to users without a new store release. Expo provides this through **`expo-updates`** and **EAS Update**.

```bash
npx expo install expo-updates
eas update:configure
eas update --channel production --message "Fix login bug"
```

On launch, the app checks for a compatible update, downloads it, and applies it (by default on the next app start).

**What OTA can update**

- JavaScript code, styles, and assets (images, fonts).

**What OTA cannot update**

- Native code: new native libraries, changed permissions, or new native modules.
- Anything that needs a new binary requires a **new store build**.

Updates are matched to compatible builds using a **runtime version**, so an update never reaches a build that cannot run it.

```json
{
  "expo": {
    "runtimeVersion": { "policy": "appVersion" },
    "updates": { "url": "https://u.expo.dev/your-project-id" }
  }
}
```

**Rules and best practices**

- Follow store policies. Updates should not change the app's primary purpose or bypass review.
- Use **channels** (for example `preview` and `production`) to test updates before releasing them widely.
- **Roll back** quickly if an update is faulty by publishing a fixed update or republishing a previous one.
- Treat OTA updates as a **fast bug-fix path**, not a replacement for testing.
- You can check for updates in code:

```jsx
import * as Updates from 'expo-updates';

async function checkForUpdate() {
  try {
    const result = await Updates.checkForUpdateAsync();
    if (result.isAvailable) {
      await Updates.fetchUpdateAsync();
      await Updates.reloadAsync();
    }
  } catch (e) {
    console.log('Update check failed', e);
  }
}
```

---

[Previous](./[16]-Performance-Optimization.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[18]-Next-Steps-With-React-Native.md)