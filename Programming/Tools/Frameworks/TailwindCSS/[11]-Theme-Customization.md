[Previous](./[10]-Animations-And-Transitions.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[12]-Reusing-Styles.md)

*Customization*

# Lesson 11 - Theme Customization

## 11.1 Design tokens and the theme

A **design token** is a named design decision, such as "brand blue" or "base spacing". Tailwind's **theme** is its set of tokens. In v4, tokens are plain **CSS variables** declared inside an `@theme` block.

```css
@import "tailwindcss";

@theme {
  --color-brand: #4f46e5;
  --font-display: "Poppins", sans-serif;
  --breakpoint-3xl: 120rem;
}
```

When you declare a variable in `@theme`, Tailwind does two things:

1. Creates **utility classes** from it (`bg-brand`, `text-brand`, `font-display`, `3xl:`).
2. Exposes the value as a **CSS variable** you can use anywhere: `color: var(--color-brand);`.

**Theme namespaces.** The variable's prefix tells Tailwind what kind of utilities to make:

| Namespace | Creates |
|---|---|
| `--color-*` | `bg-*`, `text-*`, `border-*`, `ring-*`, and so on |
| `--font-*` | `font-*` family utilities |
| `--text-*` | `text-*` font sizes |
| `--font-weight-*` | `font-*` weights |
| `--tracking-*` | `tracking-*` |
| `--leading-*` | `leading-*` |
| `--spacing` | The base unit for `p-*`, `m-*`, `w-*`, `gap-*`, and so on |
| `--breakpoint-*` | Responsive variants |
| `--container-*` | `max-w-*` sizes and container query variants |
| `--radius-*` | `rounded-*` |
| `--shadow-*` | `shadow-*` |
| `--ease-*` | `ease-*` |
| `--animate-*` | `animate-*` |

**Why this matters:** changing a token changes every place that uses it. Update `--color-brand` once and the whole site follows.

**Where the full default theme lives.** Tailwind's built-in tokens are in its own `theme.css`. You can browse them in the docs to see names like `--color-blue-500`.

**Coming from v3?** Customization used to live in `tailwind.config.js`. You can still load an old config with `@config "./tailwind.config.js";`, but new projects should use `@theme`.

---

## 11.2 Custom colors and fonts

**Adding a color palette**

```css
@theme {
  --color-brand-50: oklch(0.97 0.02 270);
  --color-brand-100: oklch(0.93 0.04 270);
  --color-brand-500: oklch(0.58 0.2 270);
  --color-brand-600: oklch(0.52 0.22 270);
  --color-brand-900: oklch(0.3 0.12 270);
}
```

Use them like any built-in color:

```html
<button class="bg-brand-500 text-white hover:bg-brand-600">Brand button</button>
<p class="text-brand-900 border-brand-100">...</p>
```

Hex, `rgb()`, `hsl()`, and `oklch()` values all work. Opacity modifiers still work (`bg-brand-500/20`).

**Single-value colors** have no shade suffix:

```css
@theme {
  --color-surface: #f8fafc;
}
```

```html
<div class="bg-surface"></div>
```

**Custom fonts**

1. Load the font. Either use a `<link>` tag (for example Google Fonts) in your HTML, or `@font-face` in CSS:

```css
@font-face {
  font-family: "Inter";
  src: url("/fonts/Inter-Variable.woff2") format("woff2");
  font-weight: 100 900;
  font-display: swap;
}
```

2. Register it in the theme:

```css
@theme {
  --font-sans: "Inter", ui-sans-serif, system-ui, sans-serif;
  --font-display: "Playfair Display", ui-serif, Georgia, serif;
}
```

Overriding `--font-sans` changes the default body font site-wide. A new name like `--font-display` creates `font-display`.

```html
<h1 class="font-display text-5xl">A serif heading</h1>
```

**Always include fallback fonts** so text is readable while the custom font loads.

---

## 11.3 Spacing and breakpoints

**The spacing system.** All spacing-based utilities (`p-*`, `m-*`, `gap-*`, `w-*`, `h-*`, `size-*`, `inset-*`, and more) multiply a single base variable:

```css
@theme {
  --spacing: 0.25rem; /* the default */
}
```

So `p-4` equals `calc(var(--spacing) * 4)`. Changing the base rescales the whole system. For example `--spacing: 0.3rem;` makes every spacing utility 20% larger.

**Custom sizes.** Add named values for container widths:

```css
@theme {
  --container-4xl: 56rem;
  --container-page: 72rem;
}
```

```html
<main class="mx-auto max-w-page px-4"></main>
```

**Custom font sizes**, optionally with a line height:

```css
@theme {
  --text-display: 4.5rem;
  --text-display--line-height: 1;
}
```

```html
<h1 class="text-display font-bold">Huge title</h1>
```

**Custom breakpoints** (also see Lesson 8):

```css
@theme {
  --breakpoint-xs: 30rem;
  --breakpoint-3xl: 120rem;
}
```

**Other useful customizations**

```css
@theme {
  --radius-card: 1.25rem;
  --shadow-soft: 0 8px 30px rgb(0 0 0 / 0.08);
  --ease-snappy: cubic-bezier(0.2, 0, 0, 1);
}
```

```html
<div class="rounded-card shadow-soft transition ease-snappy"></div>
```

---

## 11.4 Extending vs overriding

**Extending (default behavior).** Adding variables in `@theme` **adds to** Tailwind's defaults. Everything built in still works.

```css
@theme {
  --color-brand: #4f46e5;  /* bg-brand is new; bg-blue-500 still exists */
}
```

**Overriding a value.** Declare a variable with the same name as a default:

```css
@theme {
  --color-blue-500: #0ea5e9;  /* replaces the default blue-500 */
}
```

**Resetting a whole namespace.** Set the wildcard to `initial` to remove all defaults in that group, then define your own:

```css
@theme {
  --color-*: initial;
  --color-white: #fff;
  --color-black: #000;
  --color-primary: #4f46e5;
  --color-secondary: #14b8a6;
}
```

Now only those colors exist. `bg-red-500` no longer works, which is useful for strict design systems.

**Removing every default token:**

```css
@theme {
  --*: initial;
  /* define everything yourself */
}
```

**Two helpful modifiers**

| Syntax | Purpose |
|---|---|
| `@theme static { … }` | Always output the variables in the CSS. By default, Tailwind only emits variables that your code actually uses. |
| `@theme inline { … }` | Use the variable's value directly in utilities instead of referencing the variable. Needed when a token points to *another* variable (see Lesson 9.4). |

**Rule of thumb:** extend for small additions and overrides, and reset a namespace when you want to enforce a limited palette.

---

[Previous](./[10]-Animations-And-Transitions.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[12]-Reusing-Styles.md)