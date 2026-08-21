[Previous](./[8]-Collections.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[10]-Widgets-and-Components.md)

*UI Fundamentals*

# Lesson 9 - Screens, Views & Layouts

## 9.1 What is a Screen?

A **screen** (sometimes called a page, activity, or view controller) represents one full-screen unit of UI the user sees at a time — a login screen, a product detail screen, a settings screen. Apps are built by composing many small pieces of UI into screens, and then connecting screens together with navigation (Lesson 13).

---

## 9.2 The View Tree

Every mobile UI framework represents a screen as a **tree** of nested elements: a container holds rows and columns, which hold text, images, and buttons. Understanding this tree structure is fundamental, because layout, styling, and state all flow through it.

```dart
Column(
  children: [
    Text("Welcome"),
    Row(
      children: [Icon(Icons.star), Text("Favorite")],
    ),
  ],
)
```

This example creates a vertical **Column** containing a text label and a horizontal **Row** nested inside it — the same nesting concept exists in SwiftUI's `VStack`/`HStack` and Jetpack Compose's `Column`/`Row`.

---

## 9.3 Layout Building Blocks

Most layout systems boil down to a small set of primitives:

- **Stacking containers**: arrange children vertically or horizontally (`Column`/`Row`, `VStack`/`HStack`, `LinearLayout`).
- **Box/overlap containers**: layer children on top of one another (`Stack`, `ZStack`, `FrameLayout`).
- **Spacing**: padding (space inside a container's edge) vs. margin (space outside it).
- **Alignment**: how children position themselves within available space (start, center, end, stretch).

---

## 9.4 Declarative vs Imperative UI

Modern frameworks (SwiftUI, Jetpack Compose, Flutter) are **declarative**: you describe *what* the UI should look like for a given state, and the framework figures out *how* to update the screen when that state changes. Older frameworks (UIKit, classic Android Views) are **imperative**: you manually create UI elements and update their properties step by step. Declarative UI has become the industry standard because it eliminates a whole class of bugs where the UI falls out of sync with the underlying data.

[Previous](./[8]-Collections.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[10]-Widgets-and-Components.md)
