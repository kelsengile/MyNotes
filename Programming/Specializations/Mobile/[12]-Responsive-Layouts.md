[Previous](./[11]-Styling-and-Theming.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[13]-Navigation-Between-Screens.md)

*UI Fundamentals*

# Lesson 12 - Responsive Layouts & Screen Sizes

## 12.1 Why Responsiveness Matters

Mobile apps run on an enormous range of screen sizes — small phones, large phones, foldables, and tablets — and users can rotate their device between portrait and landscape at any time. A layout hard-coded to one screen size will look broken or clipped on others, so mobile UIs are built to **adapt** rather than assume fixed dimensions.

---

## 12.2 Flexible Sizing

Instead of fixed pixel widths, layouts use flexible sizing so elements grow or shrink to fill available space:

```dart
Row(
  children: [
    Expanded(flex: 2, child: Container(color: Colors.red)),
    Expanded(flex: 1, child: Container(color: Colors.blue)),
  ],
)
```

Here, `Expanded` with `flex` divides the row's width proportionally (2:1) rather than using fixed pixel values, so the ratio holds on any screen width.

---

## 12.3 Breakpoints and Adaptive Layouts

For big differences — like showing a single column on a phone but two columns on a tablet — apps check the available width and switch layouts entirely, a pattern often called a **breakpoint**:

```dart
Widget build(BuildContext context) {
  final width = MediaQuery.of(context).size.width;
  return width > 600 ? TwoColumnLayout() : SingleColumnLayout();
}
```

---

## 12.4 Safe Areas and Device Quirks

Modern phones have notches, camera cutouts, and rounded corners, and the OS reserves a **safe area** so content isn't obscured. Frameworks provide dedicated widgets for this (`SafeArea` in Flutter, `safeAreaInsets` in SwiftUI). Ignoring the safe area is one of the most common beginner mistakes — it results in text or buttons hidden behind the status bar or a phone's notch.

## 12.5 Density-Independent Units

Screens have different pixel densities (how many physical pixels per inch), so layouts are measured in density-independent units (`dp` on Android, points on iOS) rather than raw pixels, ensuring a "44-unit button" looks the same physical size across devices with very different pixel counts.

[Previous](./[11]-Styling-and-Theming.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[13]-Navigation-Between-Screens.md)
