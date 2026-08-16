[Previous](./[4]-Elements-Tags-and-Attributes.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[6]-Text-Formatting.md)

*Text & Content Basics*

# Lesson 5 - Headings & Paragraphs

## 5.1 Heading Levels `<h1>`–`<h6>`

HTML provides six levels of headings, from `<h1>` (most important) to `<h6>` (least important).

```html
<h1>Page Title</h1>
<h2>Section Title</h2>
<h3>Subsection Title</h3>
```

Headings aren't just for making text bigger — they define the outline of your document, which is used by screen readers and search engines to understand its structure.

---

## 5.2 The Paragraph Element

The `<p>` element represents a block of text — a paragraph.

```html
<p>HTML documents are made of elements that describe content.</p>
<p>Each paragraph is its own block, separated visually from the next.</p>
```

Browsers automatically add spacing above and below paragraphs, so you don't need extra line breaks between them.

---

## 5.3 Best Practices for Heading Hierarchy

- Use exactly **one `<h1>`** per page — it should describe the page's main topic.
- Don't skip levels for styling reasons (e.g. going from `<h2>` straight to `<h5>` because it "looks right"). Use CSS to control appearance instead.
- Nest headings logically: an `<h3>` should belong to the `<h2>` above it, not float on its own.

```html
<h1>HTML Course</h1>
<h2>Text Basics</h2>
<h3>Headings</h3>
<h3>Paragraphs</h3>
<h2>Semantic HTML</h2>
```

This structure creates a predictable outline, similar to a table of contents, that both humans and assistive technology can follow.

[Previous](./[4]-Elements-Tags-and-Attributes.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[6]-Text-Formatting.md)
