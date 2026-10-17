[Previous](./[15]-Authentication-And-Security.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[17]-Building-And-Deployment.md)

*Quality And Delivery*

# Lesson 16 - Testing

Code that works today can quietly break tomorrow when someone changes something nearby. **Automated tests** are small programs that check that your application still behaves correctly, and they can be run in seconds, over and over. This lesson explains why testing matters, the main kinds of tests, the tools frameworks use, and how to write your first one.

## 16.1 Why Test

Imagine changing one function in a project with 200 features. How do you know you did not break any of them? You could click through the whole app by hand after every change, but that is slow, boring, and easy to get wrong. Automated tests do that checking for you, in seconds.

### Benefits of testing

| Benefit | What it means in practice |
|---|---|
| **Catch bugs early** | Problems are found on your machine, not by your users |
| **Prevent regressions** | A **regression** is something that used to work and broke. A test that once passed fails loudly when it breaks again |
| **Confidence to change code** | You can refactor and upgrade dependencies knowing tests will warn you |
| **Living documentation** | Tests show how a piece of code is expected to behave |
| **Better design** | Code that is easy to test tends to be small and focused, with clearly separated responsibilities (Lesson 6) |
| **Faster debugging** | A failing test points to the area of the problem |

### What testing cannot do

Tests can show that bugs *exist*, but they cannot prove there are *none*. They only check the situations you thought to write down. Testing is also an investment: tests need to be maintained along with the code, so the goal is **useful** tests, not the largest possible number.

### Testing and the workflow

Tests are usually run:

- **Locally**, while you develop, often automatically whenever a file changes.
- **Automatically in CI**, on every push, so broken code is caught before it is merged or deployed (Lesson 17).

---

## 16.2 Unit, Integration, and End-to-End Tests

Tests are grouped by how much of the application they exercise at once.

### Unit tests

A **unit test** checks one small piece of code, usually a single function or class, **in isolation**. It does not touch the network, a real database, or the file system. Unit tests are tiny and extremely fast, so you can have thousands of them.

*Example question:* "Does `applyDiscount(200, 25)` return 150?"

### Integration tests

An **integration test** checks that **several parts work together**: for example, a request goes through the routing, middleware, a handler, and a real (often temporary) database, and the response is correct. They are slower than unit tests, but they find problems in the *connections* between parts, which unit tests cannot.

*Example question:* "When I POST a new task to the API, is it saved and returned by a later GET?"

### End-to-end (E2E) tests

An **end-to-end test** drives the **whole application the way a real user would**, usually by controlling an actual browser or a mobile emulator: open the page, type into the form, click the button, and check what appears. They give the most realistic confidence but are the slowest and most fragile.

*Example question:* "Can a user sign up, log in, add a task, and see it on the list?"

### The test pyramid

A popular guideline is the **test pyramid**: write **many** fast unit tests, a **moderate** number of integration tests, and **few** end-to-end tests.

```text
          /\
         /  \        End-to-end   (few: slow, realistic)
        /----\
       /      \      Integration  (some: moderate speed)
      /--------\
     /          \    Unit         (many: fast, focused)
    /------------\
```

| | Unit | Integration | End-to-end |
|---|---|---|---|
| **Scope** | One function or class | Several parts together | The whole app |
| **Speed** | Milliseconds | Seconds | Many seconds to minutes |
| **Cost to maintain** | Low | Medium | High |
| **Finds** | Logic errors | Wiring and data problems | Broken user journeys |
| **Dependencies** | Faked or none | Real or semi-real | Everything real |

### Test doubles: fakes, stubs, and mocks

To test a unit in isolation, you replace its real dependencies with stand-ins, called **test doubles**:

| Double | What it does | Example |
|---|---|---|
| **Stub** | Returns canned answers | A fake payment service that always says "approved" |
| **Fake** | A simplified working version | An in-memory database in place of a real one |
| **Mock** | Records how it was called, so the test can check it | Verifying that an email service's `send` was called once |

This is where dependency injection (Lesson 3) pays off: if a class receives its mailer from outside, a test can hand it a fake mailer.

### Other kinds of tests you may hear about

- **Component tests** render a single UI component and check its output and behavior.
- **Smoke tests** are a very small set of checks that the most vital things work after a deployment.
- **Contract tests** verify that two services agree on the shape of the data they exchange.
- **Performance and load tests** measure speed and capacity (Lesson 18).

---

## 16.3 Testing Tools and Frameworks

Testing has its own frameworks. A **test runner** finds your tests, runs them, and reports the results, and an **assertion library** provides ways to state expectations ("this should equal that"). Most ecosystems bundle both.

