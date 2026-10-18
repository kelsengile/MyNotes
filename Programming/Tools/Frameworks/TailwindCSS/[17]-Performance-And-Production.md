[Previous](./[16]-Accessibility-And-Best-Practices.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[18]-Next-Steps-With-Tailwind-CSS.md)

*Practical Use*

# Lesson 17 - Performance And Production

## 17.1 How unused CSS is removed

Tailwind does not generate everything and then delete what you don't use. It only **generates what it finds** in your source files. There is no separate purge step.

**The process**

1. Tailwind scans your project for strings that could be class names.
2. For each match that is a valid utility, it generates the CSS.
3. Everything else is never created.

**Which files are scanned (v4)**

- Automatic detection covers your project files.
- Files listed in `.gitignore` are ignored.
- `node_modules` and binary files (images, videos, archives) are ignored.
- CSS files are not scanned for class names.

**Adding sources.** If classes live somewhere Tailwind does not look (a UI package in `node_modules`, or another folder), register it:

```css
@import "tailwindcss";
@source "../node_modules/@my-company/ui-lib";
```

**Excluding sources:**

```css
@source not "../src/legacy";
```

**Setting the base path** (useful in monorepos):

```css
@import "tailwindcss" source("../src");
```

**Safelisting classes** that never appear literally in your source (for example, classes returned from a database). In v4.1 and later:

```css
@source inline("underline");
@source inline("{hover:,focus:,}bg-red-{50,{100..900..100},950}");
```

The brace syntax expands into many classes at build time. Use safelisting sparingly, because each class adds CSS.

**The reason dynamic class names fail** (Lesson 1.4) is exactly this process: if `bg-${color}-500` never appears as a full string, Tailwind never sees `bg-red-500` and does not generate it.

---

## 17.2 Minification and build size

**Production builds minify automatically** when using the Vite plugin or PostCSS plugin in production mode. v4 uses Lightning CSS for minification and vendor prefixing.

**Build commands**

```bash
# Vite
npm run build

# Tailwind CLI
npx @tailwindcss/cli -i ./src/input.css -o ./dist/output.css --minify
```

**Typical results.** Most production sites end up with a stylesheet of roughly **10 kB or less after compression**, because only used utilities are emitted.

**Checking your output**

```bash
ls -lh dist/output.css
gzip -c dist/output.css | wc -c
```

Vite also prints file sizes (raw and gzip) at the end of `npm run build`.

**What increases CSS size**

| Cause | Fix |
|---|---|
| Large safelists (`@source inline(...)`) | Safelist only what is required |
| Many one-off arbitrary values | Move repeated ones to the theme |
| Huge custom CSS files | Prefer utilities and components |
| Importing big plugin or library CSS | Include only what you use |

**Serve it efficiently**

- Enable **gzip or Brotli** compression on your server or CDN.
- Use long-lived caching with fingerprinted file names (Vite does this automatically).
- Load the stylesheet in the `<head>` so the page renders styled.
- Do not use the Play CDN script in production.

**Build speed.** v4's Rust-based engine makes full builds several times faster and incremental rebuilds close to instant, so there is rarely a reason to tune build performance by hand.

**Runtime cost.** Tailwind itself is just CSS, so there is no JavaScript at runtime. Real-world slowdowns usually come from heavy effects (large blurs, many shadows, huge images), not from the framework.

---

## 17.3 Prettier class sorting

The official **Prettier plugin** automatically orders Tailwind classes in a consistent, recommended order. This ends debates about class order and makes diffs cleaner.

**Install**

```bash
npm install -D prettier prettier-plugin-tailwindcss
```

**Configure (`.prettierrc`)**

```json
{
  "plugins": ["prettier-plugin-tailwindcss"],
  "tailwindStylesheet": "./src/app.css"
}
```

In v4, `tailwindStylesheet` points to your main CSS file so the plugin knows your theme and custom utilities. (v3 projects pointed it at `tailwind.config.js` with `tailwindConfig`.)

**Classes inside helper functions** also get sorted if you list the functions:

```json
{
  "plugins": ["prettier-plugin-tailwindcss"],
  "tailwindStylesheet": "./src/app.css",
  "tailwindFunctions": ["cn", "clsx", "cva"]
}
```

**Before and after**

```html
<!-- Before -->
<button class="text-white px-4 hover:bg-blue-700 bg-blue-600 py-2 rounded-lg">Save</button>

<!-- After running Prettier -->
<button class="rounded-lg bg-blue-600 px-4 py-2 text-white hover:bg-blue-700">Save</button>
```

**How the order works.** Classes are sorted to follow the order they appear in the generated CSS: base and layout first, then more specific things, with variants last. That is why the output order looks the same in every project. Custom classes (names Tailwind does not know) are moved to the front.

**Run it**

```bash
npx prettier --write .
```

You can also set your editor to format on save, and add a CI check with `npx prettier --check .`.

---

## 17.4 Editor tooling

Good tooling is a big part of working fast with utility classes.

**Tailwind CSS IntelliSense (VS Code extension)**

- **Autocomplete** for class names, including your theme values.
- **Hover preview** that shows the CSS a class generates.
- **Linting** that warns about conflicting classes or invalid ones.
- **Color swatches** next to color classes.

**Settings that help**

```json
{
  "editor.quickSuggestions": { "strings": "on" },
  "tailwindCSS.classFunctions": ["cn", "clsx", "cva"],
  "files.associations": { "*.css": "tailwindcss" }
}
```

- `quickSuggestions.strings` turns on autocomplete inside quotes.
- `classFunctions` enables autocomplete inside helper calls such as `cn("...")`.
- The file association helps the editor understand Tailwind-specific at-rules like `@theme` and `@apply` instead of showing false CSS errors.

**Other editors.** JetBrains IDEs (WebStorm and others) have built-in Tailwind support, and Neovim and others can use the `tailwindcss-language-server`.

**More tools**

| Tool | Use |
|---|---|
| **Tailwind Play** (play.tailwindcss.com) | Online sandbox to try and share snippets |
| **Browser DevTools** | Inspect the generated CSS and toggle classes live |
| **Multi-cursor editing** | Edit repeated class lists at once |
| **Emmet-style shortcuts and snippets** | Faster markup writing |
| **The official docs search** | Find any utility or variant quickly |

**Tip for learning:** keep the docs open and use the search bar often. Looking up `justify-between` ten times is how you memorize it.

---

[Previous](./[16]-Accessibility-And-Best-Practices.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[18]-Next-Steps-With-Tailwind-CSS.md)