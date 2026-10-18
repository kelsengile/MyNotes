[Previous](./[4]-Layout-And-Constraints.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[6]-Styling-And-Theming.md)

*Building The UI*

# Lesson 5 - Material And Cupertino

Flutter ships with two widget libraries: **Material** (Google's design language) and **Cupertino** (Apple's iOS style). This lesson shows what each offers and how to make an app feel right on both platforms.

---

## 5.1 Material components

Import it with `import 'package:flutter/material.dart';`. Modern Flutter uses **Material 3** by default.

Frequently used components:

```dart
// Buttons
ElevatedButton(onPressed: () {}, child: const Text('Elevated'));
FilledButton(onPressed: () {}, child: const Text('Filled'));
OutlinedButton(onPressed: () {}, child: const Text('Outlined'));
TextButton(onPressed: () {}, child: const Text('Text'));
IconButton(onPressed: () {}, icon: const Icon(Icons.favorite));
FloatingActionButton(onPressed: () {}, child: const Icon(Icons.add));

// Cards and lists
Card(
  child: ListTile(
    leading: const Icon(Icons.album),
    title: const Text('Song title'),
    subtitle: const Text('Artist'),
    trailing: const Icon(Icons.chevron_right),
  ),
);

// Selection
Switch(value: true, onChanged: (v) {});
Checkbox(value: false, onChanged: (v) {});
Slider(value: 0.5, onChanged: (v) {});
```

Passing `onPressed: null` disables a button automatically.

**Dialogs and feedback:**

```dart
void showInfo(BuildContext context) {
  showDialog(
    context: context,
    builder: (context) => AlertDialog(
      title: const Text('Delete item?'),
      content: const Text('This cannot be undone.'),
      actions: [
        TextButton(
          onPressed: () => Navigator.pop(context),
          child: const Text('Cancel'),
        ),
        FilledButton(
          onPressed: () => Navigator.pop(context),
          child: const Text('Delete'),
        ),
      ],
    ),
  );
}

// A brief message at the bottom of the screen
ScaffoldMessenger.of(context).showSnackBar(
  const SnackBar(content: Text('Saved')),
);
```

---

## 5.2 Cupertino (iOS-style) widgets

Import it with `import 'package:flutter/cupertino.dart';`. It provides widgets that look and behave like native iOS controls:

```dart
CupertinoButton(
  onPressed: () {},
  child: const Text('Continue'),
);

CupertinoSwitch(value: true, onChanged: (v) {});

CupertinoActivityIndicator();

CupertinoSlider(value: 0.5, onChanged: (v) {});

showCupertinoDialog(
  context: context,
  builder: (context) => CupertinoAlertDialog(
    title: const Text('Delete item?'),
    actions: [
      CupertinoDialogAction(
        onPressed: () => Navigator.pop(context),
        child: const Text('Cancel'),
      ),
      CupertinoDialogAction(
        isDestructiveAction: true,
        onPressed: () => Navigator.pop(context),
        child: const Text('Delete'),
      ),
    ],
  ),
);
```

An app built entirely in iOS style uses `CupertinoApp` and `CupertinoPageScaffold`:

```dart
CupertinoApp(
  home: CupertinoPageScaffold(
    navigationBar: const CupertinoNavigationBar(
      middle: Text('Home'),
    ),
    child: const Center(child: Text('Hello')),
  ),
)
```

Cupertino also includes `CupertinoTabBar`, `CupertinoPicker`, `CupertinoDatePicker`, and the swipe-back page transition iOS users expect.

---

## 5.3 Scaffold and AppBar

`Scaffold` is the standard page skeleton in a Material app. It positions the common screen parts for you:

```dart
Scaffold(
  appBar: AppBar(
    title: const Text('Home'),
    actions: [
      IconButton(icon: const Icon(Icons.search), onPressed: () {}),
    ],
  ),
  drawer: const Drawer(child: Center(child: Text('Menu'))),
  body: const Center(child: Text('Content')),
  floatingActionButton: FloatingActionButton(
    onPressed: () {},
    child: const Icon(Icons.add),
  ),
  bottomNavigationBar: NavigationBar(
    selectedIndex: 0,
    onDestinationSelected: (i) {},
    destinations: const [
      NavigationDestination(icon: Icon(Icons.home), label: 'Home'),
      NavigationDestination(icon: Icon(Icons.person), label: 'Profile'),
    ],
  ),
)
```

Key points:

- The `AppBar` automatically shows a back button or a drawer (menu) icon when appropriate.
- Wrap content in `SafeArea` when you are not using a Scaffold body, so it avoids notches and system bars.
- `Scaffold` supports `resizeToAvoidBottomInset`, which controls whether the body shrinks when the keyboard opens.
- Use `TabBar` + `TabBarView` inside a `DefaultTabController` for swipeable tabs.

---

## 5.4 Adaptive widgets

You can ship **one codebase that feels native on each platform** in several ways.

**1. Adaptive constructors.** Some Material widgets switch style by platform:

```dart
Switch.adaptive(value: true, onChanged: (v) {});
Slider.adaptive(value: 0.5, onChanged: (v) {});
CircularProgressIndicator.adaptive();
Checkbox.adaptive(value: false, onChanged: (v) {});
```

On iOS and macOS these render Cupertino-style controls, and elsewhere they render Material ones.

**2. Check the platform** and choose widgets yourself:

```dart
import 'package:flutter/foundation.dart';

bool get isIOS => defaultTargetPlatform == TargetPlatform.iOS;

Widget buildButton() {
  return isIOS
      ? CupertinoButton.filled(onPressed: () {}, child: const Text('Save'))
      : FilledButton(onPressed: () {}, child: const Text('Save'));
}
```

Use `defaultTargetPlatform` or `Theme.of(context).platform`. Avoid `dart:io`'s `Platform` if your app also targets the web, since it is not available there.

**3. Platform-appropriate behavior.** Theme settings such as scroll physics and page transitions already follow the platform (bouncy scroll on iOS, glow or stretch on Android).

**Decision guide:**

| Situation | Approach |
|---|---|
| Brand-driven, identical on every platform | Material everywhere with a custom theme |
| Want native feel on each platform | `.adaptive` widgets or a platform check |
| iOS-only app | `CupertinoApp` |

---

[Previous](./[4]-Layout-And-Constraints.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[6]-Styling-And-Theming.md)