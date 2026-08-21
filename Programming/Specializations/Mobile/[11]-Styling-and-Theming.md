[Previous](./[10]-Widgets-and-Components.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[12]-Responsive-Layouts.md)

*UI Fundamentals*

# Lesson 11 - Styling, Theming & Typography

## 11.1 Applying Styles

Styling controls the visual appearance of widgets — colors, fonts, borders, spacing. Frameworks apply styles either inline on the widget itself or through a shared style object:

```dart
Text(
  "Hello",
  style: TextStyle(fontSize: 24, fontWeight: FontWeight.bold, color: Colors.blue),
)
```

```swift
Text("Hello")
    .font(.title)
    .foregroundColor(.blue)
```

---

## 11.2 Themes

Rather than styling every widget individually, apps define a **theme** — a centralized set of colors, fonts, and spacing values applied consistently across the whole app. This keeps the UI visually cohesive and makes company-wide rebrands or dark mode a one-place change instead of hundreds of edits.

```dart
MaterialApp(
  theme: ThemeData(
    primaryColor: Colors.indigo,
    textTheme: TextTheme(bodyMedium: TextStyle(fontSize: 16)),
  ),
  home: HomeScreen(),
)
```

---

## 11.3 Dark Mode

Modern mobile OSes let users choose a **light** or **dark** appearance system-wide, and well-built apps adapt automatically. This is typically done by defining color values semantically (e.g., "background" and "text") rather than literally (e.g., "white" and "black"), and letting the theme resolve each semantic color differently depending on the active mode.

---

## 11.4 Typography Basics

Good typography improves readability and hierarchy:

- **Font size** establishes importance — larger text draws the eye first (headlines vs. body copy).
- **Font weight** (regular, medium, bold) adds emphasis without changing size.
- **Line height and letter spacing** affect readability, especially for longer paragraphs.
- **System fonts** (San Francisco on iOS, Roboto on Android) are optimized for legibility on small screens and are a safe default; custom fonts should be used deliberately and tested at multiple sizes.

[Previous](./[10]-Widgets-and-Components.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[12]-Responsive-Layouts.md)