| Ecosystem | Unit / integration testing | End-to-end / UI testing |
|---|---|---|
| **JavaScript / TypeScript** | Jest, Vitest, Mocha, Node's built-in test runner | Playwright, Cypress |
| **React components** | Testing Library with Jest or Vitest | Playwright, Cypress |
| **Express APIs** | A test runner plus Supertest for HTTP calls | Playwright |
| **Angular** | Jasmine and Karma, or Jest | Playwright, Cypress |
| **Vue.js / Svelte** | Vitest with Testing Library | Playwright, Cypress |
| **Python** | pytest, unittest | Playwright, Selenium |
| **Django / Flask / FastAPI** | pytest with each framework's test client | Playwright, Selenium |
| **Java / Spring Boot** | JUnit, Mockito, Spring's test support | Selenium, Playwright |
| **PHP / Laravel** | PHPUnit, Pest | Laravel Dusk |
| **C# / ASP.NET Core** | xUnit, NUnit, MSTest | Playwright |
| **Ruby on Rails** | Minitest, RSpec | Capybara |
| **Flutter** | `flutter_test` (unit and widget tests) | `integration_test` |
| **React Native** | Jest with Testing Library | Detox, Maestro |

### Framework test clients

Most backend frameworks provide a **test client** that sends fake requests directly to your application *without* starting a real network server. This is fast and makes integration tests easy. Flask, Django, FastAPI, Laravel, Spring (`MockMvc`), and ASP.NET Core (a test server) all have one.

### Choosing

You rarely need to compare test tools deeply. Use the one your framework recommends or that is already set up in the project, and put your effort into writing good tests rather than choosing the perfect library.

---

## 16.4 Writing Your First Test

### The Arrange-Act-Assert pattern

Nearly every test has three steps:

1. **Arrange**: set up the data and conditions.
2. **Act**: run the code being tested.
3. **Assert**: check that the result is what you expected.

### A unit test in Python (pytest)

Suppose we have a function that applies a percentage discount and rejects invalid percentages:

```python
def apply_discount(price, percent):
    if not 0 <= percent <= 100:
        raise ValueError("percent must be between 0 and 100")
    return price * (1 - percent / 100)
```

Here is a test file for it. By convention, test files and functions start with `test_`, so the runner can find them:

```python
import pytest

from calculator import apply_discount


def test_apply_discount_reduces_price():
    # Arrange
    price, percent = 200, 25

    # Act
    result = apply_discount(price, percent)

    # Assert
    assert result == 150


def test_apply_discount_rejects_invalid_percent():
    with pytest.raises(ValueError):
        apply_discount(100, 150)
```

Running the tests with `python -m pytest` reports:

```text
..                                                                       [100%]
2 passed in 0.01s
```

Each dot is a passing test. If a test fails, the runner shows which assertion failed and what values it saw, which is the information you need to fix it.

Notice the second test: **good tests cover the unhappy paths too**. Checking that bad input is rejected is as important as checking that good input works.

### The same idea in JavaScript

Node.js includes a built-in test runner, so no extra packages are needed. First, the function:

```javascript
function applyDiscount(price, percent) {
  if (percent < 0 || percent > 100) {
    throw new RangeError("percent must be between 0 and 100");
  }
  return price * (1 - percent / 100);
}

module.exports = { applyDiscount };
```

Then its test:

```javascript
const test = require("node:test");
const assert = require("node:assert/strict");
const { applyDiscount } = require("./discount");

test("applyDiscount reduces the price", () => {
  assert.equal(applyDiscount(200, 25), 150);
});

test("applyDiscount rejects an invalid percentage", () => {
  assert.throws(() => applyDiscount(100, 150), RangeError);
});
```

Run it with `node --test`. Both tests pass. The structure is identical to the Python version, with a different syntax, which is true for nearly every testing tool: you give each test a name, arrange, act, and assert.

### An integration-style test with a framework test client

Back in Lesson 4, we built a small Flask project. Here is a test that sends a fake request to the home page and checks the response, with no real server involved:

```python
from app import app


def test_index_lists_tasks():
    client = app.test_client()
    response = client.get("/")
    assert response.status_code == 200
    assert b"Learn what a framework is" in response.data
```

Run it from the project folder with `python -m pytest`. It passes because the page contains the task title we expect. This test exercises routing, the handler, and template rendering together.

### Good test habits

1. **Test behavior, not implementation.** Check *what* the code produces, not *how* it does it internally, so small refactors do not break tests.
2. **Keep tests independent.** Each test should set up its own data and not rely on another test having run first.
3. **Give tests descriptive names**, such as `test_apply_discount_rejects_invalid_percent`. The name should read like a sentence about expected behavior.
4. **Keep each test focused** on one idea.
5. **Make tests fast and deterministic.** A test should give the same result every time, with no dependence on the current time, random numbers, or the internet unless those are controlled.
6. **When you find a bug, write a failing test for it first**, then fix it. The test then guards against the bug coming back.

### Test-driven development (TDD)

Some developers write tests *before* the code, following a short cycle called **red-green-refactor**:

1. **Red**: write a test for behavior that does not exist yet, and watch it fail.
2. **Green**: write just enough code to make it pass.
3. **Refactor**: clean up the code while the test keeps you safe.

TDD is a technique, not a requirement, but practicing it for a while is a great way to learn to think in testable pieces.

### A note on coverage

**Code coverage** tools report what percentage of your code is executed by your tests. It is useful for spotting *untested* areas, but a high percentage does not guarantee good tests: code can be "covered" by a test that checks nothing meaningful. Treat it as a hint, not a goal in itself.

---

[Previous](./[15]-Authentication-And-Security.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[17]-Building-And-Deployment.md)
