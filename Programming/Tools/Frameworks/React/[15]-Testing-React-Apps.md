[Previous](./[14]-Modern-React-Features.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[16]-Accessibility-And-Best-Practices.md)

*Going Further*

# Lesson 15 - Testing React Apps

## 15.1 Why and what to test

Automated tests catch bugs before users do, and let you change code with confidence.

### Types of tests

| Type | What it checks | Speed | Tools |
|---|---|---|---|
| **Unit** | One function or hook in isolation | Very fast | Vitest / Jest |
| **Component / integration** | A component, or several, rendered together and used like a user would | Fast | Vitest/Jest + React Testing Library |
| **End-to-end (E2E)** | The whole app in a real browser | Slow | Playwright, Cypress |

A healthy mix has many fast tests, and a smaller number of slow ones for the most critical user journeys.

### Test behavior, not implementation

A good React test describes what a **user sees and does**:

- "When I click Add, the item appears in the list."
- "When the password is too short, an error message shows."

A bad test checks internals: "state variable `count` equals 1" or "function `setCount` was called". Such tests break whenever you refactor, even though nothing visible changed.

### What to test

- Important user flows (login, checkout, form submission).
- Conditional rendering (loading, error, empty states).
- Business logic and utility functions (formatting, calculations).
- Bug fixes (write a test that fails, then fix the bug).

### What not to test

- Third-party library internals.
- Implementation details (private state, CSS class names).
- Trivial code with no logic.

---

## 15.2 Vitest and Jest

**Vitest** and **Jest** are **test runners**: they find test files, run them, and report results. They provide `describe`, `it`/`test`, and `expect`.

- **Vitest** is designed for Vite projects and reuses your Vite config, so it is the natural choice for a Vite app.
- **Jest** is long-established and widely used, particularly in older projects and in projects not using Vite. Its API is nearly identical.

### Setting up Vitest with React

```bash
npm install -D vitest jsdom @testing-library/react @testing-library/jest-dom @testing-library/user-event
```

`jsdom` simulates a browser DOM inside Node.js. Configure it in `vite.config.js`:

```js
import { defineConfig } from "vitest/config";
import react from "@vitejs/plugin-react";

export default defineConfig({
  plugins: [react()],
  test: {
    environment: "jsdom",
    globals: true,
    setupFiles: "./src/setupTests.js",
  },
});
```

Create `src/setupTests.js`:

```js
import "@testing-library/jest-dom/vitest";
```

Add a script to `package.json`:

```json
{
  "scripts": {
    "test": "vitest"
  }
}
```

`npm test` runs in **watch mode**, re-running relevant tests when files change. Use `npx vitest run` for a single run (for example in CI).

### Writing a unit test

```js
// formatPrice.js
export function formatPrice(cents) {
  return `$${(cents / 100).toFixed(2)}`;
}
```

```js
// formatPrice.test.js
import { describe, it, expect } from "vitest";
import { formatPrice } from "./formatPrice.js";

describe("formatPrice", () => {
  it("formats cents as dollars", () => {
    expect(formatPrice(1999)).toBe("$19.99");
  });

  it("handles zero", () => {
    expect(formatPrice(0)).toBe("$0.00");
  });
});
```

### Common matchers

```js
expect(value).toBe(3);                  // strict equality (primitives)
expect(obj).toEqual({ a: 1 });          // deep equality
expect(list).toContain("apple");
expect(fn).toThrow();
expect(value).toBeTruthy();
expect(fn).toHaveBeenCalledWith("x");   // for mock functions
```

### Mocks

```js
import { vi } from "vitest";

const onSave = vi.fn();                 // a mock function
onSave("data");
expect(onSave).toHaveBeenCalledWith("data");
```

In Jest the equivalent is `jest.fn()`.

---

## 15.3 React Testing Library

**React Testing Library (RTL)** renders components and provides queries that find elements **the way users and assistive technology find them**: by role, label, and visible text.

### A component to test

```jsx
// Counter.jsx
import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

### The test

```jsx
// Counter.test.jsx
import { describe, it, expect } from "vitest";
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import Counter from "./Counter.jsx";

