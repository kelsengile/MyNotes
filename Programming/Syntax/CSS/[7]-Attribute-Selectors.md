[Previous](./[6]-Combinators.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[8]-Pseudo-Classes.md)

*Selectors*

# Lesson 7 - Attribute Selectors

Attribute selectors target elements based on the presence or value of an HTML attribute, using square brackets.

## 7.1 Presence Selector

Matches any element that has the given attribute, regardless of its value.

```css
[disabled] {
  opacity: 0.5;
}
```

This matches any element with a `disabled` attribute, such as `<button disabled>` or `<input disabled>`.

---

## 7.2 Exact Value Selector

Matches an element only if the attribute's value is an exact match.

```css
input[type="email"] {
  border-color: blue;
}
```

Only `<input type="email">` is matched — `<input type="text">` is not.

---

## 7.3 Partial And Pattern Matching

Several operators allow matching part of an attribute's value:

| Selector | Matches |
|---|---|
| `[attr~="value"]` | Value appears as one whole word in a space-separated list |
| `[attr\|="value"]` | Value equals, or starts with value followed by a hyphen |
| `[attr^="value"]` | Value starts with the given string |
| `[attr$="value"]` | Value ends with the given string |
| `[attr*="value"]` | Value contains the given string anywhere |

```css
/* Links to external PDFs */
a[href$=".pdf"] {
  color: crimson;
}

/* Any link pointing to an external domain */
a[href^="https://"] {
  text-decoration: underline;
}

/* Any class attribute containing "card" */
[class*="card"] {
  border-radius: 8px;
}
```

---

## 7.4 Case-Insensitive Matching

Adding `i` before the closing bracket makes the value comparison case-insensitive (useful for attributes users might input inconsistently).

```css
[data-status="active" i] {
  color: green;
}
```

This matches `data-status="active"`, `data-status="Active"`, and `data-status="ACTIVE"` alike.

---

## 7.5 Practical Use Cases

Attribute selectors are especially handy for styling form elements and states without needing extra classes:

```css
input:required {
  border-left: 3px solid orange;
}

input[type="checkbox"] {
  width: 1.2em;
  height: 1.2em;
}
```

[Previous](./[6]-Combinators.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[8]-Pseudo-Classes.md)
