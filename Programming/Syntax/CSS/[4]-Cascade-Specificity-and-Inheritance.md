[Previous](./[3]-CSS-Syntax.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[5]-Basic-Selectors.md)

*Getting Started*

# Lesson 4 - The Cascade, Specificity And Inheritance

## 4.1 The Cascade

When multiple CSS rules target the same element and property, the browser has to decide which one wins. This resolution process is called the **cascade**, and it's decided (in order of importance) by:

1. **Importance** — whether a declaration uses `!important`
2. **Origin** — browser default styles vs. your author styles
3. **Specificity** — how "specific" the selector is (see below)
4. **Source order** — later rules override earlier ones if everything else is equal

```css
p { color: blue; }
p { color: green; } /* This wins — it comes later */
```

---

## 4.2 Specificity

Specificity is a weighting system that determines which selector "wins" when two rules of equal importance target the same element. It's calculated from the types of selectors used, roughly in this order of strength (highest to lowest):

1. Inline styles (highest)
2. IDs (`#header`)
3. Classes, attribute selectors, pseudo-classes (`.btn`, `[type="text"]`, `:hover`)
4. Elements and pseudo-elements (`div`, `::before`)

```css
p { color: blue; }            /* specificity: 0-0-1 */
.intro { color: green; }      /* specificity: 0-1-0 — wins over element selector */
#main p.intro { color: red; } /* specificity: 1-1-1 — wins over both */
```

A higher-specificity selector wins regardless of source order. This is why a single ID selector can be surprisingly hard to override later in a stylesheet.

---

## 4.3 `!important`

Adding `!important` after a value forces that declaration to override normal cascade and specificity rules.

```css
p {
  color: blue !important;
}
```

`!important` should be used sparingly — it breaks the predictable flow of the cascade and can make debugging styles much harder. It's generally better to fix specificity issues at their source than to reach for `!important`.

---

## 4.4 Inheritance

Some CSS properties automatically pass down from a parent element to its children — this is **inheritance**. Text-related properties (like `color`, `font-family`, `line-height`) typically inherit, while box-related properties (like `margin`, `border`, `width`) typically do not.

```css
body {
  color: #333;
  font-family: sans-serif;
}
```

Every element inside `<body>` inherits that text color and font unless overridden. You can also force inheritance behavior explicitly with keyword values:

```css
.child {
  color: inherit;  /* take the parent's computed value */
  margin: initial; /* reset to the property's default value */
}
```

Understanding which properties inherit by default helps you write leaner CSS — you often only need to set typography properties once, high up in the document.

[Previous](./[3]-CSS-Syntax.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[5]-Basic-Selectors.md)
