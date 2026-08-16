[Previous](./[6]-Text-Formatting.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[8]-Links-and-Anchors.md)

*Text & Content Basics*

# Lesson 7 - Lists

## 7.1 Unordered Lists

An **unordered list** (`<ul>`) is a bulleted list of items, where order doesn't matter. Each item goes inside an `<li>` (list item).

```html
<ul>
  <li>Milk</li>
  <li>Eggs</li>
  <li>Bread</li>
</ul>
```

---

## 7.2 Ordered Lists

An **ordered list** (`<ol>`) is numbered, used when sequence matters.

```html
<ol>
  <li>Preheat the oven.</li>
  <li>Mix the ingredients.</li>
  <li>Bake for 30 minutes.</li>
</ol>
```

You can customize numbering with the `start` attribute or `type` attribute (`1`, `A`, `a`, `I`, `i`):

```html
<ol start="5" type="A">
  <li>Item E</li>
  <li>Item F</li>
</ol>
```

---

## 7.3 Description Lists

A **description list** (`<dl>`) pairs terms with descriptions, using `<dt>` (term) and `<dd>` (description).

```html
<dl>
  <dt>HTML</dt>
  <dd>The markup language that structures web pages.</dd>
  <dt>CSS</dt>
  <dd>The language used to style HTML.</dd>
</dl>
```

---

## 7.4 Nesting Lists

Lists can be nested inside list items to represent sub-items.

```html
<ul>
  <li>Fruits
    <ul>
      <li>Apple</li>
      <li>Banana</li>
    </ul>
  </li>
  <li>Vegetables</li>
</ul>
```

Make sure the nested list sits *inside* the `<li>` it belongs to, not as a sibling of it — this keeps the relationship between items and sub-items semantically correct.

[Previous](./[6]-Text-Formatting.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[8]-Links-and-Anchors.md)
