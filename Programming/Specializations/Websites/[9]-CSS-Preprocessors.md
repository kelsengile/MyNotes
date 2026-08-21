[Previous](./[8]-TypeScript-Basics.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[10]-CSS-Architecture-and-BEM.md)

*Styling at Scale*

# Lesson 9 - CSS Preprocessors (Sass/SCSS)

## 9.1 Why Preprocessors Exist

Plain CSS has no variables (until relatively recently), no nesting, and no way to reuse chunks of styles without copy-pasting. A **preprocessor** like **Sass** (Syntactically Awesome Style Sheets) adds these features on top of CSS, then compiles the result down into regular CSS that browsers can read. **SCSS** is Sass's CSS-like syntax and is the far more commonly used of Sass's two syntaxes today.

---

## 9.2 Variables

```scss
$primary-color: #3498db;
$spacing-unit: 8px;

.button {
  background-color: $primary-color;
  padding: $spacing-unit * 2;
}
```

Native CSS now has custom properties (`--primary-color: #3498db;`) which overlap in purpose, but Sass variables are resolved at compile time and can be used in more contexts, like arithmetic (`$spacing-unit * 2`).

---

## 9.3 Nesting

```scss
.card {
  padding: 16px;

  .card-title {
    font-weight: bold;
  }

  &:hover {
    box-shadow: 0 2px 8px rgba(0,0,0,0.1);
  }
}
```

Nesting mirrors your HTML structure, keeping related styles visually grouped. The `&` refers to the parent selector, commonly used for pseudo-classes like `:hover` or modifier classes.

---

## 9.4 Partials and Imports

Large stylesheets can be split into **partials** (files prefixed with `_`, e.g. `_variables.scss`) and combined with `@use`:

```scss
// _variables.scss
$primary-color: #3498db;

// main.scss
@use "variables";
.button { color: variables.$primary-color; }
```

This mirrors how ES Modules organize JavaScript (Lesson 6) — small, focused files composed together.

---

## 9.5 Mixins

A **mixin** is a reusable block of styles, optionally parameterized:

```scss
@mixin flex-center($direction: row) {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: $direction;
}

.container {
  @include flex-center(column);
}
```

Mixins avoid repeating the same declarations across many rules, similar in spirit to a function in JavaScript.

---

[Previous](./[8]-TypeScript-Basics.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[10]-CSS-Architecture-and-BEM.md)
