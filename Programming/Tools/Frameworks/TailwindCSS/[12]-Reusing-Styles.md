[Previous](./[11]-Theme-Customization.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[13]-Plugins-And-Typography.md)

*Customization*

# Lesson 12 - Reusing Styles

## 12.1 Components over classes

When a group of classes repeats, the best fix is usually not a new CSS class. It is a **component** in whatever templating system you already use. The utilities stay in one place, and the component is reused.

**React**

```jsx
function Button({ children }) {
  return (
    <button className="rounded-lg bg-indigo-600 px-4 py-2 font-semibold text-white hover:bg-indigo-500">
      {children}
    </button>
  );
}

export default function Page() {
  return (
    <>
      <Button>Save</Button>
      <Button>Publish</Button>
    </>
  );
}
```

**Vue**

```vue
<!-- BaseButton.vue -->
<template>
  <button class="rounded-lg bg-indigo-600 px-4 py-2 font-semibold text-white hover:bg-indigo-500">
    <slot />
  </button>
</template>
```

**Without a framework**, use template partials or includes (Jinja, Blade, Liquid, Eleventy, Astro, and so on) the same way.

**Why components beat CSS classes**

- The *markup and styles* are reused together, not just the styles.
- You change the design in one place.
- Props and slots allow variations (sizes, colors).

**Loops count as reuse too.** If the repetition is just a list, render it from data:

```jsx
{products.map((p) => (
  <li key={p.id} className="rounded-lg border p-4">{p.name}</li>
))}
```

The classes are written once, even if the list has a hundred items.

---

## 12.2 Extracting with @apply

When components are not an option (for example you are styling HTML you cannot edit, like CMS output or a third-party widget), use `@apply` to compose utilities inside your own CSS class.

```css
@import "tailwindcss";

.btn-primary {
  @apply rounded-lg bg-indigo-600 px-4 py-2 font-semibold text-white;
  @apply hover:bg-indigo-500 focus-visible:outline-2 focus-visible:outline-offset-2;
}
```

```html
<button class="btn-primary">Save</button>
```

Variants (`hover:`, `md:`, `dark:`) work inside `@apply`.

**Styling raw HTML you do not control**

```css
.cms-content a {
  @apply text-blue-600 underline hover:text-blue-800;
}
.cms-content h2 {
  @apply mt-8 mb-2 text-2xl font-bold;
}
```

**Using theme values without a utility.** Variables are available as CSS functions too:

```css
.custom-box {
  border-radius: var(--radius-lg);
  padding: calc(var(--spacing) * 4);
  color: var(--color-gray-700);
}
```

**Using `@apply` in framework style blocks (v4).** In Vue, Svelte, or CSS Modules files, Tailwind is not automatically in scope. Import a *reference* to your main stylesheet so `@apply` knows your theme and utilities, without duplicating the CSS:

```vue
<style scoped>
@reference "../../app.css";

h1 {
  @apply text-2xl font-bold;
}
</style>
```

**Be cautious.** Overusing `@apply` brings back the problems of traditional CSS: naming things, jumping between files, and growing stylesheets. Treat it as a tool for exceptions, not the default.

---

## 12.3 Layers and custom CSS

You will sometimes need styles that Tailwind does not offer. Put them in the right place.

**`@layer base`**: default styles for plain HTML elements.

```css
@layer base {
  h1 {
    @apply text-3xl font-bold;
  }
  a {
    @apply text-blue-600 underline;
  }
}
```

**`@layer components`**: your own component classes (cards, buttons). Utilities still override them, so `class="card p-2"` can change a card's padding.

```css
@layer components {
  .card {
    @apply rounded-xl bg-white p-6 shadow-md;
  }
}
```

**`@utility`**: define a custom *utility* that works with variants such as `hover:`, `md:`, and `dark:` (v4):

```css
@utility text-shadow-soft {
  text-shadow: 0 2px 4px rgb(0 0 0 / 0.2);
}
```

```html
<h1 class="text-shadow-soft md:text-shadow-none">Title</h1>
```

**A utility that accepts a value:**

```css
@utility tab-* {
  tab-size: --value(integer);
}
```

```html
<pre class="tab-4">code</pre>
```

**`@custom-variant`** adds your own variants:

```css
@custom-variant theme-midnight (&:where([data-theme=midnight], [data-theme=midnight] *));
```

```html
<div class="theme-midnight:bg-slate-900"></div>
```

**Note for v3 users:** in v3 you used `@layer utilities { … }` for custom utilities. In v4, use `@utility` instead, because `@layer utilities` no longer supports variants.

**Order of priority** (lowest to highest): theme, base, components, utilities. Your custom code placed in `base` and `components` is easy for utilities to override, which is what you want.

---

## 12.4 Avoiding duplication

A practical checklist for deciding how to reuse styles:

| Situation | Best approach |
|---|---|
| Same markup and styles repeat in many places | **Component** |
| A list of similar items | **Loop** over data |
| Repeated classes inside one file or one element group | **Multi-cursor edit** or keep the duplication |
| A repeated value (color, font, spacing) | **Theme token** (`@theme`) |
| A reusable piece with variants (sizes, colors) | **Component with variant helper** (Lesson 14) |
| Markup you cannot edit | **`@apply`** in your CSS |
| A missing CSS feature | **`@utility`** or `@layer` |

**Rule of three.** Do not abstract until you have written the same thing three times. Early abstractions often guess wrong, and wrong abstractions cost more than a bit of duplication.

**Be careful what you reuse.** Two buttons that look the same today may need to diverge tomorrow. Share *design tokens* widely, but share *components* only when the things are really the same thing.

**A typical project ends up with:**

1. A small theme (`@theme`) with brand colors, fonts, and spacing tweaks.
2. A handful of components (`Button`, `Card`, `Input`) with utilities inside.
3. Almost no custom CSS beyond that.

---

[Previous](./[11]-Theme-Customization.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[13]-Plugins-And-Typography.md)