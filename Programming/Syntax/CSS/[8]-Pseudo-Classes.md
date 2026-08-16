[Previous](./[7]-Attribute-Selectors.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[9]-Pseudo-Elements.md)

*Selectors*

# Lesson 8 - Pseudo-Classes

A pseudo-class selects an element based on a **state or condition** rather than its type, class, or attributes. Pseudo-classes are written with a single colon: `:pseudo-class`.

## 8.1 Interaction States

These respond to user interaction, most commonly used on links and buttons.

```css
a:hover {
  color: orange;
}

a:focus {
  outline: 2px solid blue;
}

a:active {
  color: darkred;
}

a:visited {
  color: purple;
}
```

- `:hover` — while the pointer is over the element
- `:focus` — while the element has keyboard/input focus
- `:active` — while the element is being clicked/pressed
- `:visited` — for links the user has already visited

---

## 8.2 Structural Pseudo-Classes

These select elements based on their position among siblings.

```css
li:first-child { font-weight: bold; }
li:last-child { border-bottom: none; }
li:nth-child(2) { color: red; }
li:nth-child(odd) { background: #f5f5f5; }
li:nth-child(3n) { color: blue; } /* every 3rd item */
```

`:nth-child()` accepts a number, a keyword (`odd`/`even`), or a formula (`3n`, `3n+1`) — making it powerful for zebra-striping and repeating patterns without adding classes to every element.

---

## 8.3 Form And Input States

```css
input:disabled { background: #eee; cursor: not-allowed; }
input:checked { accent-color: green; }
input:required { border-left: 3px solid orange; }
input:valid { border-color: green; }
input:invalid { border-color: red; }
```

These let you style form feedback (like validation states) purely with CSS, without any JavaScript.

---

## 8.4 The Negation Pseudo-Class: `:not()`

`:not()` excludes elements that match its argument.

```css
li:not(:last-child) {
  border-bottom: 1px solid #ddd;
}
```

This adds a bottom border to every list item except the last one — a common pattern for dividers between list items.

---

## 8.5 Other Useful Pseudo-Classes

```css
p:empty { display: none; }         /* elements with no children/content */
:root { --brand-color: teal; }     /* the document's root element (<html>) */
.card:has(img) { padding: 0; }     /* parent that contains a matching child */
```

`:has()` is a relatively modern addition that lets you style a parent based on its children — something CSS couldn't do for a long time.

[Previous](./[7]-Attribute-Selectors.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[9]-Pseudo-Elements.md)
