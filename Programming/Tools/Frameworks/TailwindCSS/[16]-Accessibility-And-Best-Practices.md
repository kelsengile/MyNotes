[Previous](./[15]-Component-Libraries.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[17]-Performance-And-Production.md)

*Practical Use*

# Lesson 16 - Accessibility And Best Practices

## 16.1 Semantic HTML first

Tailwind controls how things *look*. It cannot add meaning. Screen readers, search engines, and keyboards depend on the **right HTML element**, so choose the element for what it *is* and style it for how it should look.

**Preflight strips default styling**, so an `<h1>` looks like normal text. That does not make it okay to use `<div>` or `<p>` for headings. Style the correct tag:

```html
<!-- ❌ Looks like a heading but isn't one -->
<div class="text-3xl font-bold">Pricing</div>

<!-- ✅ Real heading, styled -->
<h1 class="text-3xl font-bold">Pricing</h1>
```

**Core rules**

| Use | Instead of |
|---|---|
| `<button>` for actions | `<div onclick>` |
| `<a href>` for navigation | `<span>` with a click handler |
| `<nav>`, `<main>`, `<header>`, `<footer>`, `<section>` | Generic `<div>`s everywhere |
| `<ul>` and `<li>` for lists | Lines separated by `<br>` |
| `<label for>` for each input | Placeholder text as the only label |
| Headings in order (`h1`, then `h2`, then `h3`) | Skipping levels for visual size |

```html
<label for="email" class="block text-sm font-medium">Email</label>
<input id="email" type="email" class="mt-1 w-full rounded-md border px-3 py-2" />
```

**Images** need `alt` text that describes their purpose. Decorative images use `alt=""`.

**Set the page language** so screen readers pronounce text correctly: `<html lang="en">`.

**Why semantic elements matter for Tailwind users:** a real `<button>` gives you keyboard activation (Enter and Space), focus, and the `disabled` state for free, and your `hover:`, `focus-visible:`, and `disabled:` variants work with it.

---

## 16.2 Focus styles and contrast

**Never remove focus indicators without a replacement.** Keyboard users need to see where they are.

```html
<!-- ❌ Invisible focus -->
<button class="outline-none">Click</button>
```

**In v4, the right tool is `outline-hidden`.** It hides the default outline for mouse users while keeping a visible fallback in high-contrast and forced-colors modes. (`outline-none` in v4 removes the outline entirely.) Pair it with your own focus style:

```html
<button class="rounded-md bg-indigo-600 px-4 py-2 text-white outline-hidden focus-visible:ring-2 focus-visible:ring-indigo-300 focus-visible:ring-offset-2">
  Accessible button
</button>
```

**A good default focus pattern**

```html
<a class="focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600" href="#">
  Link
</a>
```

Use `focus-visible:` so the indicator shows for keyboard use.

**Contrast requirements (WCAG AA)**

| Content | Minimum contrast ratio |
|---|---|
| Normal text | 4.5 : 1 |
| Large text (18pt, or 14pt bold) | 3 : 1 |
| UI components and focus indicators | 3 : 1 |

**Practical color tips**

- `text-gray-400` on white usually **fails**. Use `text-gray-600` or darker for readable text.
- Do not communicate meaning by color alone. Pair red error text with an icon or message.
- Check **both** light and dark themes.
- Test with a contrast checker or the browser's DevTools.

**User preference variants**

| Variant | Responds to |
|---|---|
| `motion-reduce:` / `motion-safe:` | Reduced motion preference |
| `contrast-more:` | Increased contrast preference |
| `forced-colors:` | Windows High Contrast and similar modes |

```html
<div class="animate-bounce motion-reduce:animate-none">Arrow</div>
<p class="text-gray-600 contrast-more:text-gray-900">Text</p>
```

**Target size.** Make clickable areas comfortable, roughly 44 by 44px on touch screens: `min-h-11 min-w-11` or enough padding.

---

## 16.3 Screen reader utilities

