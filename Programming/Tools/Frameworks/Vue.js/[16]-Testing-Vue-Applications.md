[Previous](./[15]-Nuxt-And-Server-Side-Rendering.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[17]-Performance-Optimization.md)

*Ecosystem And Delivery*

# Lesson 16 - Testing Vue Applications

## 16.1 Vitest and Vue Test Utils

**Automated tests** check that your code works and keep working as it changes.

- **Vitest** is a fast test runner built for Vite projects (similar to Jest).
- **Vue Test Utils** is the official library for mounting and interacting with components in tests.

Install them (or select Vitest in `create-vue`):

```bash
npm install -D vitest @vue/test-utils jsdom
```

`jsdom` simulates a browser environment. Configure it in `vite.config.js`:

```js
import { defineConfig } from 'vitest/config'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
  plugins: [vue()],
  test: {
    environment: 'jsdom'
  }
})
```

Add a script to `package.json`:

```json
{
  "scripts": {
    "test": "vitest"
  }
}
```

A first test for a plain function, `sum.test.js`:

```js
import { describe, it, expect } from 'vitest'

function sum(a, b) {
  return a + b
}

describe('sum', () => {
  it('adds two numbers', () => {
    expect(sum(1, 2)).toBe(3)
  })
})
```

Run it with `npm test`. Vitest watches your files and reruns tests when they change.

Test file naming: `*.test.js` or `*.spec.js`, usually placed next to the file being tested or in a `__tests__` folder.

---

## 16.2 Component testing

Mount a component, interact with it like a user would, and check the result.

The component, `Counter.vue`:

```vue
<script setup>
import { ref } from 'vue'

const props = defineProps({ initial: { type: Number, default: 0 } })
const count = ref(props.initial)
</script>

<template>
  <p data-test="count">Count: {{ count }}</p>
  <button @click="count++">Increment</button>
</template>
```

The test, `Counter.test.js`:

```js
import { describe, it, expect } from 'vitest'
import { mount } from '@vue/test-utils'
import Counter from './Counter.vue'

describe('Counter', () => {
  it('renders the initial value', () => {
    const wrapper = mount(Counter, { props: { initial: 5 } })
    expect(wrapper.get('[data-test="count"]').text()).toBe('Count: 5')
  })

  it('increments when the button is clicked', async () => {
    const wrapper = mount(Counter)

    await wrapper.get('button').trigger('click')

    expect(wrapper.get('[data-test="count"]').text()).toBe('Count: 1')
  })
})
```

Key points:

- `mount()` renders the component and returns a **wrapper**.
- `wrapper.get(selector)` finds an element (and throws if it is missing). `find` returns an empty wrapper instead.
- `trigger('click')` simulates events. **Always `await` it**, because the DOM updates asynchronously.
- `setValue()` fills inputs: `await wrapper.get('input').setValue('hello')`.
- Test **behavior** (what the user sees and does), not internal implementation details.
- Use `data-test` attributes for selectors so style changes don't break tests.

Testing emitted events:

```js
const wrapper = mount(MyButton)
await wrapper.get('button').trigger('click')

expect(wrapper.emitted()).toHaveProperty('add')
expect(wrapper.emitted('add')[0]).toEqual([5])   // payload of first emit
```

Testing slots:

```js
const wrapper = mount(BaseCard, {
  slots: { default: '<p>Slot content</p>' }
})
expect(wrapper.html()).toContain('Slot content')
```

---

## 16.3 Mocking stores and routers

Components often depend on Pinia stores or Vue Router. In unit tests you provide controlled replacements.

**Pinia**: use `@pinia/testing`:

```bash
npm install -D @pinia/testing
```

```js
import { mount } from '@vue/test-utils'
import { createTestingPinia } from '@pinia/testing'
import { vi } from 'vitest'
import CartSummary from './CartSummary.vue'
import { useCartStore } from '@/stores/cart'

it('shows the total items', () => {
  const wrapper = mount(CartSummary, {
    global: {
      plugins: [
        createTestingPinia({
          createSpy: vi.fn,   // actions become spies
          initialState: {
            cart: { items: [{ id: 1, price: 10, quantity: 2 }] }
          }
        })
      ]
    }
  })

  const cart = useCartStore()
  expect(wrapper.text()).toContain('2')
  expect(cart.addItem).not.toHaveBeenCalled()   // actions are stubbed by default
})
```

**Vue Router**: use a mock for simple components:

```js
import { vi } from 'vitest'

const push = vi.fn()

const wrapper = mount(MyComponent, {
  global: {
    mocks: {
      $router: { push },
      $route: { params: { id: '42' } }
    },
    stubs: { RouterLink: true }   // replace RouterLink with a stub
  }
})

await wrapper.get('button').trigger('click')
expect(push).toHaveBeenCalledWith('/home')
```

When components use `useRouter()` and `useRoute()` from `<script setup>`, it is usually better to install a real router created with `createRouter` and `createMemoryHistory()`, then call `router.push('/')` and `await router.isReady()` before mounting.

**Mocking network requests:**

```js
import { vi } from 'vitest'

global.fetch = vi.fn(() =>
  Promise.resolve({
    ok: true,
    json: () => Promise.resolve([{ id: 1, title: 'Test post' }])
  })
)
```

After async operations, use `flushPromises` from `@vue/test-utils` to wait for the UI to settle.

---

## 16.4 End-to-end tests

**End-to-end (E2E) tests** run the whole application in a real browser, clicking and typing like a real user. They are slower than unit tests but catch problems that unit tests cannot (routing, real API calls, browser behavior).

Popular tools:

| Tool | Notes |
|---|---|
| **Playwright** | Multi-browser (Chromium, Firefox, WebKit), fast, great tooling |
| **Cypress** | Friendly interface with visual test runner |

Setting up Playwright:

```bash
npm init playwright@latest
```

An example test, `e2e/todo.spec.js`:

```js
import { test, expect } from '@playwright/test'

test('user can add a todo', async ({ page }) => {
  await page.goto('http://localhost:5173/')

  await page.getByPlaceholder('What needs to be done?').fill('Learn Vue')
  await page.getByRole('button', { name: 'Add' }).click()

  await expect(page.getByText('Learn Vue')).toBeVisible()
})
```

Run with `npx playwright test`. Start your dev server first, or configure `webServer` in `playwright.config.js` to start it automatically.

The **testing pyramid** is a useful guide:

```
        /\
       /E2E\        few, slow, high confidence
      /------\
     / Compo- \     some, medium speed
    /  nent    \
   /------------\
  /  Unit tests  \  many, fast, small scope
 /________________\
```

Write many fast unit tests for logic (composables, utilities), a good number of component tests for UI behavior, and a few E2E tests for the most important user flows such as signing in or checking out.

---

[Previous](./[15]-Nuxt-And-Server-Side-Rendering.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[17]-Performance-Optimization.md)