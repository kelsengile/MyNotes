[Previous](./[15]-Testing-React-Apps.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[17]-Building-And-Deployment.md)

*Going Further*

# Lesson 16 - Accessibility And Best Practices

## 16.1 Semantic markup

**Accessibility (a11y)** means making your app usable by everyone, including people who use screen readers, keyboards only, voice control, or who have visual, motor, hearing, or cognitive differences. It also helps everyone else: good contrast, clear structure, and keyboard support benefit all users.

### Use the right element

Native HTML elements come with built-in accessibility: keyboard support, focus, and roles announced by screen readers. Choosing the correct element is the single most important habit.

```jsx
// Bad: a div pretending to be a button
<div onClick={save} className="button">Save</div>

// Good: a real button, with keyboard and screen reader support built in
<button type="button" onClick={save}>Save</button>
```

| Need | Use | Not |
|---|---|---|
| Action on the page | `<button>` | `<div onClick>` |
| Go to another page | `<a href>` / `<Link>` | `<span onClick>` |
| Page regions | `<header>`, `<nav>`, `<main>`, `<footer>`, `<aside>`, `<section>` | many `<div>`s |
| Headings | `<h1>`-`<h6>` in order | Bold text with big font |
| Lists | `<ul>`/`<ol>` with `<li>` | Line breaks |
| Data in rows and columns | `<table>`, `<th>`, `<td>` | Divs in a grid |

### Page structure

- One `<h1>` per page; do not skip heading levels (`h2` then `h4`).
- Use landmarks (`<main>`, `<nav>`) so screen reader users can jump around.
- Set the page language in `index.html`: `<html lang="en">`.
- Give each page a meaningful `<title>`. With a router, update the title on navigation.

### Labels and text alternatives

```jsx
// Every form control needs a label
<label htmlFor="email">Email</label>
<input id="email" type="email" />

// Images need alt text; decorative images get empty alt
<img src="chart.png" alt="Sales rose 20% in March" />
<img src="divider.png" alt="" />
```

For icon-only buttons, provide an accessible name:

```jsx
<button type="button" aria-label="Close dialog">
  <CloseIcon aria-hidden="true" />
</button>
```

### Color and text

- Maintain sufficient **contrast** (WCAG AA: 4.5:1 for normal text).
- Never use color as the **only** way to convey information (for example, show an error icon and text, not just a red border).
- Let text resize; use relative units like `rem`.

---

## 16.2 Keyboard and focus handling

Many users navigate only with the keyboard: `Tab` / `Shift+Tab` to move, `Enter` / `Space` to activate, arrow keys within widgets, and `Esc` to dismiss.

### Everything interactive must be reachable and usable

- Native buttons, links, and form controls are focusable automatically.
- Don't remove the focus outline (`outline: none`) without providing a **clearly visible** replacement, such as with `:focus-visible`.

```css
:focus-visible {
  outline: 3px solid royalblue;
  outline-offset: 2px;
}
```

### tabIndex

- `tabIndex={0}` makes a non-interactive element focusable in natural order. Rarely needed if you use correct elements.
- `tabIndex={-1}` makes an element focusable **by script only** (not through Tab). Useful for moving focus to a heading or container.
- **Avoid positive values** such as `tabIndex={3}`, as they create a confusing tab order.

### Managing focus with refs

When content changes dramatically, such as opening a modal or navigating to a new page, move focus on purpose:

```jsx
import { useEffect, useRef } from "react";

function Modal({ onClose, children }) {
  const closeRef = useRef(null);

  useEffect(() => {
    closeRef.current?.focus();            // focus inside the modal on open
  }, []);

  return (
    <div role="dialog" aria-modal="true" aria-labelledby="modal-title">
      <h2 id="modal-title">Settings</h2>
      {children}
      <button ref={closeRef} onClick={onClose}>Close</button>
    </div>
  );
}
```

For a complete modal you also need to:

1. **Trap focus** inside while open (Tab cycles within the dialog).
2. Close on **`Esc`**.
3. **Return focus** to the element that opened it when it closes.

Writing this correctly is tricky. The native `<dialog>` element, or accessible libraries such as Radix UI or React Aria, handle it for you.

### Keyboard handlers for custom widgets

If you must build a custom interactive element, handle keyboard events:

```jsx
function handleKeyDown(e) {
  if (e.key === "Enter" || e.key === " ") {
    e.preventDefault();
    activate();
  }
}
```

But prefer native elements; they do this for free.

### Skip links

Let keyboard users skip repeated navigation:

```jsx
<a href="#main" className="skip-link">Skip to main content</a>
...
<main id="main" tabIndex={-1}>...</main>
```

### Routing and focus

In single-page apps, navigating does not reload the page, so screen readers may not announce the change. Move focus to the new page's heading (or announce the route change) after navigation.

---

## 16.3 ARIA basics

**ARIA** (Accessible Rich Internet Applications) attributes add accessibility information when HTML alone is not enough. The first rule of ARIA is: **use a native HTML element if one exists**, and ARIA only as a supplement. Wrong ARIA is worse than none.

