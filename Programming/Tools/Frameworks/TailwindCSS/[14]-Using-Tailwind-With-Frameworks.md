[Previous](./[13]-Plugins-And-Typography.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[15]-Component-Libraries.md)

*Practical Use*

# Lesson 14 - Using Tailwind With Frameworks

## 14.1 Tailwind with React and Next.js

**React with Vite**

```bash
npm create vite@latest my-app -- --template react
cd my-app
npm install tailwindcss @tailwindcss/vite
```

```js
// vite.config.js
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import tailwindcss from "@tailwindcss/vite";

export default defineConfig({
  plugins: [react(), tailwindcss()],
});
```

```css
/* src/index.css */
@import "tailwindcss";
```

Make sure `index.css` is imported in `main.jsx` with `import "./index.css";`.

**Next.js**

The easiest route is the setup wizard, which offers Tailwind as a choice:

```bash
npx create-next-app@latest my-next-app
```

For an existing project, install the PostCSS integration:

```bash
npm install tailwindcss @tailwindcss/postcss postcss
```

```js
// postcss.config.mjs
export default {
  plugins: {
    "@tailwindcss/postcss": {},
  },
};
```

```css
/* app/globals.css */
@import "tailwindcss";
```

```jsx
// app/layout.js
import "./globals.css";

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body className="bg-white text-gray-900 antialiased">{children}</body>
    </html>
  );
}
```

**In JSX, use `className` instead of `class`:**

```jsx
export default function Hero() {
  return (
    <section className="mx-auto max-w-3xl px-6 py-24 text-center">
      <h1 className="text-5xl font-bold tracking-tight">Hello, Tailwind</h1>
      <p className="mt-4 text-lg text-gray-600">Built with Next.js.</p>
    </section>
  );
}
```

**Tip:** with the App Router, Tailwind works in both Server and Client Components because it is only CSS classes.

---

## 14.2 Tailwind with Vue and Svelte

Both use the Vite plugin, so setup is the same as for React.

**Vue (Vite)**

```bash
npm create vue@latest my-vue-app
cd my-vue-app
npm install tailwindcss @tailwindcss/vite
```

Add `tailwindcss()` to the `plugins` array in `vite.config.js` and put `@import "tailwindcss";` in your main CSS file, then import that file in `main.js`.

```vue
<script setup>
import { ref } from "vue";
const count = ref(0);
</script>

<template>
  <button
    class="rounded-lg bg-emerald-600 px-4 py-2 font-medium text-white hover:bg-emerald-500"
    @click="count++"
  >
    Clicked {{ count }} times
  </button>
</template>
```

**Svelte / SvelteKit**

```bash
npx sv create my-svelte-app
cd my-svelte-app
npm install tailwindcss @tailwindcss/vite
```

Add the plugin to `vite.config.js` (before `sveltekit()`), and import your CSS in `src/routes/+layout.svelte`:

```svelte
<script>
  import "../app.css";
  let { children } = $props();
</script>

{@render children()}
```

```svelte
<button class="rounded-lg bg-orange-500 px-4 py-2 text-white hover:bg-orange-400">
  Svelte button
</button>
```

The `sv create` wizard can also add Tailwind for you through its add-ons.

**Using `@apply` in `<style>` blocks** needs a reference to your main stylesheet (Lesson 12):

```svelte
<style>
  @reference "../app.css";
  h1 { @apply text-2xl font-bold; }
</style>
```

**Other frameworks.** Nuxt, Astro, Remix, and Laravel all have Tailwind guides in the official docs. Most use either the Vite plugin or the PostCSS plugin.

---

## 14.3 Class composition helpers

In components, class lists get assembled from props and state. Two small libraries make this clean and safe.

**`clsx`** builds class strings from conditions:

```bash
npm install clsx tailwind-merge
```

```js
import clsx from "clsx";

clsx("px-4 py-2", isActive && "bg-blue-600", { "opacity-50": disabled });
// → "px-4 py-2 bg-blue-600 opacity-50"
```

**`tailwind-merge`** resolves conflicts, so that the *last* conflicting utility wins, which solves the problem from Lesson 2.4:

```js
import { twMerge } from "tailwind-merge";

twMerge("px-2 py-1 bg-red-500", "px-4 bg-blue-500");
// → "py-1 px-4 bg-blue-500"
```

Use a version of `tailwind-merge` that supports your Tailwind version (v3 of `tailwind-merge` is for Tailwind v4).

**The `cn` helper** combines both, and is a very common pattern:

```js
// lib/utils.js
import { clsx } from "clsx";
import { twMerge } from "tailwind-merge";

export function cn(...inputs) {
  return twMerge(clsx(inputs));
}
```

**Why this makes components flexible**

```jsx
function Button({ className, ...props }) {
  return (
    <button
      className={cn("rounded-lg bg-indigo-600 px-4 py-2 text-white", className)}
      {...props}
    />
  );
}

// The caller can override default classes safely
<Button className="bg-red-600 px-8">Delete</Button>
```

Without `twMerge`, both `bg-indigo-600` and `bg-red-600` would be present and the winner would be unpredictable.

**Variants with `class-variance-authority` (cva)**

```bash
npm install class-variance-authority
```

```js
import { cva } from "class-variance-authority";

const button = cva("rounded-lg font-medium transition-colors", {
  variants: {
    intent: {
      primary: "bg-indigo-600 text-white hover:bg-indigo-500",
      danger: "bg-red-600 text-white hover:bg-red-500",
    },
    size: {
      sm: "px-3 py-1.5 text-sm",
      md: "px-4 py-2 text-base",
    },
  },
  defaultVariants: { intent: "primary", size: "md" },
});

button({ intent: "danger", size: "sm" });
```

---

## 14.4 Conditional classes

**Ternary for two states**

```jsx
<button className={isActive ? "bg-blue-600 text-white" : "bg-gray-100 text-gray-700"}>
  Tab
</button>
```

**`clsx` / `cn` for several conditions**

```jsx
<li
  className={cn(
    "rounded px-3 py-2",
    isSelected && "bg-blue-100 font-semibold",
    isDisabled && "cursor-not-allowed opacity-50"
  )}
>
  Item
</li>
```

**Lookup objects for named variants.** This is the safe way to turn a prop into classes:

```jsx
const colorClasses = {
  blue: "bg-blue-600 text-white",
  red: "bg-red-600 text-white",
  gray: "bg-gray-200 text-gray-900",
};

function Badge({ color = "gray", children }) {
  return <span className={`rounded px-2 py-1 text-xs ${colorClasses[color]}`}>{children}</span>;
}
```

Every complete class name appears in the source, so Tailwind detects it.

**Never build class names from fragments:**

```jsx
// ❌ Tailwind cannot detect these
<span className={`bg-${color}-600`}>...</span>
```

**Style from data attributes instead.** Let state live in an attribute and style it with variants. This keeps JavaScript out of the styling decisions:

```jsx
<button data-active={isActive} className="bg-gray-100 data-[active=true]:bg-blue-600 data-[active=true]:text-white">
  Tab
</button>
```

```vue
<!-- Vue: dynamic classes -->
<div :class="['rounded p-4', isError ? 'bg-red-100 text-red-800' : 'bg-green-100 text-green-800']"></div>
```

```svelte
<!-- Svelte 5: class arrays and objects -->
<div class={["rounded p-4", isError && "bg-red-100"]}></div>
```

**Rule:** conditions choose between *complete, literal class names*. They never assemble class names out of pieces.

---

[Previous](./[13]-Plugins-And-Typography.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[15]-Component-Libraries.md)