**`sr-only`** hides content visually but keeps it available to screen readers. **`not-sr-only`** reverses it (useful with focus).

**Example: an icon-only button**

```html
<button class="rounded-full p-2 hover:bg-gray-100">
  <svg class="size-5" aria-hidden="true" viewBox="0 0 20 20" fill="currentColor">
    <path d="M10 3a1 1 0 011 1v5h5a1 1 0 110 2h-5v5a1 1 0 11-2 0v-5H4a1 1 0 110-2h5V4a1 1 0 011-1z" />
  </svg>
  <span class="sr-only">Add item</span>
</button>
```

The icon is hidden from assistive tech (`aria-hidden="true"`), and the hidden text gives the button its name.

**Example: a skip link.** It lets keyboard users jump past a navigation menu, and it appears only when focused:

```html
<a
  href="#main"
  class="sr-only focus:not-sr-only focus:absolute focus:left-4 focus:top-4 focus:z-50 focus:rounded focus:bg-white focus:px-4 focus:py-2 focus:shadow"
>
  Skip to content
</a>
<main id="main">…</main>
```

**Important difference:**

| Class | Visible to sighted users | Read by screen readers |
|---|---|---|
| `hidden` (`display: none`) | No | **No** |
| `sr-only` | No | **Yes** |
| `invisible` | No | No (usually) |

**Style from ARIA state.** Use `aria-*` variants so your visuals follow the accessibility state automatically:

```html
<button aria-expanded="false" class="group flex items-center gap-2">
  Menu
  <svg class="size-4 transition-transform group-aria-expanded:rotate-180" aria-hidden="true"></svg>
</button>

<button aria-disabled="true" class="aria-disabled:opacity-50 aria-disabled:pointer-events-none">Send</button>
```

**Other helpful attributes:** `aria-label` (name an element), `aria-describedby` (link help text), `aria-live="polite"` (announce updates), and `role` (only when no native element fits).

---

## 16.4 Maintainable class organization

**Keep class lists consistent.** Pick one logical order and stick to it. A common one:

1. Layout and position (`flex`, `grid`, `relative`)
2. Box model (`w-*`, `p-*`, `m-*`, `border`)
3. Typography (`text-*`, `font-*`, `leading-*`)
4. Visuals (`bg-*`, `shadow-*`, `rounded-*`)
5. States (`hover:`, `focus-visible:`)
6. Responsive and theme variants (`md:`, `dark:`)

Do not sort by hand. Let the Prettier plugin do it (Lesson 17).

**Habits that keep a Tailwind codebase healthy**

| Habit | Why |
|---|---|
| Extract components early | Prevent copy-pasted blocks |
| Use theme tokens for brand values | Change a color once |
| Avoid scattered arbitrary values (`w-[317px]`) | Keeps the design scale consistent |
| Write complete class names | Tailwind can detect them |
| Prefer `gap-*` over `space-*` | More reliable for wrapping layouts |
| Use `focus-visible:` over `focus:` for rings | Better keyboard-only experience |
| Group related variants together | Easier to read |
| Limit `!important` | Avoid specificity fights |
| Comment unusual choices | Future you will thank you |

**Responsive code that stays readable**

```html
<div class="
  grid grid-cols-1 gap-4
  sm:grid-cols-2
  lg:grid-cols-3 lg:gap-6
">
```

**Accessibility checklist before shipping**

1. Can you complete every task using only the keyboard?
2. Is focus always visible?
3. Is contrast sufficient in light and dark?
4. Do images have appropriate `alt` text?
5. Do form fields have labels?
6. Does the page work at 200% zoom and on a narrow screen?
7. Is motion reduced for users who ask for it?

**Tools:** browser DevTools accessibility panel, Lighthouse, axe DevTools, and testing with a screen reader (VoiceOver, NVDA, or TalkBack).

---

[Previous](./[15]-Component-Libraries.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[17]-Performance-And-Production.md)