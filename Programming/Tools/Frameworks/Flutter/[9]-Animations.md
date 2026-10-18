[Previous](./[8]-Responsive-Design.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[10]-Navigation-And-Routing.md)

*Building The UI*

# Lesson 9 - Animations

Animations make interfaces feel alive and help users understand changes. Flutter offers a simple tier (implicit animations) for most cases and a flexible tier (explicit animations) for full control.

---

## 9.1 Implicit animations

Implicit animations animate **automatically** whenever a property value changes. You just set a `duration`, and Flutter handles the in-between frames.

```dart
class AnimatedBox extends StatefulWidget {
  const AnimatedBox({super.key});
  @override
  State<AnimatedBox> createState() => _AnimatedBoxState();
}

class _AnimatedBoxState extends State<AnimatedBox> {
  bool _big = false;

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: () => setState(() => _big = !_big),
      child: AnimatedContainer(
        duration: const Duration(milliseconds: 400),
        curve: Curves.easeInOut,
        width: _big ? 200 : 100,
        height: _big ? 200 : 100,
        decoration: BoxDecoration(
          color: _big ? Colors.orange : Colors.blue,
          borderRadius: BorderRadius.circular(_big ? 40 : 8),
        ),
      ),
    );
  }
}
```

Tapping toggles `_big`, `setState` rebuilds, and `AnimatedContainer` smoothly interpolates the changed properties.

**Other ready-made implicit widgets:**

| Widget | Animates |
|---|---|
| `AnimatedOpacity` | Fade in/out |
| `AnimatedAlign` / `AnimatedPositioned` | Position |
| `AnimatedPadding` | Padding |
| `AnimatedSwitcher` | Cross-fade between different child widgets |
| `AnimatedCrossFade` | Fade between two children |
| `AnimatedDefaultTextStyle` | Text style |

For any single value, use **`TweenAnimationBuilder`**:

```dart
TweenAnimationBuilder<double>(
  tween: Tween(begin: 0, end: 1),
  duration: const Duration(seconds: 1),
  builder: (context, value, child) {
    return Opacity(opacity: value, child: child);
  },
  child: const Text('Fade in once'),
)
```

`curve` controls the pacing: `Curves.linear`, `easeIn`, `easeOut`, `bounceOut`, `elasticOut`, and many more.

---

## 9.2 Explicit animations

Use explicit animations when you need to **start, stop, repeat, or reverse** an animation, or sync several effects. You manage an `AnimationController`.

```dart
class Pulse extends StatefulWidget {
  const Pulse({super.key});
  @override
  State<Pulse> createState() => _PulseState();
}

class _PulseState extends State<Pulse> with SingleTickerProviderStateMixin {
  late final AnimationController _controller;
  late final Animation<double> _scale;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      vsync: this,
      duration: const Duration(seconds: 1),
    )..repeat(reverse: true);

    _scale = Tween<double>(begin: 0.8, end: 1.2).animate(
      CurvedAnimation(parent: _controller, curve: Curves.easeInOut),
    );
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return ScaleTransition(
      scale: _scale,
      child: const Icon(Icons.favorite, size: 64, color: Colors.red),
    );
  }
}
```

The pieces:

- **`AnimationController`** produces values from 0.0 to 1.0 over its duration.
- **`vsync: this`** ties it to the screen refresh. It requires `SingleTickerProviderStateMixin` (or `TickerProviderStateMixin` for several controllers).
- **`Tween`** maps 0..1 to your own range (numbers, colors, sizes).
- **`CurvedAnimation`** applies an easing curve.
- **Transition widgets** (`FadeTransition`, `ScaleTransition`, `SlideTransition`, `RotationTransition`) apply the animation without rebuilding the whole subtree.

Controls: `forward()`, `reverse()`, `repeat()`, `stop()`, `reset()`. Always call `dispose()` on the controller.

For custom visuals use **`AnimatedBuilder`**, which rebuilds only what you place inside it:

```dart
AnimatedBuilder(
  animation: _controller,
  builder: (context, child) {
    return Transform.rotate(angle: _controller.value * 6.28, child: child);
  },
  child: const Icon(Icons.refresh, size: 48),
)
```

---

## 9.3 Hero transitions

A **Hero** animation visually "flies" a shared element from one screen to another during navigation. Give both widgets the **same tag**:

```dart
// List screen
GestureDetector(
  onTap: () => Navigator.push(
    context,
    MaterialPageRoute(builder: (_) => const DetailScreen()),
  ),
  child: Hero(
    tag: 'photo-1',
    child: Image.asset('assets/images/photo.jpg', width: 100),
  ),
)

// Detail screen
class DetailScreen extends StatelessWidget {
  const DetailScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(),
      body: Center(
        child: Hero(
          tag: 'photo-1',
          child: Image.asset('assets/images/photo.jpg', width: 300),
        ),
      ),
    );
  }
}
```

Rules:

- The `tag` must match on both routes and be **unique per screen**. In a list, build it from the item id, such as `'photo-${item.id}'`.
- The animation runs automatically on `push` and `pop`.
- Wrap both Hero children in similar widget types to avoid visual jumps.

**Page transitions** can also be customized with `PageRouteBuilder`:

```dart
Navigator.push(
  context,
  PageRouteBuilder(
    pageBuilder: (_, __, ___) => const DetailScreen(),
    transitionsBuilder: (_, animation, __, child) =>
        FadeTransition(opacity: animation, child: child),
  ),
);
```

---

## 9.4 Animation performance

Smooth animation means each frame finishes in about **16 ms** (60 fps) or about 8 ms on 120 Hz displays.

**Tips:**

1. **Start with implicit animations.** They are simple and efficient.
2. **Animate only what changes.** Pass an unchanging subtree as the `child` of `AnimatedBuilder` or `TweenAnimationBuilder`, so it is built once.
3. **Avoid calling `setState` on a big widget** every frame. Use `AnimatedBuilder` or transition widgets instead.
4. **Prefer transform-based animation** (`Transform`, `ScaleTransition`, `SlideTransition`, `Opacity`) over animating size or layout, because layout changes force costly relayout.
5. **Use `RepaintBoundary`** around a frequently repainting widget, so it does not force the rest of the screen to repaint.
6. **Be careful with expensive effects** such as `saveLayer`-heavy ones: large blurs (`BackdropFilter`), nested `Opacity`, and clipping with `clipBehavior: Clip.antiAlias` on big areas.
7. **Respect accessibility.** Check `MediaQuery.disableAnimationsOf(context)` and shorten or skip motion when the user turned animations off.

**Measuring:** always test in **profile mode** on a real device, because debug mode is much slower:

```bash
flutter run --profile
```

Then use DevTools' Performance view (Lesson 16) to find janky frames. For complex, designer-made animations consider the `lottie` or `rive` packages.

---

[Previous](./[8]-Responsive-Design.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[10]-Navigation-And-Routing.md)