[Previous](./[16]-Typography-and-Web-Fonts.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[18]-Backgrounds.md)

*Colors, Units & Typography*

# Lesson 17 - Text Styling

## 17.1 `line-height`

Controls the vertical space a line of text occupies — critical for readability. A unitless value (recommended) is relative to the element's own font-size, and inherits proportionally to descendants.

```css
p {
  font-size: 16px;
  line-height: 1.6; /* 25.6px — recommended: keep it unitless */
}
```

A common readability guideline is a `line-height` between `1.4` and `1.6` for body text.

---

## 17.2 `letter-spacing` And `word-spacing`

Adjust horizontal spacing between characters or words.

```css
h1 {
  letter-spacing: 0.05em; /* slightly wider tracking, often used for headings */
}

.uppercase-label {
  letter-spacing: 0.1em;
  text-transform: uppercase;
}
```

---

## 17.3 `text-align`

Controls horizontal alignment of text within its container.

```css
.center { text-align: center; }
.right  { text-align: right; }
.justify { text-align: justify; } /* stretches lines to fill full width */
```

---

## 17.4 `text-decoration`

Adds or removes lines from text, most commonly used to remove the default underline on links.

```css
a {
  text-decoration: none;
}

.strike {
  text-decoration: line-through;
}

.underline-fancy {
  text-decoration: underline wavy red; /* line, style, color shorthand */
}
```

---

## 17.5 `text-transform`

Changes the capitalization of text visually, without altering the underlying HTML content.

```css
.label { text-transform: uppercase; }
.name  { text-transform: capitalize; }
```

---

## 17.6 Overflow And Truncation

Common patterns for handling text that's too long for its container.

```css
/* Single-line ellipsis truncation */
.truncate {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

/* Multi-line clamp (modern browsers) */
.clamp-3-lines {
  display: -webkit-box;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;
  overflow: hidden;
}
```

---

## 17.7 Word Breaking

Controls how long, unbroken strings (like URLs) wrap inside their container.

```css
.wrap-anywhere {
  overflow-wrap: break-word;
  word-break: break-word;
}
```

[Previous](./[16]-Typography-and-Web-Fonts.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[18]-Backgrounds.md)
