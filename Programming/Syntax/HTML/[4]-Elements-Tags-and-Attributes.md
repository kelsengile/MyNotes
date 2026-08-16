[Previous](./[3]-Document-Structure.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[5]-Headings-and-Paragraphs.md)

*Getting Started*

# Lesson 4 - Elements, Tags & Attributes

## 4.1 Tags vs. Elements

These two words get used interchangeably, but technically:

- A **tag** is the markup itself: `<p>` is an opening tag, `</p>` is a closing tag.
- An **element** is the tag(s) plus its content: `<p>Hello</p>` is a paragraph *element*.

```html
<p>This whole thing is the element.</p>
```

---

## 4.2 Attributes

**Attributes** provide extra information about an element. They live inside the opening tag as `name="value"` pairs.

```html
<a href="https://example.com" target="_blank">Visit Example</a>
```

Here, `href` and `target` are attributes of the `<a>` element. Some rules of thumb:

- Attribute values should be wrapped in quotes (double quotes are the convention).
- Elements can have any number of attributes, separated by spaces.
- Some attributes are **boolean** — their presence alone means "true" (e.g. `disabled`, `required`).

```html
<input type="text" disabled>
```

---

## 4.3 Void (Self-Closing) Elements

Most elements have an opening and closing tag, but a few — called **void elements** — never wrap content and don't need a closing tag:

```html
<img src="photo.jpg" alt="A photo">
<br>
<hr>
<input type="text">
```

Common void elements: `<img>`, `<br>`, `<hr>`, `<input>`, `<meta>`, `<link>`.

---

## 4.4 Nesting Rules

Elements can contain other elements, but they must be **properly nested** — closing tags must close in the reverse order they were opened.

```html
<!-- Correct -->
<p>This is <strong>bold</strong> text.</p>

<!-- Incorrect: tags cross over each other -->
<p>This is <strong>bold</p> text.</strong>
```

Improperly nested tags can cause browsers to render pages unpredictably, so always close tags in the right order.

[Previous](./[3]-Document-Structure.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[5]-Headings-and-Paragraphs.md)
