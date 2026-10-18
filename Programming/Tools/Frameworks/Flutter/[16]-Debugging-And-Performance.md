[Previous](./[15]-Testing-Flutter-Apps.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[17]-Building-And-Publishing.md)

*Quality And Delivery*

# Lesson 16 - Debugging And Performance

Working apps are not enough. They must also be **correct** and **smooth**. This lesson covers Flutter's debugging tools, how to find rebuild problems, and how to fix jank (dropped frames).

---

## 16.1 Flutter DevTools

**DevTools** is a browser-based suite of debugging and profiling tools. Open it:

- From VS Code: Command Palette → *Flutter: Open DevTools*
- From Android Studio: the DevTools icon in the Run panel
- From the terminal: run `flutter run`, then open the link it prints, or use `dart devtools`

**Main tabs:**

| Tab | Use it to |
|---|---|
| **Inspector** | Explore the widget tree and layout |
| **Performance** | See frame times and find janky frames |
| **CPU Profiler** | Find slow Dart functions |
| **Memory** | Track allocations and find leaks |
| **Network** | Inspect HTTP requests and responses |
| **Logging** | View logs and framework events |
| **Debugger** | Set breakpoints and step through code |
| **App Size** | Analyze what makes the build large |

**Everyday debugging:**

```dart
import 'package:flutter/foundation.dart';

debugPrint('value = $value');          // preferred over print in Flutter
assert(count >= 0, 'count must not be negative'); // debug mode only

if (kDebugMode) {
  // code that runs only in debug builds
}
```

- **Breakpoints:** click the gutter in your editor and run in debug mode, then step with Over/Into/Out and inspect variables.
- **Read stack traces from the top:** the first line that points to *your* file is usually the cause.
- **Logging:** `dart:developer`'s `log()` provides names and levels visible in DevTools.

**Crash reporting in production** is a must, since you cannot see user devices. Use Firebase Crashlytics or Sentry, and hook into Flutter's global error handlers:

```dart
FlutterError.onError = (details) {
  // send details to your crash reporter
};
PlatformDispatcher.instance.onError = (error, stack) {
  // report error and stack
  return true;
};
```

---

## 16.2 The widget inspector

The **widget inspector** lets you click on the running app and see which widget you tapped, its properties, and the surrounding tree. Turn it on with the "Select Widget Mode" button in DevTools or your editor.

**What it helps with:**

- **Layout errors:** select a widget and open the **Layout Explorer** to see its constraints, size, flex values, and alignment.
- **Overflow errors:** find which Row/Column is overflowing and what constraints it received.
- **"Where is this widget defined?"** Selecting a widget jumps to the source line.
- **Understanding structure:** see exactly how widgets are nested, including widgets inside packages.

**Debug flags** that draw helpful overlays (toggle them in DevTools, or in code):

```dart
import 'package:flutter/rendering.dart';

void main() {
  debugPaintSizeEnabled = true;       // outlines every widget box
  runApp(const MyApp());
}
```

Related options:

- `MaterialApp(debugShowCheckedModeBanner: false)` hides the "DEBUG" banner.
- `showPerformanceOverlay: true` on `MaterialApp` draws live frame-time graphs.
- `debugDumpApp()` prints the widget tree as text, and `debugDumpRenderTree()` prints the render tree.

**Reading common errors:**

| Error message | Typical cause | Fix |
|---|---|---|
| "RenderFlex overflowed" | Content larger than its space | `Expanded`, `Flexible`, or make it scrollable |
| "Vertical viewport was given unbounded height" | `ListView` inside `Column` | Wrap the `ListView` in `Expanded` |
| "setState() called after dispose()" | Async work finishing after the widget left | Check `if (!mounted) return;` before `setState` |
| "A RenderFlex... unbounded width" | `Row` child with infinite width | Constrain with `Expanded` or `SizedBox` |

---

## 16.3 Reducing rebuilds

Rebuilding widgets is cheap, but **needless** rebuilds of big subtrees add up. Aim to rebuild only what changed.

**1. Use `const`** wherever possible. A `const` widget is created once and Flutter skips rebuilding it:

```dart
const Padding(
  padding: EdgeInsets.all(16),
  child: Text('Static text'),
)
```

**2. Push state down.** Put `setState` in the smallest widget that needs it, not at the top of the screen:

```dart
// Bad: whole page rebuilds whenever the counter changes
// Good: only the small Counter widget rebuilds
class Page extends StatelessWidget {
  const Page({super.key});
  @override
  Widget build(BuildContext context) {
    return const Column(children: [HeavyHeader(), Counter()]);
  }
}
```

**3. Extract widgets into classes** rather than helper methods like `Widget _buildHeader()`. A separate `const` widget class can skip rebuilds, whereas a method always reruns with its parent.

**4. Watch narrowly in state management:**

```dart
// Riverpod: rebuild only when the name changes
final name = ref.watch(userProvider.select((u) => u.name));

// Provider
context.select<CartModel, int>((cart) => cart.count);
```

**5. Use builders and lazy lists.** `ListView.builder` builds only visible rows. Give each item a stable `key`.

**6. Keep `build` cheap:**

- No heavy computation (sorting, parsing JSON) inside `build`. Do it once in `initState`, a notifier, or a repository.
- No network calls or controller creation in `build`.
- Cache expensive results.

**7. Move heavy work off the UI thread** with `Isolate.run` or `compute`:

```dart
final parsed = await Isolate.run(() => parseHugeJson(rawText));
```

(`Isolate` needs `import 'dart:isolate';`.)

**Find rebuilds:** in DevTools turn on **Track widget rebuilds** to see counts, and in code use `debugPrint` inside `build` temporarily.

---

## 16.4 Profiling jank

**Jank** is a visible stutter caused by frames taking too long. The target is about 16 ms per frame at 60 Hz, and about 8 ms at 120 Hz. Each frame has a **UI thread** part (your Dart `build` and layout) and a **raster thread** part (drawing).

**Golden rule: profile in profile mode on a real device.** Debug mode is deliberately slow, and emulators are unrepresentative:

```bash
flutter run --profile
```

**Workflow:**

1. Open DevTools → **Performance**.
2. Interact with the slow screen, and watch the frame chart. Red bars are janky frames.
3. Check which thread is slow:

| Slow thread | Likely cause | Remedies |
|---|---|---|
| **UI** | Heavy `build`, big layouts, expensive Dart work | Reduce rebuilds, avoid work in `build`, use isolates, lazy lists |
| **Raster** | Complex drawing: blurs, shadows, clips, large images, `saveLayer` | Simplify effects, `RepaintBoundary`, resize images |

4. Use the **CPU Profiler** to find the specific slow functions.
5. Change one thing, then **measure again**.

**Common fixes:**

- Resize large images: use `Image.asset(..., cacheWidth: 400)` or provide appropriately sized assets.
- Avoid `Opacity` on large animated areas, so prefer `FadeTransition` or `AnimatedOpacity`.
- Avoid unneeded `ClipRRect`, `BackdropFilter`, and big shadows.
- Add `RepaintBoundary` around parts that animate often.
- Use `ListView.builder` with `itemExtent` or `prototypeItem` for long uniform lists.
- Reduce first-run **shader jank** by using the Impeller renderer, which is the default on modern iOS and Android.

**Memory:** use the **Memory** tab to look for steadily growing usage. Common leaks come from forgetting to `dispose()` controllers, cancel stream subscriptions, or remove listeners.

**App size:** `flutter build apk --analyze-size` shows what contributes to download size.

---

[Previous](./[15]-Testing-Flutter-Apps.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[17]-Building-And-Publishing.md)