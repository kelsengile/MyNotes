[Previous](./[17]-Performance-And-Production.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md)

*Practical Use*

# Lesson 18 - Next Steps With Tailwind CSS

## 18.1 Design systems

A **design system** is a shared set of tokens, components, and rules that keeps a product consistent as it grows. Tailwind is a strong base for one because its theme *is* a token system.

**The building blocks**

| Layer | What it contains | In Tailwind |
|---|---|---|
| Tokens | Colors, type scale, spacing, radius, shadows | `@theme` variables |
| Primitives | Button, Input, Badge, Card | Components using utilities and `cva` |
| Patterns | Forms, navbars, data tables | Compositions of primitives |
| Documentation | Usage rules and examples | Storybook, MDX docs, or a docs site |

**Practical steps**

1. **Define tokens first.** Brand colors, font families, a type scale, and radius and shadow styles go in `@theme`.
2. **Use semantic names.** `bg-primary` and `text-muted-foreground` outlive a rebrand better than `bg-indigo-600` (Lesson 9.4).
3. **Build a few primitives** with variants (Lesson 14.3).
4. **Share the theme.** In a monorepo, put the theme in its own CSS file and import it from each app:

```css
/* packages/ui/theme.css */
@theme {
  --color-brand-500: oklch(0.6 0.2 270);
  --font-sans: "Inter", sans-serif;
}
```

```css
/* apps/web/app.css */
@import "tailwindcss";
@import "../../packages/ui/theme.css";
```

5. **Document and review.** Tools like **Storybook** let you view each component and its variants in isolation.
6. **Limit choices.** Resetting default namespaces (Lesson 11.4) stops people from using off-system values.

**Learn from existing systems:** study shadcn/ui, Radix Themes, and Tailwind Plus to see how others organize tokens and components.

---

## 18.2 Advanced layouts

Once the basics feel natural, combine them for real application layouts.

**An app shell with a fixed sidebar and a scrolling content area**

```html
<div class="grid min-h-dvh grid-cols-1 md:grid-cols-[16rem_1fr]">
  <aside class="hidden border-r bg-gray-50 p-4 md:block">Sidebar</aside>
  <div class="flex flex-col">
    <header class="sticky top-0 z-10 border-b bg-white/80 px-6 py-3 backdrop-blur">Top bar</header>
    <main class="flex-1 p-6">Content</main>
    <footer class="border-t px-6 py-4 text-sm text-gray-500">Footer</footer>
  </div>
</div>
```

**A footer that stays at the bottom even on short pages** (`min-h-dvh` plus `flex-col` plus `flex-1` on `main`) is shown above.

**Masonry-style columns (CSS columns)**

```html
<div class="columns-1 gap-4 sm:columns-2 lg:columns-3">
  <div class="mb-4 break-inside-avoid rounded-lg bg-white p-4 shadow">Card of any height</div>
  <div class="mb-4 break-inside-avoid rounded-lg bg-white p-4 shadow">Another card</div>
</div>
```

**Subgrid: aligning content across cards**

```html
<div class="grid grid-cols-3 gap-4">
  <article class="row-span-3 grid grid-rows-subgrid gap-2 rounded-lg border p-4">
    <h3>Title</h3>
    <p>Description that may vary in length</p>
    <a href="#">Read more</a>
  </article>
  <!-- More cards: titles, descriptions, and links line up across the row -->
</div>
```

**A holy-grail style layout with named grid areas** can be written with an arbitrary value:

```html
<div class="grid min-h-dvh grid-cols-[200px_1fr_200px] grid-rows-[auto_1fr_auto] [grid-template-areas:'header_header_header''nav_main_aside''footer_footer_footer']">
  <header class="[grid-area:header]">Header</header>
  <nav class="[grid-area:nav]">Nav</nav>
  <main class="[grid-area:main]">Main</main>
  <aside class="[grid-area:aside]">Aside</aside>
  <footer class="[grid-area:footer]">Footer</footer>
</div>
```

**Combine tools:** container queries for components (Lesson 8.3), breakpoints for page structure, `sticky` for persistent UI, and `dvh` units so mobile browser toolbars don't cut things off.

---

## 18.3 Animation libraries

Tailwind's transitions and keyframes cover most interface effects. Reach for a library when you need **sequencing, gestures, layout animation, or scroll-driven effects**.

| Library | Good for |
|---|---|
| **Motion** (formerly Framer Motion) | React and JavaScript animations, gestures, layout transitions, exit animations |
| **GSAP** | Complex timelines and scroll-based storytelling |
| **AutoAnimate** | One-line animation of list items being added, removed, or reordered |
| **tw-animate-css** | A set of Tailwind-friendly animation utilities (used by shadcn/ui) |
| **CSS View Transitions and scroll-driven animations** | Native browser features that need no library |

**Motion with Tailwind (React)**

```bash
npm install motion
```

```jsx
import { motion } from "motion/react";

export default function Card() {
  return (
    <motion.div
      initial={{ opacity: 0, y: 16 }}
      animate={{ opacity: 1, y: 0 }}
      whileHover={{ scale: 1.03 }}
      className="rounded-xl bg-white p-6 shadow-md"
    >
      Animated card
    </motion.div>
  );
}
```

Tailwind handles the styling (`className`), and Motion handles the movement.

**Choosing the right tool**

1. **CSS and Tailwind first** for hover, focus, loading, and simple entrances. They are fast and need no JavaScript.
2. **A library** when animation depends on state, sequences, drag, or elements entering and leaving the DOM.
3. **Always respect `prefers-reduced-motion`** (Tailwind's `motion-reduce:` variant, or the library's built-in option).
4. **Animate `transform` and `opacity`** where possible, because they are cheap for the browser. Avoid animating width, height, or large blurs.

---

## 18.4 Project ideas

The fastest way to get good is to build things. Each project below targets specific skills.

| Project | Practices |
|---|---|
| **Personal portfolio** | Layout, typography, responsive images, hover effects |
| **Landing page** (hero, features, pricing, footer) | Sections, grids, gradients, responsive design |
| **Pricing page with monthly and yearly toggle** | `peer`, `has-*`, state variants |
| **Blog with Markdown content** | Typography plugin, dark mode, readable measure |
| **Admin dashboard** | App shell, grids, tables, cards, charts container |
| **Login and sign-up forms** | Forms plugin, validation states, accessibility |
| **Dark-mode component kit** | Semantic color tokens, `cva`, a theme switcher |
| **Clone a site you admire** | Reading designs, spacing and type decisions |
| **E-commerce product page** | Image galleries, aspect ratios, sticky purchase panel |
| **Custom design system package** | Theme sharing, plugins, documentation |

**A suggested learning path**

1. Recreate a simple page (a card, then a full landing page) using only the docs for lookups.
2. Make it responsive on mobile, tablet, and desktop.
3. Add dark mode with semantic tokens.
4. Extract repeating parts into components.
5. Audit keyboard use, focus, and contrast (Lesson 16).
6. Build and check the production CSS size (Lesson 17).

**Keep learning**

- The **official Tailwind CSS documentation** (tailwindcss.com) is excellent and the best reference.
- **Tailwind Play** for quick experiments you can share.
- The **Tailwind CSS GitHub discussions** and community Discord for questions.
- **Open-source projects** built with Tailwind, where you can read real-world class usage.

**Congratulations.** You now understand how Tailwind works from utilities to production. The next step is to build something real.

---

[Previous](./[17]-Performance-And-Production.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md)