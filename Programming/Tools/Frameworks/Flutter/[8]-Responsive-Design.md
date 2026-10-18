[Previous](./[7]-Forms-And-User-Input.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[9]-Animations.md)

*Building The UI*

# Lesson 8 - Responsive Design

Your app may run on a small phone, a tablet, a browser window, or a desktop. This lesson shows how to measure the available space and adapt the layout to it.

---

## 8.1 MediaQuery and LayoutBuilder

**`MediaQuery`** describes the whole screen or window:

```dart
final media = MediaQuery.of(context);
final width = media.size.width;
final height = media.size.height;
final padding = media.padding;            // notches, status bar
final textScale = media.textScaler;       // user's text size setting
final isDark = media.platformBrightness == Brightness.dark;
```

Newer Flutter versions also offer targeted accessors such as `MediaQuery.sizeOf(context)` and `MediaQuery.orientationOf(context)`. They make the widget rebuild only when that specific value changes, which is better for performance.

**`LayoutBuilder`** tells you the space the *parent gave this widget*, which is often more useful than the screen size, because a widget may sit inside a narrow panel:

```dart
LayoutBuilder(
  builder: (context, constraints) {
    if (constraints.maxWidth > 600) {
      return const WideLayout();
    }
    return const NarrowLayout();
  },
)
```

**When to use which:**

| Tool | Use it for |
|---|---|
| `MediaQuery` | Decisions about the whole screen: orientation, system insets, text scale |
| `LayoutBuilder` | Making a reusable component adapt to the space it is given |

Other helpers: `AspectRatio`, `FractionallySizedBox`, `Flexible`/`Expanded`, `Wrap`, and `FittedBox` (scales a child to fit).

---

## 8.2 Breakpoints

A **breakpoint** is a width at which your layout changes shape. Define them in one place so every screen agrees:

```dart
class Breakpoints {
  static const double compact = 600;   // phones
  static const double medium = 840;    // small tablets, foldables
  // anything wider is "expanded"
}

enum ScreenSize { compact, medium, expanded }

ScreenSize screenSizeOf(double width) {
  if (width < Breakpoints.compact) return ScreenSize.compact;
  if (width < Breakpoints.medium) return ScreenSize.medium;
  return ScreenSize.expanded;
}
```

These values follow the Material window size classes (compact, medium, expanded). Use them to switch navigation style:

```dart
class AdaptiveScaffold extends StatelessWidget {
  const AdaptiveScaffold({super.key, required this.body});
  final Widget body;

  @override
  Widget build(BuildContext context) {
    final width = MediaQuery.sizeOf(context).width;
    final useRail = width >= Breakpoints.compact;

    return Scaffold(
      body: Row(
        children: [
          if (useRail)
            NavigationRail(
              selectedIndex: 0,
              onDestinationSelected: (_) {},
              labelType: NavigationRailLabelType.all,
              destinations: const [
                NavigationRailDestination(
                    icon: Icon(Icons.home), label: Text('Home')),
                NavigationRailDestination(
                    icon: Icon(Icons.settings), label: Text('Settings')),
              ],
            ),
          Expanded(child: body),
        ],
      ),
      bottomNavigationBar: useRail
          ? null
          : NavigationBar(
              selectedIndex: 0,
              onDestinationSelected: (_) {},
              destinations: const [
                NavigationDestination(icon: Icon(Icons.home), label: 'Home'),
                NavigationDestination(
                    icon: Icon(Icons.settings), label: 'Settings'),
              ],
            ),
    );
  }
}
```

A common pattern: a bottom bar on phones, a side **navigation rail** on tablets, and a wider drawer on desktop.

Avoid checking the **device type** (phone vs tablet). Check the **available size**, because windows can be resized and split on modern devices.

---

## 8.3 Orientation changes

Read the orientation from `MediaQuery`, or use `OrientationBuilder`:

```dart
OrientationBuilder(
  builder: (context, orientation) {
    return GridView.count(
      crossAxisCount: orientation == Orientation.portrait ? 2 : 4,
      children: List.generate(
        8,
        (i) => Card(child: Center(child: Text('Item $i'))),
      ),
    );
  },
)
```

**Locking orientation** (for example a game or a camera screen):

```dart
import 'package:flutter/services.dart';

Future<void> main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await SystemChrome.setPreferredOrientations([
    DeviceOrientation.portraitUp,
  ]);
  runApp(const MyApp());
}
```

`WidgetsFlutterBinding.ensureInitialized()` is required before calling platform services ahead of `runApp`.

Tips for rotation:

- Make screens **scrollable** so landscape does not overflow.
- State held in a `State` object survives rotation, since the widget is not destroyed.
- Respect