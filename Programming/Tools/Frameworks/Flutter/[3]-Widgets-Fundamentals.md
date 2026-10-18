[Previous](./[2]-Dart-Essentials.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[4]-Layout-And-Constraints.md)

*Basics*

# Lesson 3 - Widgets Fundamentals

Widgets are the building blocks of every Flutter interface. This lesson explains what a widget is, the two main kinds, how the widget tree is built, and how Flutter decides whether a widget is "the same" across rebuilds.

---

## 3.1 Everything is a widget

In Flutter, almost everything is a widget: a button, a piece of text, padding, alignment, a theme, even the app itself. A widget is an immutable description of part of the UI.

Widgets **compose**. Instead of setting properties like "padding" on a button, you wrap it in a `Padding` widget:

```dart
Padding(
  padding: const EdgeInsets.all(16),
  child: Text('Hello'),
)
```

Common widget categories:

| Category | Examples |
|---|---|
| Structure | `Scaffold`, `AppBar`, `Container` |
| Layout | `Row`, `Column`, `Stack`, `Padding`, `Center` |
| Content | `Text`, `Image`, `Icon` |
| Interaction | `ElevatedButton`, `TextField`, `GestureDetector` |
| Scrolling | `ListView`, `GridView`, `SingleChildScrollView` |

Widgets with one child use `child:`, and widgets with several use `children: [ ... ]`.

---

## 3.2 StatelessWidget vs StatefulWidget

**StatelessWidget** has no changing internal data. Its output depends only on the values passed into it.

```dart
class Greeting extends StatelessWidget {
  const Greeting({super.key, required this.name});

  final String name;

  @override
  Widget build(BuildContext context) {
    return Text('Hello, $name');
  }
}
```

**StatefulWidget** owns mutable data that can change over time, such as a counter or a checkbox. It is split into two classes: the widget (immutable) and a `State` object (mutable, long-lived).

```dart
class Counter extends StatefulWidget {
  const Counter({super.key});

  @override
  State<Counter> createState() => _CounterState();
}

class _CounterState extends State<Counter> {
  int _count = 0;

  void _increment() {
    setState(() {
      _count++;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Text('Count: $_count'),
        ElevatedButton(
          onPressed: _increment,
          child: const Text('Add'),
        ),
      ],
    );
  }
}
```

`setState()` tells Flutter the state changed and `build` must run again.

**Choosing between them:** start with `StatelessWidget`. Use `StatefulWidget` only when the widget itself must remember something that changes, or when it needs lifecycle methods:

- `initState()`: runs once when the State is created (set up controllers, subscriptions).
- `didUpdateWidget()`: runs when the parent supplies a new configuration.
- `dispose()`: runs when removed (clean up controllers, cancel subscriptions).

---

## 3.3 The widget tree and build method

Your app is a **tree** of widgets, with `runApp`'s widget at the root:

```
MaterialApp
└── Scaffold
    ├── AppBar
    │   └── Text
    └── Center
        └── Column
            ├── Text
            └── ElevatedButton
                └── Text
```

Flutter calls `build()` to ask a widget "what UI do you describe right now?". The method returns other widgets, which in turn get built, down to primitive widgets that paint on screen.

**Rules for `build`:**

- It can be called many times, even every frame during animation, so it must be fast and free of side effects.
- Never start network calls or create controllers inside `build`.
- It receives a `BuildContext`, which is the widget's location in the tree. Use it to look up ancestors:

```dart
final theme = Theme.of(context);
final size = MediaQuery.of(context).size;
Navigator.of(context).pop();
```

**Keep widgets small.** Extract pieces into their own widget classes rather than deep nesting or helper methods. Small `const` widgets let Flutter skip rebuilding unchanged subtrees:

```dart
const Text('Static label');   // const: created once, reused
```

---

## 3.4 Keys and widget identity

When Flutter rebuilds, it decides whether to **reuse** or **replace** each element by comparing widget `runtimeType` and `key`. Without keys, widgets of the same type in the same position are matched up, which usually works fine.

It breaks when a list of **stateful** widgets is reordered:

```dart
// Without keys, removing or reordering items can mix up which State
// belongs to which row, so a checkbox state may "move" to the wrong item.
ListView(
  children: [
    for (final todo in todos)
      TodoTile(key: ValueKey(todo.id), todo: todo),
  ],
)
```

**Types of keys:**

| Key | Use it when |
|---|---|
| `ValueKey(value)` | Each item has a unique value, such as an id |
| `ObjectKey(obj)` | The object itself is the identity |
| `UniqueKey()` | You want a new identity every build, which forces state reset |
| `GlobalKey` | You need to reach a widget's State or position from elsewhere (use sparingly) |

Example: `GlobalKey<FormState>` lets you call `validate()` on a form from outside it (see Lesson 7).

**Rule of thumb:** if you have a dynamic list of stateful widgets, give each a `ValueKey` based on a stable id. For static layouts you rarely need keys.

---

[Previous](./[2]-Dart-Essentials.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[4]-Layout-And-Constraints.md)