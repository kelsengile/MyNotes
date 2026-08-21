[Previous](./[15]-Linting-and-Formatting.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[17]-Component-Based-UI.md)

*Tooling & Build Systems*

# Lesson 16 - Testing JavaScript (Unit & End-to-End Testing)

## 16.1 Why Automated Tests Matter

Manually clicking through an app after every change doesn't scale, and it's easy to miss a broken edge case a human wouldn't think to try. **Automated tests** are code that verifies other code behaves correctly, and can be re-run in seconds, every time something changes, catching regressions before they reach users.

---

## 16.2 Unit Tests

A **unit test** checks a single, isolated piece of logic — typically one function. Using a framework like **Vitest** or **Jest**:

```js
// sum.js
export function sum(a, b) { return a + b; }

// sum.test.js
import { sum } from "./sum.js";
import { test, expect } from "vitest";

test("adds two numbers", () => {
  expect(sum(2, 3)).toBe(5);
});
```

Unit tests run fast (no browser, no network) and are the foundation most testing strategies are built on.

---

## 16.3 Integration Tests

An **integration test** checks that multiple pieces work correctly together — a component that fetches data and renders it, for example — rather than testing pure logic in isolation. Tools like **React Testing Library** render components in a simulated DOM and let you assert on what a user would actually see:

```js
render(<UserProfile userId={1} />);
expect(await screen.findByText("Ada Lovelace")).toBeInTheDocument();
```

---

## 16.4 End-to-End (E2E) Tests

**End-to-end tests** drive a real browser through actual user flows — logging in, adding an item to a cart, submitting a form — verifying the whole system (front end, back end, database) works together. Tools like **Playwright** and **Cypress** automate this:

```js
await page.goto("/login");
await page.fill("#email", "user@example.com");
await page.click("button[type=submit]");
await expect(page.locator("h1")).toHaveText("Welcome");
```

E2E tests give the highest confidence but are the slowest to run and the most brittle to small UI changes, so most projects write far fewer of them than unit tests.

---

## 16.5 The Testing Pyramid

A common mental model, the **testing pyramid**, suggests writing many fast unit tests, a moderate number of integration tests, and few, high-value end-to-end tests — balancing confidence against speed and maintenance cost. No project needs 100% coverage everywhere; the goal is testing the logic most likely to break and most costly to get wrong.

---

[Previous](./[15]-Linting-and-Formatting.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[17]-Component-Based-UI.md)
