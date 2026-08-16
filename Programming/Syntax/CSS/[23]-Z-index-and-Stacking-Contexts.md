[Previous](./[22]-Floats-and-Clearing.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[24]-Flexbox-Fundamentals.md)

*Layout Fundamentals*

# Lesson 23 - Z-Index And Stacking Contexts

## 23.1 What `z-index` Does

When elements overlap, `z-index` controls which one appears on top. Higher values stack above lower values. It only works on elements with a `position` value other than `static` (Lesson 21).

```css
.back {
  position: absolute;
  z-index: 1;
}

.front {
  position: absolute;
  z-index: 2; /* renders above .back */
}
```

---

## 23.2 What Is A Stacking Context?

A **stacking context** is a self-contained group of elements that stack together, isolated from elements outside it. Once an element creates a stacking context, its children's `z-index` values are only compared *within* that context — they can never appear above elements from a sibling stacking context, regardless of how high their `z-index` is set.

```html
<div class="context-a">
  <div class="child-a" style="z-index: 999;"></div>
</div>
<div class="context-b">
  <div class="child-b" style="z-index: 1;"></div>
</div>
```

If `.context-a` and `.context-b` both create their own stacking context, `.child-b` can still appear above `.child-a` — because the comparison happens between `.context-a` and `.context-b` themselves, not their children directly.

---

## 23.3 What Creates A New Stacking Context

Several common CSS properties trigger a new stacking context, not just `position` + `z-index`:

```css
.creates-context {
  position: relative;
  z-index: 1;             /* position + non-auto z-index */
}

.creates-context-2 {
  opacity: 0.99;           /* any opacity less than 1 */
}

.creates-context-3 {
  transform: scale(1);     /* any transform */
}

.creates-context-4 {
  display: flex;           /* flex/grid items with z-index also qualify, see below */
}
```

Being aware of this list explains many "impossible" `z-index` bugs — a very high `z-index` can still lose to a lower one, if it's trapped inside an unrelated stacking context created higher up the tree.

---

## 23.4 Debugging Z-Index Issues

A practical approach when stacking doesn't behave as expected:

1. Check whether the element has `position` set to something other than `static`.
2. Trace up the DOM tree for any ancestor that might be creating its own stacking context (opacity, transform, etc.).
3. Remember `z-index` comparisons only happen between siblings *within the same* stacking context — raising a value further won't help if the real problem is context isolation.

```css
/* Common fix: remove the unintentional stacking context, or raise 
   the z-index on the *context-creating* ancestor instead of the child */
.ancestor {
  z-index: 5; /* raise the ancestor's own stacking position */
}
```

[Previous](./[22]-Floats-and-Clearing.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[24]-Flexbox-Fundamentals.md)
