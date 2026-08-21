[Previous](./[9]-Screens-Views-and-Layouts.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[11]-Styling-and-Theming.md)

*UI Fundamentals*

# Lesson 10 - Widgets & Components

## 10.1 What is a Widget/Component?

A **widget** (Flutter), **component** (React Native), or **view** (native) is a single, reusable piece of UI — a button, a text field, an image, or an entire custom-built card. Screens are simply widgets/components composed together, and widgets can themselves be composed of smaller widgets, all the way down to primitive elements like `Text` and `Image`.

---

## 10.2 Common Built-In Widgets

Every framework ships a standard library of ready-made widgets covering the same basic needs:

- **Text display**: `Text` (Flutter/RN), `Text` (SwiftUI), `TextView` (Android).
- **Images**: `Image`.
- **Buttons**: `ElevatedButton`/`TextButton` (Flutter), `Button` (SwiftUI), `Button` (Jetpack Compose).
- **Input fields**: `TextField`/`TextInput`.
- **Lists**: `ListView`/`FlatList` — efficiently render long scrollable collections by only building the items currently visible on screen (this "lazy" rendering is critical for performance with large data sets).

---

## 10.3 Building Custom, Reusable Components

Rather than repeating the same UI code, you extract it into your own reusable widget — the same principle as writing a reusable function, applied to UI:

```dart
class ProductCard extends StatelessWidget {
  final String title;
  final double price;
  const ProductCard({required this.title, required this.price});

  @override
  Widget build(BuildContext context) {
    return Card(
      child: Column(
        children: [Text(title), Text("\$$price")],
      ),
    );
  }
}
```

Once defined, `ProductCard` can be reused anywhere with different data, keeping the UI consistent and the code DRY (Don't Repeat Yourself).

---

## 10.4 Component Props / Parameters

Just like functions, components accept inputs — often called **props** (React Native) or constructor parameters (Flutter/SwiftUI) — that let the same component render differently depending on the data passed in. This is the core mechanism that makes UI reusable: the `ProductCard` above takes a `title` and `price` and can represent any product without duplicating layout code.

[Previous](./[9]-Screens-Views-and-Layouts.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[11]-Styling-and-Theming.md)
