[Previous](./[11]-Animations-And-Gestures.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[13]-Native-Modules-And-The-New-Architecture.md)

*Device Features*

# Lesson 12 - Platform-Specific Code

Most of your code is shared, but sometimes iOS and Android need different behavior, design, or libraries. React Native gives you several ways to branch cleanly.

## 12.1 Platform module

The `Platform` module tells you which system the app is running on.

```jsx
import { Platform } from 'react-native';

console.log(Platform.OS);      // 'ios' or 'android' (also 'web' if you target the web)
console.log(Platform.Version); // iOS: '17.4' (string), Android: 34 (API level, number)

if (Platform.OS === 'ios') {
  // iOS-only logic
}
```

**`Platform.select`** returns the value for the current platform. It is handy inside styles.

```jsx
import { StyleSheet, Platform } from 'react-native';

const styles = StyleSheet.create({
  container: {
    paddingTop: Platform.select({ ios: 20, android: 0, default: 0 }),
    ...Platform.select({
      ios: {
        shadowColor: '#000',
        shadowOpacity: 0.2,
        shadowRadius: 4,
        shadowOffset: { width: 0, height: 2 },
      },
      android: {
        elevation: 4,
      },
    }),
  },
});
```

Other helpful checks:

- `Platform.isPad` (iOS) tells you whether the device is an iPad.
- `Platform.isTV` tells you whether it is a TV platform.
- Check `Platform.Version` before using features that exist only in newer OS versions.

---

## 12.2 Platform file extensions

When whole components differ, put each version in its own file using the platform extension. React Native's bundler (Metro) picks the right file automatically.

```
components/
├── DatePicker.ios.js
├── DatePicker.android.js
└── DatePicker.js        # optional fallback (for web or shared code)
```

```jsx
// Import without any extension
import DatePicker from './components/DatePicker';
```

On iOS, Metro loads `DatePicker.ios.js`. On Android, it loads `DatePicker.android.js`.

The same works for TypeScript (`.ios.tsx`, `.android.tsx`) and for any module, not just components. There is also a `.native.js` extension, which is used for both iOS and Android but not for web.

Use platform files when:

- The two implementations share little code.
- The component uses a platform-only library.
- Putting many `Platform.OS` checks in one file makes it hard to read.

Keep the **same exports and props** in each version so the rest of your app does not need to know which one is used.

---

## 12.3 Handling iOS and Android differences

Common areas where behavior differs, and how to handle them:

| Area | Difference | Approach |
|---|---|---|
| Shadows | iOS `shadow*` props vs Android `elevation` | `Platform.select` or `boxShadow` |
| Back button | Android has a hardware/system back button | Use the `BackHandler` API |
| Permissions | Different dialogs and flows | Use a library that unifies them |
| Keyboard | Different resize behavior | `KeyboardAvoidingView` with a platform-based `behavior` |
| Fonts | Different system fonts | Bundle a custom font for a consistent look |
| Navigation feel | Swipe-back on iOS | Use the native stack, which follows platform conventions |
| Alerts and pickers | Different visuals | Use cross-platform libraries |
| Status bar | Different defaults and edge-to-edge rules | Configure explicitly |

**Handle the Android back button**

```jsx
import { BackHandler } from 'react-native';
import { useEffect } from 'react';

useEffect(() => {
  const sub = BackHandler.addEventListener('hardwareBackPress', () => {
    // return true to stop the default behavior (like closing the app)
    console.log('Back pressed');
    return true;
  });
  return () => sub.remove();
}, []);
```

**Follow each platform's conventions**

- Do not copy iOS design onto Android or the reverse. Users expect each system's patterns.
- Prefer platform-native components when available, such as the native stack and native alerts.
- Test on **both platforms** regularly. A feature that works on one may break on the other.

---

## 12.4 Safe area and status bar

**Status bar**

The status bar is the strip at the top showing time, battery, and signal. In Expo, use `expo-status-bar`.

```jsx
import { StatusBar } from 'expo-status-bar';

export default function App() {
  return (
    <>
      <StatusBar style="dark" />  {/* dark icons for light backgrounds */}
      {/* rest of app */}
    </>
  );
}
```

Options for `style`: `auto`, `light`, `dark`, and `inverted`.

**Safe area**

Content should not sit under the notch, dynamic island, status bar, or home indicator. Use `react-native-safe-area-context` (see [Lesson 4](./[4]-Styling-With-StyleSheet.md)).

```jsx
import { useSafeAreaInsets } from 'react-native-safe-area-context';

function Screen() {
  const insets = useSafeAreaInsets();

  return (
    <View
      style={{
        flex: 1,
        paddingTop: insets.top,
        paddingBottom: insets.bottom,
        paddingLeft: insets.left,
        paddingRight: insets.right,
      }}
    >
      <Text>Content stays inside the safe area</Text>
    </View>
  );
}
```

**Android edge-to-edge**

Recent Android versions and recent React Native and Expo versions draw the app behind the system bars ("edge-to-edge"). This means you should handle insets on Android too. Using the safe area library consistently solves most issues. Check your Expo SDK documentation for the current default behavior and any configuration options.

Tips:

- Test with different devices, including ones with notches, rounded corners, and gesture navigation bars.
- Test in landscape, where the insets move to the sides.
- Navigation headers and tab bars already respect safe areas, so apply insets mainly to your own full-screen layouts.

---

[Previous](./[11]-Animations-And-Gestures.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[13]-Native-Modules-And-The-New-Architecture.md)