[Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[2]-React-Foundations-For-Native.md)

*Basics*

# Lesson 1 - What Is React Native

## 1.1 How React Native works

React Native lets you write your app in JavaScript (or TypeScript) using React, while the user sees **real native UI**. Your code does not run inside a browser. Instead, it runs in a JavaScript engine called **Hermes** that is bundled inside your app.

When your components render, React Native translates them into native views through its renderer (called **Fabric** in the New Architecture). A `<View>` becomes a real iOS `UIView` or an Android `ViewGroup`.

```
Your JS/TS code (React components)
        ↓  runs on Hermes (JS engine)
React Native renderer (Fabric)
        ↓  creates and updates
Native views (UIView on iOS, android.view.View on Android)
```

Key ideas:

- **JavaScript thread** — runs your React code, business logic, and state.
- **UI thread** — the native main thread that draws views and handles touches.
- **Metro** — the bundler that packages your JavaScript during development and release.
- **Fast Refresh** — edits appear on your device almost instantly while keeping component state.

Since version 0.76, the **New Architecture** (JSI, Fabric, TurboModules) is enabled by default. You will learn more about it in [Lesson 13](./[13]-Native-Modules-And-The-New-Architecture.md).

---

## 1.2 Native components vs web views

Many "hybrid" frameworks (such as Cordova or Ionic) show your app inside a **web view**: a hidden browser. React Native does not do this.

| Approach | What you see on screen | Feel |
|---|---|---|
| Web view (hybrid) | HTML/CSS rendered by a browser | Often feels like a website |
| React Native | Real platform widgets | Feels like a native app |

Core components map to platform widgets:

| React Native | iOS | Android | Web (for comparison) |
|---|---|---|---|
| `<View>` | `UIView` | `ViewGroup` | `<div>` |
| `<Text>` | `UILabel` / `UITextView` | `TextView` | `<p>`, `<span>` |
| `<Image>` | `UIImageView` | `ImageView` | `<img>` |
| `<TextInput>` | `UITextField` | `EditText` | `<input>` |
| `<ScrollView>` | `UIScrollView` | `ScrollView` | scrolling `<div>` |

Because the widgets are native, you automatically get native scrolling physics, accessibility features, text input behavior, and platform look and feel.

---

## 1.3 Expo vs React Native CLI

There are two main ways to start a React Native project:

**Expo (recommended for most people)**

- A framework and toolset built around React Native.
- Includes a large library of ready-made native features (camera, location, notifications, and more).
- Lets you test instantly on a phone with the **Expo Go** app.
- Provides **EAS** (Expo Application Services) for cloud builds and app store submission.
- Recommended by the React Native team for new apps.

**React Native CLI (a "bare" project)**

- You manage the `ios/` and `android/` native folders yourself.
- Needs Xcode (macOS only, for iOS) and Android Studio installed.
- Gives full control, but requires more setup and native knowledge.

Expo is not limited to simple apps. With **development builds** and **config plugins**, you can use almost any native library while keeping Expo's workflow. This Topic uses Expo for its examples.

---

## 1.4 Setting up and running your first app

**Requirements**

- Node.js (an LTS version) and npm
- A code editor such as VS Code
- A phone with **Expo Go** installed, or an iOS Simulator (macOS) / Android Emulator

**Create a project**

```bash
npx create-expo-app@latest MyFirstApp
cd MyFirstApp
npx expo start
```

The terminal shows a QR code and a few shortcut keys:

- `i` — open in the iOS Simulator
- `a` — open in the Android Emulator
- `r` — reload the app
- `m` — toggle the developer menu
- `j` — open the debugger

To run on a real phone, scan the QR code with the Camera app (iOS) or the Expo Go app (Android). Your phone and computer should be on the same network.

**A minimal app**

```jsx
import { View, Text, StyleSheet } from 'react-native';

export default function App() {
  return (
    <View style={styles.container}>
      <Text style={styles.title}>Hello, React Native!</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
  title: {
    fontSize: 24,
    fontWeight: 'bold',
  },
});
```

Save the file and the change appears on your device immediately thanks to Fast Refresh.

---

[Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[2]-React-Foundations-For-Native.md)