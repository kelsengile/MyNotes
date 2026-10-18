[Previous](./[8]-Responsive-Design.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[10]-Animations-And-Transitions.md)

*Responsive And Interactive Design*

# Lesson 9 - Dark Mode

## 9.1 The dark variant

The `dark:` variant applies a utility only when dark mode is active.

```html
<div class="bg-white text-gray-900 dark:bg-gray-900 dark:text-gray-100">
  <h2 class="text-xl font-semibold">Hello</h2>
  <p class="text-gray-600 dark:text-gray-400">Adapts to the theme.</p>
</div>
```

**Pattern:** write the **light** styles normally, then add `dark:` versions only for what changes. Elements that look fine in both themes need nothing extra.

**By default**, `dark:` follows the operating system setting through the `prefers-color-scheme: dark` media query. That requires no JavaScript. The next section shows how to let users choose manually.

**Combining with other variants:**

```html
<button class="bg-blue-600 hover:bg-blue-700 dark:bg-blue-500 dark:hover:bg-blue-400">Save</button>
<div class="md:dark:bg-gray-800">Only on md+ in dark mode</div>
```

---

## 9.2 System preference vs manual toggle

To add a theme toggle, switch from the media query to a **class** (or attribute) you control.

**Step 1: redefine the `dark` variant in your CSS**

```css
@import "tailwindcss";

@custom-variant dark (&:where(.dark, .dark *));
```

Now `dark:` utilities apply when the element or any ancestor has the `dark` class, normally set on `<html>`.

(Prefer a data attribute? Use `@custom-variant dark (&:where([data-theme=dark], [data-theme=dark] *));`.)

**Step 2: set the class early to avoid a flash of the wrong theme.** Put this inline script in `<head>`, before your CSS finishes loading:

```html
<script>
  const saved = localStorage.getItem("theme"); // "light", "dark", or null (follow system)
  const prefersDark = window.matchMedia("(prefers-color-scheme: dark)").matches;
  document.documentElement.classList.toggle("dark", saved === "dark" || (!saved && prefersDark));
</script>
```

**Step 3: add a toggle button**

```html
<button id="theme-toggle" class="rounded-md bg-gray-200 px-3 py-1 dark:bg-gray-700 dark:text-white">
  Toggle theme
</button>

<script>
  document.getElementById("theme-toggle").addEventListener("click", () => {
    const isDark = document.documentElement.classList.toggle("dark");
    localStorage.setItem("theme", isDark ? "dark" : "light");
  });
</script>
```

**Three-way choice (Light, Dark, System):** store `"light"`, `"dark"`, or remove the key (`localStorage.removeItem("theme")`) for system, and run the same startup logic. Listen for changes with `matchMedia("(prefers-color-scheme: dark)").addEventListener("change", …)` if you want live updates in System mode.

**Summary**

| Method | Setup | User control |
|---|---|---|
| Default (media query) | None | Only via OS setting |
| Class or attribute | `@custom-variant` plus a script | Full toggle |

---

## 9.3 Designing for both themes

A good dark theme is **designed**, not just inverted.

**Guidelines**

- **Avoid pure black on pure white.** Dark backgrounds like `gray-900` or `gray-950` and light text like `gray-100` are easier on the eyes than `#000` and `#fff`.
- **Reduce saturation** of bright colors on dark backgrounds. Vivid `blue-600` can glare, whereas `blue-400` often looks better.
- **Express elevation with lighter surfaces**, not shadows. Shadows barely show on dark backgrounds, so raised cards usually use a slightly lighter background (`dark:bg-gray-800` on a `dark:bg-gray-900` page) and a subtle border.
- **Check borders and dividers.** `border-gray-200` looks harsh in dark mode, so use `dark:border-gray-700`.
- **Handle images.** Consider `dark:brightness-90`, or supply different logo files with `hidden dark:block` and `block dark:hidden`.
- **Verify contrast** in both themes. Body text should reach at least 4.5:1.

**Tell the browser which scheme you use.** This styles scrollbars and form controls:

```html
<html class="scheme-light dark:scheme-dark">
```

**A complete card example**

```html
<article class="rounded-xl border border-gray-200 bg-white p-6 shadow-sm
                dark:border-gray-700 dark:bg-gray-800 dark:shadow-none">
  <h3 class="font-semibold text-gray-900 dark:text-white">Title</h3>
  <p class="mt-2 text-gray-600 dark:text-gray-300">Readable in both modes.</p>
  <a href="#" class="mt-4 inline-block text-blue-600 hover:underline dark:text-blue-400">Read more</a>
</article>
```

**Test both themes** during development, not at the end.

---

## 9.4 Color tokens

Repeating `bg-white dark:bg-gray-900` everywhere is tedious. **Semantic color tokens** solve this: define each role once for each theme, then use a single class.

**Step 1: define theme-specific CSS variables**

```css
@import "tailwindcss";

@custom-variant dark (&:where(.dark, .dark *));

:root {
  --background: oklch(1 0 0);
  --foreground: oklch(0.21 0.03 265);
  --muted: oklch(0.96 0.01 265);
  --muted-foreground: oklch(0.55 0.03 265);
  --border: oklch(0.92 0.01 265);
  --primary: oklch(0.55 0.22 265);
}

.dark {
  --background: oklch(0.17 0.02 265);
  --foreground: oklch(0.97 0.01 265);
  --muted: oklch(0.25 0.02 265);
  --muted-foreground: oklch(0.7 0.02 265);
  --border: oklch(0.3 0.02 265);
  --primary: oklch(0.7 0.17 265);
}
```

**Step 2: expose them to Tailwind**

```css
@theme inline {
  --color-background: var(--background);
  --color-foreground: var(--foreground);
  --color-muted: var(--muted);
  --color-muted-foreground: var(--muted-foreground);
  --color-border: var(--border);
  --color-primary: var(--primary);
}
```

The `inline` keyword tells Tailwind to use the `var(--background)` reference directly, so when `.dark` changes the variable, the utility updates too.

**Step 3: use semantic classes with no `dark:` needed**

```html
<body class="bg-background text-foreground">
  <div class="rounded-xl border border-border bg-muted p-6">
    <p class="text-muted-foreground">Automatically themed.</p>
    <button class="mt-4 rounded-md bg-primary px-4 py-2 text-white">Action</button>
  </div>
</body>
```

**Why this scales:** adding a third theme (for example "high contrast") means defining one more block of variables, with no changes to your markup. This is the approach used by shadcn/ui (Lesson 15).

---

[Previous](./[8]-Responsive-Design.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[10]-Animations-And-Transitions.md)