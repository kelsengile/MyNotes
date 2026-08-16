[Previous](./[20]-Normal-Flow.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[22]-Floats-and-Clearing.md)

*Layout Fundamentals*

# Lesson 21 - Positioning (Static, Relative, Absolute, Fixed, Sticky)

## 21.1 `static` (The Default)

Every element starts as `position: static` — it simply sits in normal flow (Lesson 20). The offset properties `top`, `right`, `bottom`, and `left` have no effect on statically positioned elements.

```css
.box {
  position: static; /* default, rarely written explicitly */
}
```

---

## 21.2 `relative`

The element stays in normal flow, but can be nudged from its original position using `top`/`right`/`bottom`/`left` — its original space in the layout is still reserved, even though it visually moves.

```css
.nudged {
  position: relative;
  top: 10px;    /* moves down 10px from where it would normally sit */
  left: 20px;   /* moves right 20px */
}
```

`position: relative` is also frequently used just to establish a **positioning context** for an absolutely positioned child (see 21.3), without visually moving the element itself.

---

## 21.3 `absolute`

The element is removed from normal flow entirely and positioned relative to its nearest **positioned ancestor** (any ancestor with `position` set to something other than `static`) — or relative to the page itself if no such ancestor exists.

```css
.parent {
  position: relative; /* establishes the positioning context */
}

.badge {
  position: absolute;
  top: 8px;
  right: 8px;
}
```

Here, `.badge` positions itself relative to `.parent`'s box, rather than the whole page — a very common pattern for badges, tooltips, and dropdown menus.

---

## 21.4 `fixed`

The element is removed from normal flow and positioned relative to the **browser viewport**, staying in place even as the page scrolls.

```css
.sticky-header {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
}
```

---

## 21.5 `sticky`

A hybrid between `relative` and `fixed`: the element scrolls normally within its container until it reaches a specified offset, then "sticks" in place until its container scrolls out of view.

```css
.sticky-nav {
  position: sticky;
  top: 0; /* sticks once it reaches 0px from the top of the viewport */
}
```

`sticky` requires at least one offset (`top`, `bottom`, etc.) to have any effect, and only sticks within the bounds of its parent container.

---

## 21.6 The `z-index` Connection

Positioned elements (anything other than `static`) can be layered using `z-index`, covered in Lesson 23. Elements without a `position` value other than `static` cannot use `z-index` at all.

```css
.overlay {
  position: absolute;
  z-index: 10;
}
```

[Previous](./[20]-Normal-Flow.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[22]-Floats-and-Clearing.md)
