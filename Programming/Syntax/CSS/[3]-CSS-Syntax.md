[Previous](./[2]-Adding-CSS.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[4]-Cascade-Specificity-and-Inheritance.md)

*Getting Started*

# Lesson 3 - CSS Syntax: Rules, Selectors, Properties And Values

## 3.1 Anatomy Of A CSS Rule

Every CSS rule (also called a "ruleset") has the same basic shape:

```css
selector {
  property: value;
}
```

```css
h1 {
  color: navy;
}
```

- The **selector** (`h1`) chooses which element(s) the rule applies to.
- The **declaration block** is everything inside the curly braces `{ }`.
- Each line inside is a **declaration**, made of a **property** (`color`) and a **value** (`navy`), separated by a colon and ended with a semicolon.

---

## 3.2 Multiple Declarations And Selectors

A single rule can set many properties, and a single selector list can target multiple elements at once.

```css
h1, h2, h3 {
  color: navy;
  font-family: sans-serif;
  margin-bottom: 0.5em;
}
```

Here, `h1`, `h2`, and `h3` are separated by commas, meaning "apply this rule to all of these." Each declaration ends in a semicolon — while the last one is technically optional, always including it prevents bugs when you add more declarations later.

---

## 3.3 Comments

CSS comments use `/* ... */` and are ignored by the browser. They're useful for documenting sections of a stylesheet or temporarily disabling a rule.

```css
/* Header styles */
h1 {
  color: navy; /* brand color */
}
```

CSS does not support single-line `//` comments — only the `/* */` block syntax.

---

## 3.4 Whitespace And Formatting

CSS ignores extra whitespace, tabs, and line breaks between tokens, so formatting is a matter of convention rather than a strict rule. That said, consistent formatting makes stylesheets far easier to read and maintain. A common style:

```css
selector {
  property: value;
  property: value;
}
```

Both of the following are valid, but the first is far more readable in a real project:

```css
h1 { color: navy; font-size: 2rem; }

h1{color:navy;font-size:2rem;}
```

[Previous](./[2]-Adding-CSS.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[4]-Cascade-Specificity-and-Inheritance.md)