describe("Counter", () => {
  it("starts at zero", () => {
    render(<Counter />);
    expect(screen.getByText("Count: 0")).toBeInTheDocument();
  });

  it("increments when the button is clicked", async () => {
    const user = userEvent.setup();
    render(<Counter />);

    await user.click(screen.getByRole("button", { name: /increment/i }));

    expect(screen.getByText("Count: 1")).toBeInTheDocument();
  });
});
```

### The query families

| Prefix | If not found | Use for |
|---|---|---|
| `getBy...` | Throws an error | Elements that **should be there** |
| `queryBy...` | Returns `null` | Asserting something is **absent** |
| `findBy...` | Waits, then throws | Elements that appear **asynchronously** |

Each also has an `...AllBy...` version for multiple matches.

### Preferred queries (in order)

1. `getByRole("button", { name: "Save" })`: most accessible, mirrors real usage.
2. `getByLabelText("Email")`: for form fields.
3. `getByPlaceholderText`, `getByText`, `getByDisplayValue`.
4. `getByTestId("...")`: **last resort**, using a `data-testid` attribute.

### Testing a form

```jsx
it("shows an error for an invalid email", async () => {
  const user = userEvent.setup();
  render(<SignupForm />);

  await user.type(screen.getByLabelText(/email/i), "not-an-email");
  await user.click(screen.getByRole("button", { name: /sign up/i }));

  expect(screen.getByRole("alert")).toHaveTextContent(/valid email/i);
});
```

### Testing async behavior

```jsx
it("shows posts after loading", async () => {
  render(<PostList />);

  expect(screen.getByText(/loading/i)).toBeInTheDocument();
  expect(await screen.findByText("First post")).toBeInTheDocument();
});
```

### Mocking network requests

Avoid real network calls in tests. Replace `fetch` with a mock for simple cases:

```jsx
import { vi, beforeEach } from "vitest";

beforeEach(() => {
  vi.stubGlobal(
    "fetch",
    vi.fn().mockResolvedValue({
      ok: true,
      json: async () => [{ id: 1, title: "First post" }],
    })
  );
});
```

For larger apps, **Mock Service Worker (MSW)** intercepts requests at the network level, which gives more realistic tests.

### Useful tips

- `userEvent` simulates real interactions (focus, typing, clicking) and is preferred over `fireEvent`.
- `screen.debug()` prints the current DOM to help when a query fails.
- Tests that use context or routing must wrap the component in the necessary providers (a small custom `render` helper keeps this tidy).

---

## 15.4 End-to-end tests

**E2E tests** drive a real browser against your running app, covering the front end, back end, and network together. They are the closest thing to a real user, but slower and more fragile than component tests, so reserve them for **critical paths**: sign-up, login, checkout.

### Playwright

**Playwright** (from Microsoft) supports Chromium, Firefox, and WebKit.

```bash
npm init playwright@latest
```

The setup asks a few questions and creates a config and an example test. A test looks like:

```js
// tests/todo.spec.js
import { test, expect } from "@playwright/test";

test("user can add a todo", async ({ page }) => {
  await page.goto("http://localhost:5173");

  await page.getByPlaceholder("What needs to be done?").fill("Buy milk");
  await page.getByRole("button", { name: "Add" }).click();

  await expect(page.getByText("Buy milk")).toBeVisible();
});
```

Run the tests with:

```bash
npx playwright test
```

Playwright can start your dev server for you using the `webServer` option in `playwright.config.js`:

```js
export default defineConfig({
  webServer: {
    command: "npm run dev",
    url: "http://localhost:5173",
    reuseExistingServer: true,
  },
});
```

Useful extras: `npx playwright test --ui` (interactive mode), `npx playwright codegen` (records your actions into test code), and trace viewing for debugging failures.

### Cypress

**Cypress** is another popular E2E tool with an interactive runner and a similar workflow. Both are good; Playwright's multi-browser support and speed make it a common default for new projects.

### Making E2E tests reliable

- Use **role- and label-based locators**, like in RTL, not fragile CSS selectors.
- Rely on the tools' **automatic waiting** instead of fixed `sleep` delays.
- Keep each test **independent**, so it sets up its own data and doesn't depend on another test.
- Test against a **dedicated test environment** with controlled data.
- Run them in **CI** on every pull request (see [Lesson 17](./[17]-Building-And-Deployment.md)).

[Previous](./[14]-Modern-React-Features.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[16]-Accessibility-And-Best-Practices.md)