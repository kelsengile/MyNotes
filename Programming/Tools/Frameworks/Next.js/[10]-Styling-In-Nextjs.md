[Previous](./[9]-Route-Handlers-And-APIs.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[11]-Optimization-And-Metadata.md)

*Building Features*

# Lesson 10 - Styling In Next.js

## 10.1 CSS Modules

**CSS Modules** scope class names locally to the component that imports them, which prevents naming collisions. Name the file `*.module.css`.

```css
/* app/components/Button.module.css */
.button {
  padding: 0.5rem 1rem;
  background: royalblue;
  color: white;
  border: none;
  border-radius: 6px;
}

.button:hover {
  background: navy;
}
```

```tsx
// app/components/Button.tsx
import styles from "./Button.module.css";

export default function Button({ label }: { label: string }) {
  return <button className={styles.button}>{label}</button>;
}
```

At build time `.button` becomes a unique name such as `Button_button__x7Yz2`, so another `.button` elsewhere cannot clash with it.

CSS Modules work in both Server and Client Components. Combine multiple classes with a template string:

```tsx
<div className={`${styles.card} ${styles.highlighted}`} />
```

---

## 10.2 Tailwind CSS

**Tailwind CSS** is a utility-first framework: you style elements by combining small classes directly in your markup. `create-next-app` can set it up for you.

```tsx
export default function Card() {
  return (
    <div className="max-w-sm rounded-xl bg-white p-6 shadow-md">
      <h2 className="text-xl font-bold text-gray-900">Title</h2>
      <p className="mt-2 text-gray-600">Some description text.</p>
      <button className="mt-4 rounded bg-blue-600 px-4 py-2 text-white hover:bg-blue-700">
        Learn more
      </button>
    </div>
  );
}
```

Useful features:

- **Responsive prefixes**: `md:flex`, `lg:grid-cols-3`
- **State variants**: `hover:`, `focus:`, `disabled:`
- **Dark mode**: `dark:bg-gray-900`
- **Customization** through the Tailwind configuration (setup varies by Tailwind version, so follow the current Tailwind docs)

Tailwind works in Server Components with no runtime cost since it generates plain CSS. A helper like `clsx` makes conditional classes cleaner:

```tsx
import clsx from "clsx";

<button className={clsx("rounded px-4 py-2", isActive ? "bg-blue-600" : "bg-gray-300")} />
```

---

## 10.3 CSS-in-JS considerations

Libraries such as **styled-components** and **Emotion** generate styles at runtime in JavaScript. In the App Router they need extra care because:

- They rely on React context and hooks, so they only work in **Client Components**.
- Styles must be collected during server rendering and injected into the HTML, usually through a registry component, or the page may flash unstyled content.
- Runtime style generation adds JavaScript and can slow rendering.

Because of this, many teams prefer **zero-runtime** options: CSS Modules, Tailwind, or build-time libraries such as vanilla-extract.

If you must use a runtime library, follow its official Next.js App Router guide, which shows how to set up its registry in the root layout.

| Approach | Server Components | Runtime cost | Setup effort |
|---|---|---|---|
| CSS Modules | Yes | None | Low |
| Tailwind CSS | Yes | None | Low |
| Global CSS | Yes | None | Low |
| styled-components / Emotion | No (client only) | Yes | Higher |

---

## 10.4 Global styles and fonts

**Global styles** apply to the whole app. Import the file once in the root layout:

```css
/* app/globals.css */
*,
*::before,
*::after {
  box-sizing: border-box;
}

body {
  margin: 0;
  font-family: system-ui, sans-serif;
}
```

```tsx
// app/layout.tsx
import "./globals.css";

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

Global CSS can be imported in any layout, but it applies globally no matter where it is imported, so use it sparingly for resets, variables, and base styles.

**Fonts** are best loaded with `next/font`, which downloads fonts at build time and self-hosts them (no request to Google at runtime, and no layout shift):

```tsx
import { Inter } from "next/font/google";

const inter = Inter({ subsets: ["latin"] });

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en" className={inter.className}>
      <body>{children}</body>
    </html>
  );
}
```

Fonts are covered in more detail in Lesson 11.

[Previous](./[9]-Route-Handlers-And-APIs.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[11]-Optimization-And-Metadata.md)