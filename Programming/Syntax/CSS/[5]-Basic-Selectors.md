[Previous](./[4]-Cascade-Specificity-and-Inheritance.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[6]-Combinators.md)

*Selectors*

# Lesson 5 - Basic Selectors

## 5.1 Element (Type) Selector

Targets every instance of a given HTML tag.

```css
p {
  line-height: 1.6;
}
```

This applies to every `<p>` element on the page. Element selectors are useful for broad, page-wide defaults but aren't specific enough for one-off styling.

---

## 5.2 Class Selector

Targets any element with a matching `class` attribute, written with a leading dot. Classes are the most common selector in real projects because they're reusable and don't depend on document structure.

```html
<p class="highlight">Important text</p>
<span class="highlight">Also important</span>
```

```css
.highlight {
  background-color: yellow;
}
```

An element can have multiple classes, separated by spaces in the HTML:

```html
<p class="highlight bold">Text</p>
```

```css
.highlight { background-color: yellow; }
.bold { font-weight: bold; }
```

---

## 5.3 ID Selector

Targets the single element with a matching `id` attribute, written with a leading `#`. IDs must be unique per page, and carry much higher specificity than classes (Lesson 4).

```html
<header id="main-header">Site Title</header>
```

```css
#main-header {
  background-color: #222;
  color: white;
}
```

Because of their high specificity and one-per-page nature, IDs are generally reserved for unique page landmarks or JavaScript hooks rather than everyday styling — classes are preferred for that.

---

## 5.4 Universal Selector

The asterisk `*` matches every element on the page. It's most often used for broad resets.

```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
```

Because it applies to everything, the universal selector should be used carefully — it's powerful, but overusing it can make styles harder to trace.

---

## 5.5 Grouping Selectors

Any of the above can be combined with commas to apply the same rule to multiple, unrelated selectors at once.

```css
h1, .highlight, #main-header {
  font-family: Georgia, serif;
}
```

[Previous](./[4]-Cascade-Specificity-and-Inheritance.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[6]-Combinators.md)
