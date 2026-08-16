[Previous](./[13]-Div-vs-Span.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[15]-Time-Address-and-Other-Semantic-Elements.md)

*Semantic HTML*

# Lesson 14 - Figures, Captions & Blockquotes

## 14.1 `<figure>` and `<figcaption>`

`<figure>` wraps self-contained media — an image, diagram, code snippet, or chart — that's referenced from the main content but could be moved without disrupting the flow. `<figcaption>` provides a caption for it.

```html
<figure>
  <img src="chart.png" alt="Sales grew 20% year over year">
  <figcaption>Fig. 1 — Quarterly sales growth</figcaption>
</figure>
```

`<figcaption>` is optional but, when present, must be the first or last child of `<figure>`.

---

## 14.2 `<blockquote>`

`<blockquote>` marks an extended quotation from another source. Use the `cite` attribute to link to the source URL.

```html
<blockquote cite="https://example.com/article">
  <p>The web is not just a technology, it's a philosophy of openness.</p>
</blockquote>
```

For a short, inline quotation within a sentence, use `<q>` instead — browsers automatically add quotation marks around it.

```html
<p>She said <q>the web belongs to everyone</q> during the talk.</p>
```

---

## 14.3 `<cite>`

`<cite>` marks the *title* of a creative work being referenced — a book, article, movie, or song — not the person who said the quote.

```html
<p><cite>The Design of Everyday Things</cite> changed how I think about UI.</p>
```

Together, these elements let you properly attribute and format quoted or referenced content instead of relying on plain italics or generic containers.

[Previous](./[13]-Div-vs-Span.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[15]-Time-Address-and-Other-Semantic-Elements.md)
