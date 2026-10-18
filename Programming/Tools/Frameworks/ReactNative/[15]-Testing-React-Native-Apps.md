[Previous](./[14]-Debugging-React-Native-Apps.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[16]-Performance-Optimization.md)

*Quality And Delivery*

# Lesson 15 - Testing React Native Apps

Automated tests catch bugs before your users do and let you change code with confidence.

## 15.1 Jest and React Native Testing Library

**Jest** is the test runner. **React Native Testing Library (RNTL)** renders components and lets you interact with them the way a user would: by finding text, labels, and roles instead of implementation details.

**Setup (Expo)**

```bash
npx expo install jest-expo jest @types/jest --dev
npm install --save-dev @testing-library/react-native
```

In `package.json`:

```json
{
  "scripts": { "test": "jest" },
  "jest": { "preset": "jest-expo" }
}
```

**A component to test**

```jsx
// components/Counter.js
import { useState } from 'react';
import { View, Text, Pressable } from 'react-native';

export default function Counter() {
  const [count, setCount] = useState(0);
  return (
    <View>
      <Text>Count: {count}</Text>
      <Pressable accessibilityRole="button" onPress={() => setCount(count + 1)}>
        <Text>Increment</Text>
      </Pressable>
    </View>
  );
}
```

**The test**

```jsx
// components/Counter.test.js
import { render, screen, userEvent } from '@testing-library/react-native';
import Counter from './Counter';

describe('Counter', () => {
  it('starts at zero', () => {
    render(<Counter />);
    expect(screen.getByText('Count: 0')).toBeOnTheScreen();
  });

  it('increments when pressed', async () => {
    const user = userEvent.setup();
    render(<Counter />);

    await user.press(screen.getByRole('button', { name: 'Increment' }));

    expect(screen.getByText('Count: 1')).toBeOnTheScreen();
  });
});
```

Run the tests:

```bash
npm test
npm test -- --watch      # re-run on file changes
npm test -- --coverage   # coverage report
```

**Query types in RNTL**

| Prefix | Behavior |
|---|---|
| `getBy...` | Returns one element, throws if not found |
| `queryBy...` | Returns one element or `null` (good for "should not exist" checks) |
| `findBy...` | Waits asynchronously for an element to appear |
| `getAllBy...` | Returns multiple elements |

Prefer queries by **role, label, and text**, which also encourages accessible UI. Use `testID` only when nothing else fits.

**Testing async behavior**

```jsx
expect(await screen.findByText('Loaded!')).toBeOnTheScreen();
```

Older code may use `fireEvent.press(...)`. `userEvent` is closer to real user behavior and is generally preferred.

---

## 15.2 Mocking native modules

Jest runs in Node.js, not on a device, so native modules (camera, storage, location) do not exist there. You replace them with **mocks**.

**Mock a module**

```jsx
jest.mock('expo-secure-store', () => ({
  getItemAsync: jest.fn().mockResolvedValue('fake-token'),
  setItemAsync: jest.fn().mockResolvedValue(undefined),
  deleteItemAsync: jest.fn().mockResolvedValue(undefined),
}));
```

**Mock AsyncStorage with the official helper**

```jsx
jest.mock('@react-native-async-storage/async-storage', () =>
  require('@react-native-async-storage/async-storage/jest/async-storage-mock')
);
```

**Mock network requests**

```jsx
global.fetch = jest.fn(() =>
  Promise.resolve({
    ok: true,
    json: () => Promise.resolve([{ id: 1, title: 'Hello' }]),
  })
);

it('shows posts', async () => {
  render(<Posts />);
  expect(await screen.findByText('Hello')).toBeOnTheScreen();
  expect(fetch).toHaveBeenCalledTimes(1);
});
```

For larger projects, **Mock Service Worker (MSW)** can intercept requests at the network layer so you do not need to mock `fetch` by hand.

**Mock navigation**

```jsx
const mockNavigate = jest.fn();

render(<HomeScreen navigation={{ navigate: mockNavigate }} />);
await user.press(screen.getByText('Go to Details'));
expect(mockNavigate).toHaveBeenCalledWith('Details');
```

**Put shared mocks in a setup file** and reference it from the Jest config:

```json
{
  "jest": {
    "preset": "jest-expo",
    "setupFilesAfterEnv": ["<rootDir>/jest.setup.js"]
  }
}
```

Reset mocks between tests with `jest.clearAllMocks()` in a `beforeEach`. Mock only what you must, because too many mocks make tests less meaningful.

---

## 15.3 End-to-end tests with Detox or Maestro

**End-to-end (E2E) tests** run the real app on a simulator or device and drive it like a user. They catch problems that unit tests cannot, such as navigation, native integration, and permissions.

**Maestro** is known for being simple. Tests are written in YAML and need little setup.

```yaml
# flows/login.yaml
appId: com.mycompany.myapp
---
- launchApp
- tapOn: "Email"
- inputText: "ana@example.com"
- tapOn: "Password"
- inputText: "secret123"
- tapOn: "Log in"
- assertVisible: "Welcome back"
```

```bash
maestro test flows/login.yaml
```

**Detox** is a gray-box framework from Wix. It synchronizes with the app, waiting automatically for animations and network activity, which reduces flaky tests. Tests are written in JavaScript.

```jsx
describe('Login', () => {
  beforeAll(async () => {
    await device.launchApp();
  });

  it('logs the user in', async () => {
    await element(by.id('email-input')).typeText('ana@example.com');
    await element(by.id('password-input')).typeText('secret123');
    await element(by.id('login-button')).tap();
    await expect(element(by.text('Welcome back'))).toBeVisible();
  });
});
```

| | Maestro | Detox |
|---|---|---|
| Test language | YAML | JavaScript / TypeScript |
| Setup effort | Low | Higher |
| Synchronization | Smart waiting | Automatic gray-box sync |
| Best for | Quick flows, small teams | Deep integration, large suites |

E2E tests are **slower and more fragile** than unit tests, so use them for your most important user journeys such as sign-up, login, and checkout. Cloud services (such as EAS Workflows or Maestro Cloud) can run them in CI.

---

## 15.4 Test strategy

You cannot test everything, so test where it matters most. A common model is the **testing pyramid**:

```
        /\
       /E2E\          Few: slow, high confidence, critical flows
      /------\
     /  Integ  \      Some: screens with mocked network and navigation
    /------------\
   /     Unit      \  Many: fast, small functions and components
  /------------------\
```

**What to test**

- **Business logic** — pure functions, reducers, validation, formatting. These are the easiest and most valuable tests.
- **Components** — behavior from the user's point of view (what they see and what happens when they press).
- **Screens** — loading, error, empty, and success states.
- **Critical journeys** — with a few E2E tests.

**What not to test**

- Third-party library internals.
- Exact styles or implementation details, which make tests break when you refactor.
- Trivial code with no logic.

**Good habits**

- Name tests by behavior: `it('shows an error when the password is too short')`.
- Keep each test **independent** so they can run in any order.
- Follow **Arrange, Act, Assert**: set up, perform the action, check the result.
- Run tests on **every pull request** in continuous integration (CI), together with linting and type checking:

```bash
npx tsc --noEmit
npx eslint .
npm test -- --ci
```

- Fix or delete **flaky tests** quickly, because an unreliable suite gets ignored.
- Also test manually on **real devices**, especially for performance, gestures, and platform differences.

---

[Previous](./[14]-Debugging-React-Native-Apps.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[16]-Performance-Optimization.md)