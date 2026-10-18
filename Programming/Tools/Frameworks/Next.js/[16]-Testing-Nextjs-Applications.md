[Previous](./[15]-TypeScript-With-Nextjs.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[17]-Performance-Tuning.md)

*Quality And Delivery*

# Lesson 16 - Testing Next.js Applications

## 16.1 Unit testing components

A **unit test** checks one small piece (a component or function) in isolation. The usual tools are **Jest** or **Vitest** as the test runner, plus **React Testing Library** to render components and query the DOM.

Install (Vitest example):

```bash
npm install -D vitest @vitejs/plugin-react jsdom @testing-library/react @testing-library/jest-dom
```

```ts
// vitest.config.mts
import { defineConfig } from "vitest/config";
import react from "@vitejs/plugin-react";

export default defineConfig({
  plugins: [react()],
  test: {
    environment: "jsdom",
    setupFiles: ["./vitest.setup.ts"],
  },
});
```

```ts
// vitest.setup.ts
import "@testing-library/jest-dom/vitest";
```

A component test:

```tsx
// app/components/Counter.test.tsx
import { render, screen, fireEvent } from "@testing-library/react";
import { expect, test } from "vitest";
import Counter from "./Counter";

test("increments the count when clicked", () => {
  render(<Counter />);
  const button = screen.getByRole("button");

  expect(button).toHaveTextContent("Clicked 0 times");
  fireEvent.click(button);
  expect(button).toHaveTextContent("Clicked 1 times");
});
```

Test **behavior the user sees** (text, roles, clicks), not internal implementation details.

Important limitation: unit-testing tools generally **cannot render `async` Server Components**. Test those with end-to-end tests (16.3), or extract their logic into plain functions and test the functions.

---

## 16.2 Testing server code

Move important logic out of components into plain functions, then test them directly. This works for validation, formatting, permissions, and data transformations.

```ts
// lib/slugify.ts
export function slugify(text: string): string {
  return text
    .toLowerCase()
    .trim()
    .replace(/[^a-z0-9]+/g, "-")
    .replace(/(^-|-$)/g, "");
}
```

```ts
// lib/slugify.test.ts
import { expect, test } from "vitest";
import { slugify } from "./slugify";

test("converts a title to a slug", () => {
  expect(slugify("Hello, Next.js World!")).toBe("hello-next-js-world");
});
```

**Route Handlers** are standard functions that take a `Request` and return a `Response`, so you can call them directly:

```ts
// app/api/hello/route.test.ts
import { expect, test } from "vitest";
import { GET } from "./route";

test("GET returns a greeting", async () => {
  const response = await GET();
  const data = await response.json();

  expect(response.status).toBe(200);
  expect(data.message).toBe("Hello from the API");
});
```

**Server Actions** that call the database should have the database **mocked**:

```ts
import { vi, test, expect } from "vitest";

vi.mock("@/lib/db", () => ({
  db: { post: { create: vi.fn().mockResolvedValue({ id: 1 }) } },
}));
vi.mock("next/cache", () => ({ revalidatePath: vi.fn() }));
```

Mocking `next/cache` and `next/navigation` is usually needed because those modules expect a running Next.js request context.

---

## 16.3 End-to-end tests

An **end-to-end (E2E) test** drives a real browser against your running app, checking whole user flows. It is the best way to test async Server Components, routing, forms, and authentication together. **Playwright** is a common choice and is supported by `create-next-app` examples.

```bash
npm init playwright@latest
```

```ts
// playwright.config.ts
import { defineConfig } from "@playwright/test";

export default defineConfig({
  testDir: "./e2e",
  webServer: {
    command: "npm run build && npm run start",
    url: "http://localhost:3000",
    reuseExistingServer: !process.env.CI,
  },
  use: { baseURL: "http://localhost:3000" },
});
```

```ts
// e2e/navigation.spec.ts
import { test, expect } from "@playwright/test";

test("navigates from home to about", async ({ page }) => {
  await page.goto("/");
  await page.getByRole("link", { name: "About" }).click();

  await expect(page).toHaveURL("/about");
  await expect(page.getByRole("heading", { level: 1 })).toContainText("About");
});

test("submits the contact form", async ({ page }) => {
  await page.goto("/contact");
  await page.getByLabel("Email").fill("test@example.com");
  await page.getByRole("button", { name: "Send" }).click();
  await expect(page.getByText("Thanks!")).toBeVisible();
});
```

Run with:

```bash
npx playwright test
```

Testing against a **production build** (`build` + `start`) is more realistic than the dev server. Keep E2E tests focused on critical paths (sign up, checkout), because they are slower than unit tests.

---

## 16.4 Mocking requests

Tests should not depend on real external services. **Mock Service Worker (MSW)** intercepts network requests and returns fake responses, so your code runs unchanged.

```bash
npm install -D msw
```

```ts
// mocks/handlers.ts
import { http, HttpResponse } from "msw";

export const handlers = [
  http.get("https://api.example.com/users", () => {
    return HttpResponse.json([
      { id: 1, name: "Ada" },
      { id: 2, name: "Linus" },
    ]);
  }),
];
```

```ts
// vitest.setup.ts (Node server setup)
import { setupServer } from "msw/node";
import { beforeAll, afterEach, afterAll } from "vitest";
import { handlers } from "./mocks/handlers";

export const server = setupServer(...handlers);

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());
```

Override a handler for a single test to simulate failures:

```ts
import { http, HttpResponse } from "msw";
import { server } from "../vitest.setup";

server.use(
  http.get("https://api.example.com/users", () =>
    HttpResponse.json({ error: "Server error" }, { status: 500 })
  )
);
```

For E2E tests, Playwright can also intercept requests directly with `page.route()`:

```ts
await page.route("**/api/users", (route) =>
  route.fulfill({ json: [{ id: 1, name: "Ada" }] })
);
```

Good testing balance: **many** quick unit tests for logic, **some** component tests for UI behavior, and **a few** E2E tests for the most important user journeys.

[Previous](./[15]-TypeScript-With-Nextjs.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[17]-Performance-Tuning.md)