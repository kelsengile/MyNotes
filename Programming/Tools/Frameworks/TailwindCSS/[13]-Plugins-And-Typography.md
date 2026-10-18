[Previous](./[12]-Reusing-Styles.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[14]-Using-Tailwind-With-Frameworks.md)

*Customization*

# Lesson 13 - Plugins And Typography

## 13.1 Official plugins

Plugins extend Tailwind with extra utilities, components, or variants. In v4, you install a plugin with npm and enable it in your CSS with `@plugin`.

**Official plugins**

| Plugin | Purpose |
|---|---|
| `@tailwindcss/typography` | The `prose` classes for styling rich text (Markdown, CMS content) |
| `@tailwindcss/forms` | A base reset that makes form controls easy to style |
| `@tailwindcss/container-queries` | Container queries. **Built into v4**, so you only need this on v3. |

Plugins that used to be separate in older versions, such as `aspect-ratio` and `line-clamp`, are now built into Tailwind core.

**Installing and enabling**

```bash
npm install -D @tailwindcss/typography @tailwindcss/forms
```

```css
@import "tailwindcss";
@plugin "@tailwindcss/typography";
@plugin "@tailwindcss/forms";
```

That is all, with no config file needed.

**Third-party plugins** work the same way, for example daisyUI (`@plugin "daisyui";`, Lesson 15).

**Plugins with options** use a block:

```css
@plugin "@tailwindcss/typography" {
  className: wysiwyg;
}
```

**Before adding a plugin**, ask whether a custom `@utility`, `@custom-variant`, or `@theme` entry (Lesson 11 and Lesson 12) would solve it more simply.

---

## 13.2 The typography plugin

Preflight strips all default styling from headings, lists, links, and so on. That is great for UI, but a problem for **content you do not control**, such as a blog post rendered from Markdown. The typography plugin adds `prose`, which gives that content sensible, beautiful defaults.

```html
<article class="prose">
  <h1>Article title</h1>
  <p>Paragraphs, <a href="#">links</a>, <strong>bold</strong> text…</p>
  <ul>
    <li>List item</li>
  </ul>
  <blockquote>A pull quote.</blockquote>
  <pre><code>const x = 1;</code></pre>
</article>
```

**Sizes:** `prose-sm`, `prose` (base), `prose-lg`, `prose-xl`, `prose-2xl`. Use responsive versions: `prose md:prose-lg`.

**Gray scales:** `prose-gray`, `prose-slate`, `prose-zinc`, `prose-neutral`, `prose-stone` (default is gray).

**Dark mode:** `dark:prose-invert` flips the colors for dark backgrounds.

```html
<article class="prose prose-slate lg:prose-lg dark:prose-invert">...</article>
```

**By default, `prose` limits width** to about 65 characters for readability. Remove it with `max-w-none` when the article sits in a layout that already controls width.

**Customizing elements with modifiers**

```html
<article class="prose prose-headings:font-bold prose-headings:tracking-tight prose-a:text-blue-600 prose-a:no-underline hover:prose-a:underline prose-img:rounded-xl">
  ...
</article>
```

The pattern is `prose-{element}:{utility}`. Elements include `headings`, `p`, `a`, `blockquote`, `code`, `pre`, `ul`, `ol`, `li`, `img`, `table`, `hr`, and more.

**Escape hatch:** use `not-prose` on a child to exclude it, for example an interactive widget inside an article:

```html
<article class="prose">
  <p>Text…</p>
  <div class="not-prose rounded border p-4">A custom component with its own styling</div>
</article>
```

---

## 13.3 Forms plugin

Browsers style form controls inconsistently, and Preflight leaves them mostly unstyled. `@tailwindcss/forms` applies a simple, consistent base style to inputs, selects, checkboxes, and radios so that utilities like `rounded-lg`, `border-gray-300`, and `focus:ring-2` work the way you expect.

```css
@import "tailwindcss";
@plugin "@tailwindcss/forms";
```

**Example: after enabling the plugin**

```html
<form class="max-w-sm space-y-4">
  <div>
    <label for="email" class="block text-sm font-medium text-gray-700">Email</label>
    <input
      id="email"
      type="email"
      class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500"
    />
  </div>

  <div>
    <label for="role" class="block text-sm font-medium text-gray-700">Role</label>
    <select id="role" class="mt-1 block w-full rounded-md border-gray-300">
      <option>Designer</option>
      <option>Developer</option>
    </select>
  </div>

  <label class="flex items-center gap-2">
    <input type="checkbox" class="rounded border-gray-300 text-indigo-600 focus:ring-indigo-500" />
    <span class="text-sm">Remember me</span>
  </label>
</form>
```

**Colors on checkboxes and radios.** With the plugin active, `text-indigo-600` sets the *checked* color.

**Class strategy.** By default the plugin styles form elements globally. If you would rather opt in per element, use the class strategy:

```css
@plugin "@tailwindcss/forms" {
  strategy: class;
}
```

Then add the classes yourself:

```html
<input type="text" class="form-input rounded-md" />
<select class="form-select"></select>
<input type="checkbox" class="form-checkbox" />
```

**Without the plugin**, you can style forms with plain utilities (padding, border, rounded, and so on). The plugin mainly saves you from resetting browser defaults.

---

## 13.4 Writing a simple plugin

Most extensions can now be written directly in CSS with `@utility` and `@custom-variant` (Lesson 12). A JavaScript plugin is still useful when you need logic, loops, or to share code across projects.

**Create `plugins/my-plugin.js`**

```js
import plugin from "tailwindcss/plugin";

export default plugin(function ({ addBase, addUtilities, addVariant }) {
  // 1. Base styles
  addBase({
    "html": { scrollBehavior: "smooth" },
  });

  // 2. New utilities
  addUtilities({
    ".text-shadow-soft": {
      textShadow: "0 2px 4px rgb(0 0 0 / 0.2)",
    },
    ".scrollbar-hidden": {
      scrollbarWidth: "none",
    },
  });

  // 3. A custom variant: style on hover OR focus
  addVariant("hocus", ["&:hover", "&:focus"]);
});
```

**Enable it in your CSS**

```css
@import "tailwindcss";
@plugin "./plugins/my-plugin.js";
```

**Use it**

```html
<h1 class="text-shadow-soft">Title</h1>
<div class="scrollbar-hidden overflow-x-auto">…</div>
<button class="bg-blue-600 hocus:bg-blue-800">Button</button>
```

**Plugin helpers you will use most**

| Helper | Purpose |
|---|---|
| `addBase` | Add global element styles |
| `addUtilities` | Add fixed utility classes |
| `matchUtilities` | Add utilities that accept values (like `tab-4`, `tab-8`) |
| `addVariant` | Add new variants |
| `theme` | Read theme values |

**The same thing in plain CSS (often preferred in v4):**

```css
@utility text-shadow-soft {
  text-shadow: 0 2px 4px rgb(0 0 0 / 0.2);
}
@custom-variant hocus (&:hover, &:focus);
```

Choose a JS plugin only when you need programmatic generation or distribution to other projects.

---

[Previous](./[12]-Reusing-Styles.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[14]-Using-Tailwind-With-Frameworks.md)