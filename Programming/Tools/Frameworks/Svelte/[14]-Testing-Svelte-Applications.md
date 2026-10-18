[Previous](./[13]-TypeScript-And-Tooling.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[15]-Performance-And-Accessibility.md)

*Quality And Delivery*

# Lesson 14 - Testing Svelte Applications

Automated tests catch mistakes before your users do. A healthy project usually has many fast **unit and component tests** and a few slower **end-to-end tests**.

## 14.1 Vitest and Testing Library

**Vitest** is a test runner that shares your Vite config. **Testing Library** renders components and lets you interact with them like a user.

The easy way to add Vitest to a project:

```bash
npx sv add vitest
```

To test components, also install:

```bash
npm install -D @testing-library/svelte @testing-library/jest-dom @testing-library/user-event jsdom
```

Configure `vite.config.js`:

```js
import { defineConfig } from 'vitest/config';
import { sveltekit } from '@sveltejs/kit/vite';
import { svelteTesting } from '@testing-library/svelte/vite';

export default defineConfig({
  plugins: [sveltekit(), svelteTesting()],
  test: {
    environment: 'jsdom',
    setupFiles: ['./vitest-setup.js'],
    include: ['src/**/*.{test,spec}.{js,ts}']
  }
});
```

Create `vitest-setup.js` in the project root:

```js
import '@testing-library/jest-dom/vitest';
```

A first test of a plain function:

```js
// src/lib/math.js
export function add(a, b) {
  return a + b;
}
```

```js
// src/lib/math.test.js
import { describe, it, expect } from 'vitest';
import { add } from './math.js';

describe('add', () => {
  it('adds two numbers', () => {
    expect(add(2, 3)).toBe(5);
  });
});
```

Run all tests with `npx vitest` (or the script created by `sv add`).

Tip: if a test file uses runes directly, name it `something.svelte.test.js` so the compiler understands them.

---

## 14.2 Component tests

Render the component, find elements the way a user would (by role or text), interact, then check the result.

```svelte
<!-- src/lib/Counter.svelte -->
<script>
  let { start = 0 } = $props();
  let count = $state(start);
</script>

<p>Count: {count}</p>
<button onclick={() => count++}>Increment</button>
```

```js
// src/lib/Counter.test.js
import { render, screen } from '@testing-library/svelte';
import userEvent from '@testing-library/user-event';
import { describe, it, expect } from 'vitest';
import Counter from './Counter.svelte';

describe('Counter', () => {
  it('starts at the given value', () => {
    render(Counter, { props: { start: 5 } });
    expect(screen.getByText('Count: 5')).toBeInTheDocument();
  });

  it('increments when the button is clicked', async () => {
    const user = userEvent.setup();
    render(Counter);

    await user.click(screen.getByRole('button', { name: 'Increment' }));

    expect(screen.getByText('Count: 1')).toBeInTheDocument();
  });
});
```

Good habits:

- Query by **role, label, or text** (`getByRole`, `getByLabelText`, `getByText`) rather than CSS classes. This also encourages accessible markup.
- Use `await` with user interactions, because Svelte updates the DOM asynchronously.
- Test **behavior** (what the user sees), not internal variable names.
- To test reactive code outside a component, call `flushSync()` from `svelte` after a change so updates apply immediately.

---

## 14.3 End-to-end tests with Playwright

End-to-end (E2E) tests drive a real browser through your running app, covering routing, server code, and the browser together.

```bash
npx sv add playwright
```

This creates a `playwright.config.js`. A typical setup builds the app and tests the production preview:

```js
// playwright.config.js
export default {
  webServer: {
    command: 'npm run build && npm run preview',
    port: 4173
  },
  testDir: 'e2e'
};
```

A test:

```js
// e2e/home.test.js
import { expect, test } from '@playwright/test';

test('home page has a heading', async ({ page }) => {
  await page.goto('/');
  await expect(page.getByRole('heading', { level: 1 })).toBeVisible();
});

test('can navigate to the about page', async ({ page }) => {
  await page.goto('/');
  await page.getByRole('link', { name: 'About' }).click();
  await expect(page).toHaveURL('/about');
});
```

Run with `npx playwright test`. The first time, install browsers with `npx playwright install`.

Use E2E tests for the most important user flows (sign up, checkout, login). They are slower and harder to maintain than unit tests, so keep them focused.

---

## 14.4 Mocking

Mocking replaces a real dependency with a fake one so tests are fast, predictable, and isolated.

**Mock functions** record how they were called:

```js
import { vi, it, expect } from 'vitest';

it('calls the callback', () => {
  const onselect = vi.fn();
  onselect('red');

  expect(onselect).toHaveBeenCalledWith('red');
});
```

**Mock a whole module:**

```js
import { vi } from 'vitest';

vi.mock('$lib/api.js', () => ({
  getUsers: vi.fn().mockResolvedValue([{ id: 1, name: 'Ada' }])
}));
```

**Mock `fetch`:**

```js
import { vi, it, expect } from 'vitest';

it('loads data', async () => {
  vi.stubGlobal(
    'fetch',
    vi.fn().mockResolvedValue({
      ok: true,
      json: async () => ({ title: 'Hello' })
    })
  );

  const res = await fetch('/api/post');
  const data = await res.json();

  expect(data.title).toBe('Hello');
  vi.unstubAllGlobals();
});
```

**Mock SvelteKit modules**, which only exist inside a running SvelteKit app:

```js
vi.mock('$app/navigation', () => ({
  goto: vi.fn(),
  invalidateAll: vi.fn()
}));
```

Guidelines:

- Mock at the **edges** (network, database, time), not your own components.
- Reset mocks between tests (`vi.clearAllMocks()` in `beforeEach`) so they do not affect each other.
- For many realistic API responses, consider the Mock Service Worker (MSW) library.

[Previous](./[13]-TypeScript-And-Tooling.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[15]-Performance-And-Accessibility.md)