[Previous](./[19]-Borders-Shadows-and-Outlines.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[21]-Positioning.md)

*Layout Fundamentals*

# Lesson 20 - Normal Flow And Document Flow

## 20.1 What Is Normal Flow?

Normal flow (also called "document flow") is the default way the browser lays out elements when no special positioning, floats, or layout system (Flexbox/Grid) is applied. Elements simply appear in the order they're written in the HTML.

```html
<div>One</div>
<div>Two</div>
<div>Three</div>
```

With no CSS applied, these three `<div>` elements stack vertically, top to bottom, in source order — that's normal flow in action.

---

## 20.2 Block Flow Direction

Block-level elements (Lesson 13) flow **vertically**, each starting a new line and stretching to fill the available width.

```css
div {
  display: block; /* stacks top to bottom */
}
```

---

## 20.3 Inline Flow Direction

Inline elements flow **horizontally**, wrapping to a new line only when they run out of horizontal space — similar to how words wrap in a paragraph.

```html
<span>One</span> <span>Two</span> <span>Three</span>
```

These three spans sit side by side, wrapping only if the container becomes too narrow to fit them all on one line.

---

## 20.4 Taking Elements Out Of Flow

Certain CSS properties remove an element from normal flow entirely, meaning surrounding elements behave as though it isn't there:

```css
.floated { float: left; }              /* Lesson 22 */
.positioned { position: absolute; }    /* Lesson 21 */
.fixed { position: fixed; }            /* Lesson 21 */
```

Once out of flow, an element no longer affects the position of its siblings, and other content may flow around or underneath it — the exact behavior depends on which property took it out of flow.

---

## 20.5 Why This Matters

Every layout technique in CSS — positioning, floats, Flexbox, Grid — is really a different way of overriding or working alongside normal flow. Understanding the default behavior first makes it much easier to reason about what each layout method is actually changing, and why elements sometimes end up overlapping, collapsing, or behaving unexpectedly when flow is altered.

[Previous](./[19]-Borders-Shadows-and-Outlines.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[21]-Positioning.md)