### Common attributes (hyphenated in JSX)

| Attribute | Purpose | Example |
|---|---|---|
| `aria-label` | Gives an accessible name | `<button aria-label="Close">X</button>` |
| `aria-labelledby` | Points to another element that names this one | `aria-labelledby="modal-title"` |
| `aria-describedby` | Points to extra description (like help text or an error) | `aria-describedby="email-error"` |
| `aria-expanded` | Whether a collapsible is open | `<button aria-expanded={open}>` |
| `aria-controls` | Which element this controls | `aria-controls="menu"` |
| `aria-hidden` | Hides from assistive tech | decorative icons |
| `aria-live` | Announces changes automatically | status messages |
| `aria-invalid` | A field has an error | form inputs |
| `role` | Declares an element's role | `role="alert"`, `role="dialog"` |

### Example: disclosure (accordion item)

```jsx
function Disclosure({ title, children }) {
  const [open, setOpen] = useState(false);
  return (
    <div>
      <button
        type="button"
        aria-expanded={open}
        aria-controls="panel-1"
        onClick={() => setOpen(!open)}
      >
        {title}
      </button>
      <div id="panel-1" hidden={!open}>
        {children}
      </div>
    </div>
  );
}
```

### Example: accessible form errors

```jsx
<label htmlFor="email">Email</label>
<input
  id="email"
  type="email"
  aria-invalid={Boolean(error)}
  aria-describedby={error ? "email-error" : undefined}
/>
{error && <p id="email-error" role="alert">{error}</p>}
```

### Live regions

Announce dynamic updates (like "Item added to cart") without moving focus:

```jsx
<div aria-live="polite">{statusMessage}</div>
```

- `polite` waits for the user to finish what they're doing.
- `assertive` (and `role="alert"`) interrupts immediately; use it only for urgent messages.

The live region element should exist in the DOM **before** its text changes.

### Testing accessibility

- **eslint-plugin-jsx-a11y** catches many mistakes while you write code.
- **axe DevTools** or Lighthouse audits find issues in the browser.
- **Test with a keyboard:** unplug your mouse and try your whole app.
- **Try a screen reader:** VoiceOver (macOS/iOS), NVDA (Windows, free), or TalkBack (Android).
- Automated tools find only a portion of problems; manual testing is still needed.

---

## 16.4 Error boundaries

A JavaScript error during **rendering** would normally break your whole app and leave a blank screen. An **error boundary** catches errors in its child tree and shows a **fallback UI** instead.

### What they catch (and don't)

Error boundaries catch errors during: rendering, lifecycle code, and constructors of components below them.

They do **not** catch errors in:

- **event handlers** (use `try/catch` in the handler),
- **asynchronous code** such as `setTimeout` or promises (unless the error is rethrown during render),
- server-side rendering,
- the error boundary itself.

### react-error-boundary (recommended)

Error boundaries must be **class components** in core React, so most people use the small `react-error-boundary` package:

```bash
npm install react-error-boundary
```

```jsx
import { ErrorBoundary } from "react-error-boundary";

function ErrorFallback({ error, resetErrorBoundary }) {
  return (
    <div role="alert">
      <p>Something went wrong.</p>
      <pre>{error.message}</pre>
      <button onClick={resetErrorBoundary}>Try again</button>
    </div>
  );
}

function App() {
  return (
    <ErrorBoundary
      FallbackComponent={ErrorFallback}
      onError={(error, info) => console.error(error, info.componentStack)}
      onReset={() => {
        // reset any state that caused the error
      }}
    >
      <Dashboard />
    </ErrorBoundary>
  );
}
```

### Writing one yourself

```jsx
import { Component } from "react";

class ErrorBoundary extends Component {
  state = { hasError: false };

  static getDerivedStateFromError() {
    return { hasError: true };
  }

  componentDidCatch(error, info) {
    console.error("Caught by boundary:", error, info.componentStack);
  }

  render() {
    if (this.state.hasError) {
      return this.props.fallback ?? <p>Something went wrong.</p>;
    }
    return this.props.children;
  }
}
```

### Placement strategy

- A **top-level** boundary prevents a blank page, as a last resort.
- **Several smaller boundaries** (around a widget, a route, a sidebar) let the rest of the app keep working when one part fails.
- In production, send caught errors to a monitoring service (for example Sentry).

### Other best practices

- **Keep components small and focused;** split when a component does too many things.
- **Don't mutate props or state;** always create new copies.
- **Use stable keys** for lists.
- **Prefer derived values** over duplicated state.
- **Handle loading, error, and empty states** everywhere you fetch data.
- **Don't use `dangerouslySetInnerHTML`** with untrusted content. It allows cross-site scripting (XSS). If needed, sanitize it first (for example with DOMPurify).
- **Keep dependencies lean** and updated; audit with `npm audit`.
- **Use TypeScript and ESLint** to catch mistakes early.
- **Write tests** for important flows (see [Lesson 15](./[15]-Testing-React-Apps.md)).

[Previous](./[15]-Testing-React-Apps.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[17]-Building-And-Deployment.md)