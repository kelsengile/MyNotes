[Previous](./[12]-Responsive-Layouts.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[14]-Animations-and-Gestures.md)

*UI Fundamentals*

# Lesson 13 - Navigation Between Screens

## 13.1 The Navigation Stack

Most mobile navigation is modeled as a **stack**: moving to a new screen pushes it on top, and going back pops it off, revealing the previous screen underneath. This mirrors how users mentally model moving forward and backward through an app.

```dart
Navigator.push(context, MaterialPageRoute(builder: (context) => DetailScreen()));
Navigator.pop(context); // go back
```

---

## 13.2 Passing Data Between Screens

Navigating usually needs to carry data along — e.g., which product to show on the detail screen. Data is typically passed as constructor arguments or route parameters:

```dart
Navigator.push(
  context,
  MaterialPageRoute(builder: (context) => DetailScreen(productId: "p123")),
);
```

Returning data back to the previous screen (e.g., a picked date from a date-picker screen) is also common, usually done by awaiting the navigation call and having the popped screen return a value.

---

## 13.3 Tabs and Drawers

Beyond simple push/pop stacks, apps commonly offer:

- **Tab bars** (bottom or top) for switching between top-level sections of an app (Home, Search, Profile) — each tab often has its own independent navigation stack.
- **Navigation drawers** (a side panel that slides in) for less-frequent destinations like settings or account management.

---

## 13.4 Named Routes and Deep Linking (Preview)

Larger apps often define **named routes** — string identifiers mapped to screens — rather than manually constructing every screen at the call site. This centralizes navigation logic and is also what enables **deep linking** (opening a specific screen directly from a URL or notification), covered fully in Lesson 27.

```dart
Navigator.pushNamed(context, '/product/123');
```

[Previous](./[12]-Responsive-Layouts.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[14]-Animations-and-Gestures.md)
