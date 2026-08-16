[Previous](./[11]-Box-Sizing.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[13]-Display-Property.md)

*The Box Model*

# Lesson 12 - Margin Collapsing

## 12.1 What Is Margin Collapsing?

When two vertical margins meet, they don't always add together — instead, they "collapse" into a single margin equal to the **larger** of the two. This behavior is unique to vertical margins in normal document flow, and it often surprises beginners.

```html
<p style="margin-bottom: 30px;">First paragraph</p>
<p style="margin-top: 20px;">Second paragraph</p>
```

You might expect 50px of space between the paragraphs (30 + 20), but the actual gap is only **30px** — the larger of the two collapses into one.

---

## 12.2 When Collapsing Happens

Margin collapsing occurs in a few specific situations:

- **Adjacent siblings** — the bottom margin of one element and the top margin of the next
- **Parent and first/last child** — a parent's top margin can collapse with its first child's top margin (and similarly for bottom/last child), if there's no padding, border, or content separating them
- **Empty elements** — an element's own top and bottom margin can collapse together if it has no height, border, or padding

```css
.parent { margin-top: 0; }
.child { margin-top: 40px; }
```

```html
<div class="parent">
  <p class="child">First child</p>
</div>
```

Here, the child's `40px` top margin can "escape" the parent and push the parent itself downward, rather than creating space inside it — because nothing (padding, border) separates the parent's edge from the child's edge.

---

## 12.3 When Collapsing Does *Not* Happen

Margin collapsing does **not** occur:

- Between **horizontal** margins (left/right) — ever
- When elements use Flexbox or Grid layout (their children never collapse margins)
- When there's padding, a border, or intervening content between the two margins
- With `overflow` values other than `visible` on the parent

```css
.parent {
  overflow: hidden; /* prevents child margin collapsing with parent */
}
```

---

## 12.4 Avoiding Unexpected Collapsing

A few common, practical fixes:

```css
/* Add padding or a border to break the collapse */
.parent {
  padding-top: 1px;
}

/* Or switch to Flexbox/Grid, which don't collapse margins at all */
.parent {
  display: flex;
  flex-direction: column;
}
```

Understanding margin collapsing helps explain spacing bugs that otherwise look like CSS "not working" — the math is correct, it's just following different rules than simple addition.

[Previous](./[11]-Box-Sizing.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[13]-Display-Property.md)
