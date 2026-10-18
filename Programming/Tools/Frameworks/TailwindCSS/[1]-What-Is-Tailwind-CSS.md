[Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[2]-Utility-First-Fundamentals.md)

*Basics*

# Lesson 1 - What Is Tailwind CSS

## 1.1 Utility-first explained

Traditional CSS asks you to invent a name for every piece of UI (`.card`, `.button`), then write the styles for that name in a separate file. **Utility-first** CSS flips this: you build the design directly in your HTML using small classes that each do exactly one thing.

**Traditional CSS**

```html
<button class="btn-primary">Save</button>
```

```css
.btn-primary {
  background-color: #4f46e5;
  color: white;
  padding: 0.5rem 1rem;
  border-radius: 0.5rem;
  font-weight: 600;
}
```

**Tailwind (utility-first)**

```html
<button class="rounded-lg bg-indigo-600 px-4 py-2 font-semibold text-white">
  Save
</button>
```

Each class maps to one CSS declaration:

| Class | CSS it generates |
|---|---|
| `rounded-lg` | `border-radius: 0.5rem` |
| `bg-indigo-600` | `background-color` set to the indigo 600 color |
| `px-4` | `padding-left` and `padding-right` of `1rem` |
| `py-2` | `padding-top` and `padding-bottom` of `0.5rem` |
| `font-semibold` | `font-weight: 600` |
| `text-white` | `color: white` |

**Why this approach works**

- **No naming.** You never spend time deciding what to call something.
- **Local changes.** Editing a class only affects that element, so you cannot accidentally break another page.
- **CSS stops growing.** Reusing utilities means your stylesheet does not get bigger with every new feature.
- **Constraints.** Values come from a shared scale (spacing, colors, sizes), so designs stay consistent.

---

## 1.2 Tailwind vs Bootstrap and plain CSS

| | Plain CSS | Bootstrap | Tailwind CSS |
|---|---|---|---|
| Approach | You write everything | Pre-built components | Low-level utility classes |
| Example class | `.card` (you define it) | `.card`, `.btn btn-primary` | `rounded-xl p-6 shadow-md` |
| Default look | None | Recognizable "Bootstrap look" | None, you design it |
| Customization | Unlimited, manual | Override or use Sass variables | Theme tokens plus utilities |
| Speed to first result | Slow | Very fast | Fast once utilities are familiar |
| Risk | Growing, tangled stylesheets | Sites look alike | Long class lists in markup |
| Final CSS size | Whatever you write | Large unless trimmed | Small, only used classes |

**Key idea:** Bootstrap gives you *finished components* you tweak. Tailwind gives you *building blocks* so you can create any design without fighting defaults. Tailwind does not ship a ready-made button or navbar. You build them (or use a component library, covered in Lesson 15).

Tailwind is not a replacement for knowing CSS. Every utility is a CSS property, so understanding CSS makes you much faster with Tailwind.

---

## 1.3 Installing with Vite or the CLI

**Option A: Vite (recommended for apps)**

1. Create a project and install Tailwind:

```bash
npm create vite@latest my-project
cd my-project
npm install tailwindcss @tailwindcss/vite
```

2. Add the plugin to `vite.config.js`:

```js
import { defineConfig } from "vite";
import tailwindcss from "@tailwindcss/vite";

export default defineConfig({
  plugins: [tailwindcss()],
});
```

3. Import Tailwind in your main CSS file (for example `src/style.css`):

```css
@import "tailwindcss";
```

4. Make sure that CSS file is loaded (via `import "./style.css"` in your JavaScript entry, or a `<link>` tag), then start the dev server:

```bash
npm run dev
```

**Option B: Tailwind CLI (no bundler)**

```bash
npm install tailwindcss @tailwindcss/cli
```

Create `src/input.css`:

```css
@import "tailwindcss";
```

Build and watch for changes:

```bash
npx @tailwindcss/cli -i ./src/input.css -o ./src/output.css --watch
```

Then link the generated file in your HTML:

```html
<link href="./src/output.css" rel="stylesheet" />
```

**Option C: Play CDN (experiments only)**

For quick tests, with no build step:

```html
<script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
```

Do not use the CDN script in production. It compiles in the browser and is slower.

**Note:** Tailwind v4 targets modern browsers (roughly Safari 16.4+, Chrome 111+, Firefox 128+). If you must support older browsers, stay on v3.

**Quick test:** after installing, add this to your HTML. If the text is large, bold, and blue, it works.

```html
<h1 class="text-3xl font-bold text-blue-600">Hello Tailwind</h1>
```

---

## 1.4 How Tailwind generates CSS

Tailwind does not ship a giant stylesheet containing every possible class. Instead, it:

1. **Scans** your project files (HTML, JS, TS, JSX, Vue, Svelte, and so on) for strings that look like class names.
2. **Generates** CSS only for the utilities it finds.
3. **Outputs** one small stylesheet.

In v4, this detection is automatic. It ignores files in `.gitignore`, `node_modules`, and binary files. To include an extra location, use `@source`:

```css
@import "tailwindcss";
@source "../node_modules/some-ui-library";
```

**Tailwind's CSS layers**

When you write `@import "tailwindcss";`, you get these cascade layers, in order:

| Layer | Purpose |
|---|---|
| `theme` | Design tokens as CSS variables (colors, spacing, fonts) |
| `base` | **Preflight**, a modern reset (removes default margins, sets `box-sizing: border-box`, unstyles headings and lists) |
| `components` | A place for your own component classes |
| `utilities` | All the utility classes, which win over earlier layers |

**Important: write complete class names.** Tailwind finds classes by *reading text*, not by running your code. This breaks:

```js
// ❌ Tailwind never sees "text-red-500" or "text-green-500"
const cls = `text-${color}-500`;
```

This works:

```js
// ✅ Full class names appear in the source
const cls = isError ? "text-red-500" : "text-green-500";
```

Remember this rule. It causes the most common "my class does nothing" bug.

---

[Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[2]-Utility-First-Fundamentals.md)