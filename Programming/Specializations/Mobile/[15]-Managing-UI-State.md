[Previous](./[14]-Animations-and-Gestures.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[16]-Handling-User-Input-and-Forms.md)

*State & Data*

# Lesson 15 - Managing UI State

## 15.1 What is "State"?

**State** is any data that can change over time and affects what's rendered on screen — whether a checkbox is checked, how many items are in a cart, or whether data is still loading. In declarative UI frameworks (Lesson 9.4), the core mental model is: **UI = a function of state**. When state changes, the framework re-renders the affected UI automatically.

---

## 15.2 Local Component State

The simplest kind of state lives inside a single widget/component and is discarded when that widget is removed from the screen — perfect for things like "is this dropdown open."

```dart
class Counter extends StatefulWidget {
  @override
  State<Counter> createState() => _CounterState();
}
class _CounterState extends State<Counter> {
  int count = 0;
  void increment() => setState(() => count++);

  @override
  Widget build(BuildContext context) {
    return Column(children: [
      Text("$count"),
      ElevatedButton(onPressed: increment, child: Text("+")),
    ]);
  }
}
```

Calling `setState` (or the equivalent `@State` in SwiftUI, `remember { mutableStateOf() }` in Compose) tells the framework "this data changed, re-render."

---

## 15.3 Lifting State Up

When two sibling widgets need to share the same piece of state (e.g., a filter dropdown and the list it filters), the state is moved ("lifted") to their common parent, which then passes it down to both children. This avoids two widgets holding out-of-sync copies of the same data.

---

## 15.4 Global / App-Wide State

Some state needs to be accessible from many unrelated screens — the logged-in user, the shopping cart, the current theme. Passing this down manually through every widget ("prop drilling") becomes unwieldy, so apps use a **state management solution** instead:

- Flutter: Provider, Riverpod, Bloc.
- React Native: Context API, Redux, Zustand.
- SwiftUI: `@EnvironmentObject`, `ObservableObject`.
- Android: ViewModel + StateFlow (part of the MVVM pattern, covered in Lesson 33).

These tools let any screen read or update shared state without manually threading it through every intermediate widget.

[Previous](./[14]-Animations-and-Gestures.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[16]-Handling-User-Input-and-Forms.md)
