[Previous](./[18]-Backgrounds.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[20]-Normal-Flow.md)

*Colors, Units & Typography*

# Lesson 19 - Borders, Shadows And Outlines

## 19.1 `border-radius`

Rounds the corners of an element's box. A single value rounds all four corners equally; four values target each corner individually.

```css
.rounded {
  border-radius: 8px;
}

.pill {
  border-radius: 999px; /* large enough to fully round short elements into a pill shape */
}

.circle {
  border-radius: 50%; /* on a square element, produces a perfect circle */
}

.custom {
  border-radius: 10px 0 10px 0; /* top-left, top-right, bottom-right, bottom-left */
}
```

---

## 19.2 `box-shadow`

Adds a drop shadow around an element's box. Syntax: `offset-x offset-y blur-radius spread-radius color`.

```css
.card {
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
}

/* Multiple shadows, comma-separated, layered like backgrounds */
.elevated {
  box-shadow:
    0 1px 2px rgba(0, 0, 0, 0.1),
    0 4px 12px rgba(0, 0, 0, 0.1);
}

/* inset shadow, appears inside the box's edge */
.pressed {
  box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.3);
}
```

Layering multiple soft shadows of varying size is a common technique for realistic, subtle elevation effects.

---

## 19.3 `text-shadow`

Adds a shadow to text specifically, sharing similar syntax to `box-shadow` (minus the spread and inset options).

```css
h1 {
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
}
```

---

## 19.4 `outline`

An outline is drawn *outside* the border, doesn't affect layout/box size (unlike border), and is most commonly used for accessible focus indicators.

```css
button:focus {
  outline: 2px solid dodgerblue;
  outline-offset: 2px; /* gap between the element's edge and the outline */
}
```

Removing outlines entirely (`outline: none`) without providing an alternative focus style harms keyboard accessibility — always replace, rather than simply delete, focus indicators.

---

## 19.5 Border Vs. Outline Vs. Box-Shadow

| | Affects Layout Size? | Follows `border-radius`? | Common Use |
|---|---|---|---|
| `border` | Yes | Yes | Visible dividing lines |
| `outline` | No | No (by default) | Focus states |
| `box-shadow` | No | Yes | Depth, elevation, glow effects |

Because `box-shadow` doesn't affect layout and respects rounded corners, it's often used as a border alternative when you don't want to shift surrounding content.

[Previous](./[18]-Backgrounds.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[20]-Normal-Flow.md)
