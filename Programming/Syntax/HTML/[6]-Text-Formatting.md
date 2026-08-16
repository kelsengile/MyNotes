[Previous](./[5]-Headings-and-Paragraphs.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[7]-Lists.md)

*Text & Content Basics*

# Lesson 6 - Text Formatting

## 6.1 Bold and Strong

`<b>` makes text visually bold with no added meaning, while `<strong>` marks text as **important**, which also happens to render bold.

```html
<b>Bold with no special meaning.</b>
<strong>This text is important.</strong>
```

Prefer `<strong>` when the emphasis actually matters semantically (screen readers may announce it differently); use `<b>` only for stylistic bolding, like keywords in a product name.

---

## 6.2 Italic and Emphasis

Similarly, `<i>` is for stylistic italics (e.g. a technical term or foreign phrase), while `<em>` adds semantic **emphasis** — like stressing a word when speaking.

```html
<i>Homo sapiens</i> is the scientific name for humans.
<em>Never</em> use tables for page layout.
```

---

## 6.3 Mark, Small, Sub, Sup

- `<mark>` highlights text, like a highlighter pen.
- `<small>` represents side comments or fine print.
- `<sub>` and `<sup>` render subscript and superscript text.

```html
<p>Search results for <mark>HTML</mark>.</p>
<p><small>Terms and conditions apply.</small></p>
<p>H<sub>2</sub>O and E = mc<sup>2</sup></p>
```

---

## 6.4 Line Breaks and Horizontal Rules

- `<br>` inserts a single line break within a block of text (use sparingly — don't use it to add spacing between paragraphs).
- `<hr>` inserts a thematic break, usually rendered as a horizontal line, to separate unrelated sections of content.

```html
<p>123 Main Street<br>Springfield, USA</p>
<hr>
<p>A new, unrelated section starts here.</p>
```

[Previous](./[5]-Headings-and-Paragraphs.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[7]-Lists.md)
