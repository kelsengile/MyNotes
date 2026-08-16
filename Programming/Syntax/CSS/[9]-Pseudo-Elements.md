[Previous](./[8]-Pseudo-Classes.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[10]-The-Box-Model.md)

*Selectors*

# Lesson 9 - Pseudo-Elements

While pseudo-classes target an element's *state*, pseudo-elements target a specific **part** of an element — even parts that don't exist in the HTML. Pseudo-elements use a double colon: `::pseudo-element` (though the single-colon syntax is still supported for older ones, for compatibility).

## 9.1 `::before` And `::after`

These insert generated content immediately before or after an element's actual content, without adding anything to the HTML. They require a `content` property to appear, even if it's empty.

```css
.required::after {
  content: " *";
  color: red;
}

.quote::before {
  content: "“";
}

.quote::after {
  content: "”";
}
```

These are commonly used for decorative content, icons, tooltips, and clearfix hacks (covered in Lesson 22) — content that's purely visual and not meaningful page content.

---

## 9.2 `::first-line` And `::first-letter`

These target the first line or first letter of a block of text, useful for editorial/magazine-style typography.

```css
p::first-line {
  font-weight: bold;
}

p::first-letter {
  font-size: 2em;
  float: left;
  line-height: 1;
}
```

`::first-line` re-applies dynamically as text reflows on different screen sizes — it always targets whatever text currently renders as the first line, not a fixed number of characters.

---

## 9.3 `::selection`

Styles the portion of text a user has highlighted/selected with their cursor.

```css
::selection {
  background: gold;
  color: black;
}
```

---

## 9.4 `::placeholder`

Styles the placeholder text inside form inputs.

```css
input::placeholder {
  color: #999;
  font-style: italic;
}
```

---

## 9.5 Pseudo-Classes Vs. Pseudo-Elements

| | Pseudo-class | Pseudo-element |
|---|---|---|
| Syntax | `:hover` | `::before` |
| Targets | A state/condition of an element | A specific sub-part of an element |
| Example | `a:hover` | `p::first-letter` |

A quick way to remember the distinction: pseudo-*classes* act like a conditional class being toggled on an existing element, while pseudo-*elements* act like a brand-new, generated element.

[Previous](./[8]-Pseudo-Classes.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[10]-The-Box-Model.md)
