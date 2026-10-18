[Previous](./[10]-Data-Fetching-And-APIs.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[12]-Project-Structure-And-Tooling.md)

*Building Applications*

# Lesson 11 - Styling React Apps

## 11.1 CSS files and CSS Modules

### Plain CSS files

The simplest approach is to write a normal `.css` file and import it into a component. Vite includes the CSS in the build automatically.

```css
/* Button.css */
.btn {
  padding: 0.5rem 1rem;
  border-radius: 6px;
  background: royalblue;
  color: white;
}
.btn.danger {
  background: crimson;
}
```

```jsx
import "./Button.css";

function Button({ danger, children }) {
  return (
    <button className={danger ? "btn danger" : "btn"}>{children}</button>
  );
}
```

**Downside:** CSS is **global**. Importing `Button.css` in one component makes the `.btn` class apply everywhere, so two components using the same class name can clash.

### Conditional class names

Build class strings with template literals, or use the small `clsx` helper:

```bash
npm install clsx
```

```jsx
import clsx from "clsx";

<button className={clsx("btn", { danger: isDanger, large: isLarge })}>
  Save
</button>
```

### CSS Modules

A **CSS Module** is a CSS file named `*.module.css`. Class names are **automatically made unique** to that file, so clashes are impossible. Vite supports this without any setup.

```css
/* Card.module.css */
.card {
  border: 1px solid #ddd;
  padding: 1rem;
}
.title {
  font-size: 1.25rem;
}
```

```jsx
import styles from "./Card.module.css";

function Card({ title, children }) {
  return (
    <div className={styles.card}>
      <h2 className={styles.title}>{title}</h2>
      {children}
    </div>
  );
}
```

At build time `styles.card` becomes something like `Card_card__x7f3a`, which is unique. Use bracket syntax for hyphenated names: `styles["card-header"]`.

### Inline styles

The `style` prop (see [Lesson 2](./[2]-JSX-And-Elements.md)) is fine for **dynamic values**, such as a computed width:

```jsx
<div style={{ width: `${progress}%` }} />
```

But inline styles cannot express hover states, media queries, or pseudo-elements, so use classes for general styling.

---

## 11.2 CSS-in-JS options

**CSS-in-JS** means writing styles inside JavaScript files, scoped to components.

### styled-components

```bash
npm install styled-components
```

```jsx
import styled from "styled-components";

const Button = styled.button`
  padding: 0.5rem 1rem;
  background: ${(props) => (props.$danger ? "crimson" : "royalblue")};
  color: white;
  &:hover {
    opacity: 0.85;
  }
`;

function App() {
  return <Button $danger>Delete</Button>;
}
```

The `$` prefix marks a **transient prop**: it is used for styling but is not passed down to the DOM element.

### Emotion

Emotion offers a similar `styled` API and a `css` prop:

```bash
npm install @emotion/react @emotion/styled
```

```jsx
import styled from "@emotion/styled";

const Title = styled.h1`
  color: hotpink;
`;
```

### Trade-offs

| Pros | Cons |
|---|---|
| Styles live beside the component | Runtime libraries add JavaScript and styling work in the browser |
| Dynamic styles from props are easy | Often incompatible with Server Components (see [Lesson 14](./[14]-Modern-React-Features.md)) |
| Automatic scoping | Extra dependency |

Because of the runtime cost and Server Component limitations, many newer projects prefer **CSS Modules**, **Tailwind**, or **zero-runtime** tools such as Vanilla Extract or Panda CSS, which generate CSS at build time. Check the current status of any CSS-in-JS library before adopting it for a new project, as maintenance levels vary.

---

## 11.3 Utility CSS with Tailwind

**Tailwind CSS** is a **utility-first** framework: instead of writing custom CSS, you compose small, single-purpose classes directly in your markup.

```jsx
<button className="rounded-md bg-blue-600 px-4 py-2 text-white hover:bg-blue-700">
  Save
</button>
```

### Setup with Vite (Tailwind v4)

```bash
npm install tailwindcss @tailwindcss/vite
```

Add the plugin in `vite.config.js`:

```js
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import tailwindcss from "@tailwindcss/vite";

export default defineConfig({
  plugins: [react(), tailwindcss()],
});
```

Then import Tailwind at the top of your main CSS file (such as `src/index.css`):

```css
@import "tailwindcss";
```

Make sure that CSS file is imported in `main.jsx`. Tailwind v4 scans your source files automatically, so no `content` configuration is needed.

### Useful patterns

**Responsive design** with breakpoint prefixes (mobile-first):

```jsx
<div className="grid grid-cols-1 gap-4 md:grid-cols-2 lg:grid-cols-3">
```

**State variants:** `hover:`, `focus:`, `disabled:`, and dark mode with `dark:`:

```jsx
<p className="text-gray-900 dark:text-gray-100">Adapts to dark mode</p>
```

**Extracting repetition into components**, not into CSS:

```jsx
function Button({ children, ...props }) {
  return (
    <button
      className="rounded-md bg-blue-600 px-4 py-2 text-white hover:bg-blue-700"
      {...props}
    >
      {children}
    </button>
  );
}
```

**Dynamic classes:** always write complete class names so Tailwind can find them:

```jsx
// Good
const color = isError ? "text-red-600" : "text-green-600";

// Bad: Tailwind cannot detect a class built from pieces
const color = `text-${isError ? "red" : "green"}-600`;
```

### Pros and cons

Utility classes give fast development, consistent spacing and colors, and small final CSS. The trade-off is longer `className` strings and a vocabulary of class names to learn.

---

## 11.4 Component libraries

A **component library** gives you ready-made, tested UI parts: buttons, modals, menus, data tables, date pickers.

| Library | Style | Notes |
|---|---|---|
| **MUI (Material UI)** | Material Design look | Large, full-featured, with theming |
| **Chakra UI** | Style props, accessible defaults | Simple API |
| **Mantine** | Rich set including hooks and forms | Good defaults |
| **shadcn/ui** | Copies component source into your project | Built on Radix UI and Tailwind; you own the code |
| **Radix UI / React Aria** | **Unstyled**, accessible primitives | You supply all styling |

### Example: MUI

```bash
npm install @mui/material @emotion/react @emotion/styled
```

```jsx
import Button from "@mui/material/Button";
import TextField from "@mui/material/TextField";

function LoginForm() {
  return (
    <form>
      <TextField label="Email" type="email" fullWidth margin="normal" />
      <Button variant="contained" type="submit">Log in</Button>
    </form>
  );
}
```

### Choosing

- **Need to ship fast with a consistent look?** A full library (MUI, Mantine, Chakra).
- **Need a unique brand design and full control?** Unstyled primitives (Radix, React Aria) or shadcn/ui.
- **Accessibility matters** (it always should): prefer libraries that handle keyboard navigation and ARIA for you, such as Radix or React Aria. Building accessible modals and dropdowns from scratch is difficult.

Consider bundle size, how customizable the theme is, how active the maintenance is, and whether the library supports your React version before adding it.

[Previous](./[10]-Data-Fetching-And-APIs.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[12]-Project-Structure-And-Tooling.md)