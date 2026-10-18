[Previous](./[3]-Widgets-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[5]-Material-And-Cupertino.md)

*Building The UI*

# Lesson 4 - Layout And Constraints

Layout in Flutter is done by composing layout widgets. This lesson covers the core ones, the rule that governs how big everything is, and scrolling lists and grids.

---

## 4.1 Row, Column, and Stack

**`Row`** lays children out horizontally and **`Column`** vertically. The direction they run in is the **main axis**, and the perpendicular direction is the **cross axis**.

```dart
Column(
  mainAxisAlignment: MainAxisAlignment.center,       // along the column
  crossAxisAlignment: CrossAxisAlignment.start,      // across (left edge)
  children: const [
    Text('Title'),
    SizedBox(height: 8),                             // spacing
    Text('Subtitle'),
  ],
)
```

Useful `MainAxisAlignment` values: `start`, `center`, `end`, `spaceBetween`, `spaceAround`, `spaceEvenly`.

`mainAxisSize: MainAxisSize.min` makes the Row/Column only as big as its children, rather than filling the available space.

**`Stack`** layers children on top of each other. Use `Positioned` to pin a child to an edge:

```dart
Stack(
  alignment: Alignment.center,
  children: [
    Container(width: 200, height: 200, color: Colors.blue),
    const Text('Centered'),
    const Positioned(
      top: 8,
      right: 8,
      child: Icon(Icons.star),
    ),
  ],
)
```

Other handy layout widgets: `Padding`, `Center`, `Align`, `SizedBox`, `Container`, `Wrap` (wraps children onto new lines), and `SingleChildScrollView`.

---

## 4.2 Expanded and Flexible

Inside a `Row` or `Column`, wrap a child in **`Expanded`** to make it fill the leftover space along the main axis:

```dart
Row(
  children: [
    const Icon(Icons.person),
    Expanded(
      child: Text('A long name that should use the remaining width'),
    ),
    const Icon(Icons.more_vert),
  ],
)
```

Several `Expanded` children share the space by `flex` ratio:

```dart
Row(
  children: [
    Expanded(flex: 2, child: Container(color: Colors.red, height: 50)),
    Expanded(flex: 1, child: Container(color: Colors.green, height: 50)),
  ],
)
// red gets 2/3 of the width, green gets 1/3
```

**`Flexible`** is similar, but by default the child may be *smaller* than its share (`fit: FlexFit.loose`). `Expanded` is `Flexible` with `fit: FlexFit.tight`, which forces the child to fill its share.

A classic error: putting a `ListView` or another unbounded-height widget directly inside a `Column`. The `Column` gives it infinite height. Wrap the `ListView` in `Expanded` to fix it.

---

## 4.3 How constraints work

Flutter's layout rule in one sentence:

> **Constraints go down. Sizes go up. The parent sets the position.**

1. A parent passes each child **constraints**: minimum and maximum width and height.
2. The child picks a size **within** those constraints and reports it back up.
3. The parent then decides where to place the child.

This explains many surprises:

- A `Container` with `width: 100` inside a parent that forces a tight size will ignore your 100. The parent's constraint wins.
- `Center` loosens constraints, so its child can be smaller than the screen.
- `Text` is as wide as its content, up to the maximum width it is given.

**Common constraint helpers:**

```dart
ConstrainedBox(
  constraints: const BoxConstraints(maxWidth: 400, minHeight: 100),
  child: ...,
)

SizedBox(width: 120, height: 48, child: ...)   // tight constraints

FractionallySizedBox(widthFactor: 0.8, child: ...) // 80% of parent
```

**Overflow errors.** If children are bigger than the space they are given, you see the yellow-and-black "RenderFlex overflowed" stripe. Fixes: wrap in `Expanded`/`Flexible`, use `SingleChildScrollView`, or reduce sizes. When confused, open the widget inspector (Lesson 16) to see the constraints passed to each widget.

---

## 4.4 Lists and grids

For a short, fixed list, `ListView(children: [...])` is fine. For long or dynamic data use **`ListView.builder`**, which builds only the rows currently visible:

```dart
final items = List.generate(1000, (i) => 'Item $i');

ListView.builder(
  itemCount: items.length,
  itemBuilder: (context, index) {
    return ListTile(
      leading: const Icon(Icons.label),
      title: Text(items[index]),
      onTap: () => debugPrint('Tapped ${items[index]}'),
    );
  },
)
```

Add dividers with `ListView.separated`:

```dart
ListView.separated(
  itemCount: items.length,
  separatorBuilder: (context, index) => const Divider(),
  itemBuilder: (context, index) => ListTile(title: Text(items[index])),
)
```

**Grids** use `GridView.builder` and a delegate describing the columns:

```dart
GridView.builder(
  padding: const EdgeInsets.all(8),
  gridDelegate: const SliverGridDelegateWithFixedCrossAxisCount(
    crossAxisCount: 2,
    mainAxisSpacing: 8,
    crossAxisSpacing: 8,
    childAspectRatio: 1,
  ),
  itemCount: 20,
  itemBuilder: (context, index) => Container(
    color: Colors.teal,
    child: Center(child: Text('$index')),
  ),
)
```

Use `SliverGridDelegateWithMaxCrossAxisExtent(maxCrossAxisExtent: 200)` to let the column count adapt to the screen width.

For mixing scrolling pieces (a header that collapses, then a list, then a grid), use `CustomScrollView` with **slivers**: `SliverAppBar`, `SliverList`, `SliverGrid`.

---

[Previous](./[3]-Widgets-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[5]-Material-And-Cupertino.md)