[Previous](./[13]-Navigation-Between-Screens.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[15]-Managing-UI-State.md)

*UI Fundamentals*

# Lesson 14 - Animations & Gestures

## 14.1 Why Animation Matters

Animation isn't just decoration — it gives users feedback that an action worked (a button press, a screen transition) and helps them understand spatial relationships between screens. Well-used animation makes an app feel responsive and polished; overused animation makes it feel slow and distracting.

---

## 14.2 Implicit vs Explicit Animations

- **Implicit animations**: you just change a value, and the framework automatically animates the transition between the old and new value.
- **Explicit animations**: you control timing, curves, and playback manually using an animation controller for more complex or interactive effects.

```dart
AnimatedContainer(
  duration: Duration(milliseconds: 300),
  width: isExpanded ? 200 : 100,
  color: isExpanded ? Colors.blue : Colors.grey,
)
```

Changing `isExpanded` here automatically animates the width and color change over 300ms — no manual animation code required.

---

## 14.3 Gesture Recognition

Touchscreens support far more input than a single "click" — mobile frameworks recognize taps, double-taps, long-presses, drags/pans, pinches (for zoom), and swipes:

```dart
GestureDetector(
  onTap: () => print("Tapped"),
  onLongPress: () => print("Long pressed"),
  onPanUpdate: (details) => print("Dragging: ${details.delta}"),
  child: Icon(Icons.star),
)
```

---

## 14.4 Common Gesture-Driven Patterns

- **Swipe-to-dismiss**: swiping a list item (e.g., an email) removes it.
- **Pull-to-refresh**: dragging down at the top of a list triggers a data reload.
- **Pinch-to-zoom**: two-finger gestures scale an image or map.

These patterns are common enough that most frameworks provide them as ready-made widgets (`Dismissible`, `RefreshIndicator` in Flutter) rather than requiring you to build gesture-detection logic from scratch.

[Previous](./[13]-Navigation-Between-Screens.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[15]-Managing-UI-State.md)
