[Previous](./[13]-Native-Modules-And-The-New-Architecture.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[15]-Testing-React-Native-Apps.md)

*Quality And Delivery*

# Lesson 14 - Debugging React Native Apps

Bugs are normal. Knowing your debugging tools lets you find causes quickly instead of guessing.

## 14.1 Dev menu and logs

**The developer menu**

Open it while your app is running in development:

- **iOS Simulator:** press `Cmd + D`
- **Android Emulator:** press `Cmd + M` (macOS) or `Ctrl + M` (Windows/Linux)
- **Real device:** shake the phone
- **Expo terminal:** press `m` to toggle it

Useful entries: **Reload**, **Open JS Debugger**, **Toggle Element Inspector**, **Show Perf Monitor**, and **Toggle Fast Refresh**.

**Logging**

`console.log` output appears in the terminal running Metro.

```jsx
console.log('value:', value);
console.warn('Something looks off');   // yellow warning
console.error('Something failed');     // red error
console.table(users);                  // table format
console.time('load'); /* work */ console.timeEnd('load');
```

Tips for useful logs:

- Log **objects** rather than concatenated strings: `console.log({ userId, status })`.
- Remove noisy logs before release. A babel plugin can strip them in production builds.
- Remember that logging inside render functions runs on every render.

**LogBox**

During development, errors show as a red full-screen error and warnings as small yellow badges. Read the **message and the component stack**, since they usually point to the cause. You can ignore a known warning with `LogBox.ignoreLogs([...])`, but fix real warnings when possible.

**Native logs**

For crashes that never reach JavaScript, read native logs:

```bash
npx react-native log-ios       # iOS
adb logcat                      # Android (or use Logcat in Android Studio)
```

For iOS, you can also view logs in Xcode's console.

---

## 14.2 React Native DevTools

**React Native DevTools** is the official debugger, based on Chrome DevTools. It works with the Hermes engine and is available in React Native 0.76 and newer.

Open it by pressing **`j`** in the terminal where Metro is running, or choose **Open DevTools** from the dev menu.

What it offers:

| Panel | Use |
|---|---|
| **Console** | See logs, run JavaScript expressions in your app |
| **Sources** | Set breakpoints, step through code, inspect variables |
| **Components** | Inspect the React tree, view and edit props and state |
| **Profiler** | Record renders to find slow components |
| **Memory** | Take heap snapshots to find leaks |

**Using breakpoints**

1. Open the **Sources** tab and find your file.
2. Click a line number to add a breakpoint.
3. Trigger the code in the app. Execution pauses on that line.
4. Inspect variables and use step over, step into, and continue.

You can also place the `debugger;` statement in your code to pause there when DevTools is attached.

Older tools you may see in tutorials: Flipper (no longer the default), the Chrome remote debugger (removed), and Reactotron (still available as a separate tool). Prefer React Native DevTools for new projects.

---

## 14.3 Common errors

| Error message | Likely cause | Fix |
|---|---|---|
| `Text strings must be rendered within a <Text> component` | Raw text or a stray `{" "}` or `0` inside a `View` | Wrap text in `<Text>`. With `&&`, convert numbers to booleans: `{count > 0 && ...}` |
| `Unable to resolve module ...` | Missing package, wrong import path, or stale cache | Install the package, fix the path, run `npx expo start -c` |
| `Invariant Violation: "main" has not been registered` | The app entry point is not registered correctly | Check the entry file and your `package.json` `main` field |
| `Cannot read property 'x' of undefined` | Data not loaded yet | Use optional chaining (`data?.x`) and loading states |
| `Each child in a list should have a unique "key" prop` | Missing `key` or `keyExtractor` | Provide stable unique keys |
| `Network request failed` | Wrong URL, no HTTPS, device cannot reach server | Check the URL, use `10.0.2.2` on Android emulators, use HTTPS |
| `VirtualizedLists should never be nested inside plain ScrollViews` | A `FlatList` inside a `ScrollView` | Use `ListHeaderComponent` or restructure the screen |
| Version mismatch errors with Expo | Package versions do not match your SDK | Run `npx expo install --fix` |
| `Invalid hook call` | Hooks used incorrectly or duplicate React versions | Follow hook rules, check for multiple React installs |

**General recovery steps**

```bash
npx expo start -c            # clear the Metro cache
rm -rf node_modules && npm install   # reinstall dependencies
npx expo-doctor              # check your project for problems
```

If native errors appear after adding a library, rebuild the native app (for example `npx expo run:ios`) because a reload cannot update native code.

**Debugging approach**

1. **Read** the full error message and stack trace.
2. **Reproduce** the problem reliably.
3. **Isolate** it by removing code until the problem disappears.
4. **Inspect** values with the console or breakpoints.
5. **Fix**, then confirm the fix and check nothing else broke.

---

## 14.4 Network inspection

When an API call misbehaves, inspect the actual request and response.

**In React Native DevTools**

Recent versions include a **Network** panel that shows `fetch` and `XMLHttpRequest` calls, with URLs, status codes, headers, and response bodies. Availability depends on your React Native version, so check the release notes of the version you use.

**Quick checks with logging**

```jsx
const res = await fetch(url);
console.log('status', res.status);
console.log('headers', [...res.headers.entries()]);
console.log('body', await res.text());
```

With Axios, you can log requests and responses using interceptors:

```jsx
api.interceptors.request.use((config) => {
  console.log('REQUEST', config.method, config.url, config.data);
  return config;
});

api.interceptors.response.use(
  (res) => {
    console.log('RESPONSE', res.status, res.config.url);
    return res;
  },
  (err) => {
    console.log('ERROR', err.response?.status, err.config?.url, err.message);
    return Promise.reject(err);
  }
);
```

**External proxy tools**

Tools such as **Proxyman**, **Charles Proxy**, or **mitmproxy** sit between your device and the internet and show all traffic, including from native libraries. You configure your device or simulator to use the proxy and may need to install the tool's certificate to read HTTPS.

**Checklist for network problems**

- Is the **URL** correct, including the protocol and port?
- Is the device **online** and able to reach the server?
- Is the request **HTTPS** (or is cleartext allowed in development)?
- Are the **headers** and **body** correct, such as `Content-Type` and the `Authorization` token?
- Does the **server** respond correctly when tested with another tool (curl or Postman)?
- Is a **CORS** policy involved? It does not apply to native apps, but it does if you also run on web.

---

[Previous](./[13]-Native-Modules-And-The-New-Architecture.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[15]-Testing-React-Native-Apps.md)