[Previous](./[12]-Debugging-And-Performance.md) | [Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[14]-Packaging-And-Distribution.md)

*Hardening And Delivery*

# Lesson 13 - Testing Electron Applications

## 13.1 Unit tests for main and renderer code

Unit tests check small pieces of logic in isolation and run fast. The trick in Electron is to keep **pure logic separate from Electron APIs**, so most code can be tested without launching the app.

Hard to test (logic mixed with Electron):

```js
ipcMain.handle('notes:add', (_e, title) => {
  if (!title || title.length > 100) throw new Error('Invalid title')
  return db.prepare('INSERT INTO notes (title) VALUES (?)').run(title).lastInsertRowid
})
```

Easy to test (logic extracted):

```js
// src/notes-service.js
function validateTitle(title) {
  if (typeof title !== 'string' || title.length === 0 || title.length > 100) {
    throw new Error('Invalid title')
  }
  return title.trim()
}

function createNotesService(db) {
  return {
    add(title) {
      const clean = validateTitle(title)
      return db.insertNote(clean)
    }
  }
}

module.exports = { validateTitle, createNotesService }
```

Test it with any runner. **Vitest** example:

```bash
npm install --save-dev vitest
```

```js
// src/notes-service.test.js
import { describe, it, expect, vi } from 'vitest'
import { validateTitle, createNotesService } from './notes-service'

describe('validateTitle', () => {
  it('trims valid titles', () => {
    expect(validateTitle('  hello ')).toBe('hello')
  })

  it('rejects empty titles', () => {
    expect(() => validateTitle('')).toThrow('Invalid title')
  })
})

describe('notes service', () => {
  it('stores the cleaned title', () => {
    const db = { insertNote: vi.fn().mockReturnValue(7) }
    const service = createNotesService(db)
    expect(service.add(' note ')).toBe(7)
    expect(db.insertNote).toHaveBeenCalledWith('note')
  })
})
```

Add `"test": "vitest run"` to your scripts and run `npm test`.

**Renderer code** (React components, plain DOM scripts) can be tested with a DOM environment such as `jsdom` plus Testing Library. Set `environment: 'jsdom'` in your Vitest config and provide a fake `window.api` (see 13.3).

---

## 13.2 End-to-end tests with Playwright

End-to-end (E2E) tests launch the **real app** and interact with it like a user. Playwright has built-in Electron support.

```bash
npm install --save-dev @playwright/test
```

```js
// tests/app.spec.js
const { test, expect, _electron: electron } = require('@playwright/test')

test('app launches and shows the title', async () => {
  const app = await electron.launch({ args: ['.'] })

  // Get the first window (a normal Playwright Page)
  const window = await app.firstWindow()
  await window.waitForLoadState('domcontentloaded')

  await expect(window.locator('h1')).toHaveText('Notes')

  // Interact like a user
  await window.fill('#title', 'First note')
  await window.click('#save')
  await expect(window.locator('li')).toHaveText('First note')

  await app.close()
})
```

You can also run code **inside the main process** to check native state:

```js
const name = await app.evaluate(({ app }) => app.getName())
expect(name).toBe('my-app')

const isPackaged = await app.evaluate(({ app }) => app.isPackaged)
expect(isPackaged).toBe(false)
```

Tips:

- Give elements stable hooks such as `data-testid` attributes instead of relying on styling.
- Use a **temporary user data directory** for tests so they never touch real data: launch with `args: ['.', '--user-data-dir=/tmp/test-profile']`, or set it in code when an environment variable like `NODE_ENV=test` is present.
- Playwright can record traces and screenshots on failure, which makes debugging CI failures much easier.
- Native dialogs cannot be clicked by Playwright. Stub them in the main process during tests (see 13.3).

---

## 13.3 Mocking IPC and native APIs

Tests should not depend on real file pickers, notifications, or the operating system. **Mock** them.

**Mock `window.api` in renderer tests.** The page only talks to your preload API, so replace it with a fake:

```js
// renderer.test.js (jsdom environment)
import { beforeEach, it, expect, vi } from 'vitest'

beforeEach(() => {
  window.api = {
    notes: {
      list: vi.fn().mockResolvedValue([{ id: 1, title: 'Mocked note' }]),
      add: vi.fn().mockResolvedValue(2)
    }
  }
  document.body.innerHTML = '<ul id="list"></ul>'
})

it('renders notes from the API', async () => {
  const { refresh } = await import('./renderer.js')
  await refresh()
  expect(document.querySelector('li').textContent).toBe('Mocked note')
})
```

**Mock the `electron` module in main-process unit tests:**

```js
import { vi, it, expect } from 'vitest'

vi.mock('electron', () => ({
  app: { getPath: vi.fn().mockReturnValue('/tmp/test') },
  dialog: { showOpenDialog: vi.fn().mockResolvedValue({ canceled: false, filePaths: ['/tmp/a.txt'] }) },
  ipcMain: { handle: vi.fn() }
}))
```

**Stub native dialogs in E2E tests** by replacing the method inside the running main process:

```js
await app.evaluate(({ dialog }) => {
  dialog.showOpenDialog = async () => ({
    canceled: false,
    filePaths: ['/tmp/fixture.txt']
  })
})
await window.click('#open')
```

Keep the number of mocks small. A test that mocks everything proves little, so use unit tests for logic and a few real E2E tests for the critical user flows.

---

## 13.4 Continuous integration

**Continuous integration (CI)** runs your tests automatically on every push, so regressions are caught early. Because Electron apps behave differently per platform, test on all of them.

A GitHub Actions workflow:

```yaml
# .github/workflows/test.yml
name: Test

on: [push, pull_request]

jobs:
  test:
    strategy:
      fail-fast: false
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
    runs-on: ${{ matrix.os }}

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm

      - run: npm ci

      - name: Unit tests
        run: npm test

      - name: E2E tests (Linux needs a virtual display)
        if: runner.os == 'Linux'
        run: xvfb-run --auto-servernum npx playwright test

      - name: E2E tests
        if: runner.os != 'Linux'
        run: npx playwright test

      - name: Upload Playwright report
        if: failure()
        uses: actions/upload-artifact@v4
        with:
          name: playwright-report-${{ matrix.os }}
          path: playwright-report/
```

Notes:

- **Linux needs `xvfb`** (a virtual display) because Electron opens real windows.
- Use `npm ci` for reproducible installs.
- Run slow packaging and signing steps only on release branches or tags, not on every commit.
- Fail the build on lint, type, and test errors so problems cannot be merged unnoticed.

---

[Previous](./[12]-Debugging-And-Performance.md) | [Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[14]-Packaging-And-Distribution.md)