# Testing & Quality

## 18.1 Unit Testing

A **unit test** verifies the smallest testable piece of a program — typically a single function, method, or class — in isolation from the rest of the system.

### Why Unit Test

- Catches bugs early, close to where they're introduced.
- Acts as a safety net when refactoring — tests fail immediately if behavior changes unexpectedly.
- Serves as executable documentation of how a piece of code is expected to behave.
- Enables confident, incremental changes to a codebase over time.

### Anatomy of a Unit Test — Arrange, Act, Assert (AAA)

```python
# Using Python's built-in unittest
import unittest

def add(a, b):
    return a + b

class TestAdd(unittest.TestCase):
    def test_adds_two_positive_numbers(self):
        # Arrange
        a, b = 2, 3
        # Act
        result = add(a, b)
        # Assert
        self.assertEqual(result, 5)

if __name__ == "__main__":
    unittest.main()
```

```javascript
// Using Jest (JavaScript)
function add(a, b) {
  return a + b;
}

test("adds two positive numbers", () => {
  expect(add(2, 3)).toBe(5);   // Arrange (implicit) / Act / Assert
});
```

- **Arrange** — set up the inputs and any required state.
- **Act** — call the function/method under test.
- **Assert** — check that the actual output matches the expected output.

### Characteristics of a Good Unit Test

- **Fast** — runs in milliseconds, so it can be run constantly during development.
- **Isolated** — doesn't depend on external systems (databases, networks, the filesystem) or on other tests' state/order.
- **Repeatable** — produces the same result every time, regardless of environment.
- **Self-checking** — automatically passes or fails; no manual inspection required.
- **Focused** — tests one specific behavior per test, with a clear, descriptive name (e.g., `test_returns_error_for_negative_input`).

### Test Doubles: Mocks, Stubs, and Fakes

To keep unit tests isolated, real dependencies (databases, APIs, file systems) are often replaced with lightweight substitutes:

| Type | Purpose |
|---|---|
| **Stub** | Returns predefined/canned responses, with no real logic |
| **Mock** | Like a stub, but also records how it was called, so the test can assert *interactions* (e.g., "was `send_email()` called exactly once?") |
| **Fake** | A simplified working implementation (e.g., an in-memory database) — behaves correctly but isn't production-suitable |
| **Spy** | Wraps a real object and records calls to it, while still delegating to the real implementation |

```python
from unittest.mock import Mock

def send_welcome_email(mailer, user_email):
    mailer.send(user_email, "Welcome!")

def test_send_welcome_email_calls_mailer():
    mock_mailer = Mock()
    send_welcome_email(mock_mailer, "alice@example.com")
    mock_mailer.send.assert_called_once_with("alice@example.com", "Welcome!")
```

### Assertions

Common assertion types across frameworks: equality (`assertEqual`/`toBe`), truthiness, exception raising (`assertRaises`/`toThrow`), collection membership, and approximate equality for floating-point numbers.

```python
self.assertEqual(result, expected)
self.assertTrue(is_valid)
self.assertRaises(ValueError, parse_input, "not a number")
self.assertAlmostEqual(0.1 + 0.2, 0.3, places=7)  # avoids float precision issues
```

### Code Coverage

**Code coverage** measures the percentage of code executed by the test suite (line, branch, or function coverage). It's a useful signal for finding untested code, but:

- **High coverage doesn't guarantee correctness** — a test can execute a line without meaningfully asserting on its behavior.
- Coverage is a *floor*, not a *target* — chasing 100% coverage can encourage low-value tests. Focus on testing meaningful behavior and edge cases, not just executing every line.

### Parameterized Tests

Run the same test logic against multiple input/output pairs to avoid repetitive test code:

```python
import pytest

@pytest.mark.parametrize("a,b,expected", [
    (2, 3, 5),
    (-1, 1, 0),
    (0, 0, 0),
])
def test_add(a, b, expected):
    assert add(a, b) == expected
```

### Testing Edge Cases

A thorough unit test suite covers not just the "happy path" but also:
- Boundary values (empty input, zero, negative numbers, maximum values).
- Invalid/unexpected input (wrong types, `null`/`None`, malformed data).
- Error conditions (does the function raise the right exception under the right circumstances?).

## 18.2 Integration & End-to-End Testing

Unit tests verify components in isolation; **integration** and **end-to-end (E2E)** tests verify that components work correctly *together*.

### The Testing Pyramid

A common model for balancing test types by speed, cost, and confidence:

```
        ▲
       /E2E\          Few — slow, expensive, high confidence in real user flows
      /------\
     /Integr. \       Some — moderate speed, verifies components work together
    /----------\
   /   Unit     \     Many — fast, cheap, isolated, run constantly
  /--------------\
```

The general guidance: have **many** fast unit tests, a **moderate** number of integration tests, and **few** slow, brittle E2E tests — using each layer for what it's best at rather than over-relying on the most expensive one.

### Integration Testing

Verifies that multiple units or components work correctly when combined — e.g., that application code correctly reads/writes to a real (or realistic) database, or that two internal services communicate correctly.

```python
# Integration test: verifies the app layer + real database work together
def test_create_user_persists_to_database():
    db = connect_to_test_database()
    user_service = UserService(db)

    user_service.create_user("alice@example.com")

    saved_user = db.query("SELECT * FROM users WHERE email = ?", "alice@example.com")
    assert saved_user is not None
```

**What integration tests typically cover:**
- Database reads/writes and query correctness.
- Communication between internal services or modules.
- Third-party API integrations (often against a sandbox/test environment).
- Correct wiring of configuration and dependency injection.

**Best practices:**
- Use a **dedicated test database** (or an in-memory/containerized one, e.g., via Docker) — never run integration tests against production data.
- **Reset state between tests** (transactions rolled back, or a fresh database per test run) so tests don't interfere with each other.
- Integration tests are slower than unit tests, so run them less frequently (e.g., on every commit/PR) rather than on every keystroke.

### End-to-End (E2E) Testing

Simulates real user behavior through the entire application stack — frontend, backend, database, and any external integrations — to verify complete workflows function correctly.

```javascript
// Example using Playwright (browser automation)
test("user can sign up and see the welcome page", async ({ page }) => {
  await page.goto("https://example.com/signup");
  await page.fill("#email", "alice@example.com");
  await page.fill("#password", "SecurePass123");
  await page.click("#submit");
  await expect(page.locator("h1")).toHaveText("Welcome, Alice!");
});
```

**Common E2E tools:** Selenium, Playwright, Cypress (web); Appium (mobile).

**Characteristics:**
- Highest confidence that real user flows work, since it tests the system as a whole, closest to production conditions.
- **Slowest and most expensive** to run and maintain — full environment setup, real (or realistic) browser/network interactions.
- **Most brittle** — small UI changes (a renamed CSS class, a shifted button) can break tests unrelated to actual functionality; tests should target stable selectors (e.g., `data-testid` attributes) rather than fragile ones like CSS classes or exact text.
- Best reserved for critical user journeys (login, checkout, core workflows) rather than exhaustive coverage of every possible path.

### Other Test Types Worth Knowing

| Type | Purpose |
|---|---|
| **Smoke tests** | A minimal set of tests verifying the system's most critical functions work — often run right after deployment |
| **Regression tests** | Re-run existing tests to confirm a change hasn't broken previously working functionality |
| **Performance/load testing** | Measures how the system behaves under expected or peak traffic (e.g., using tools like JMeter, k6, Locust) |
| **Contract testing** | Verifies that a service's API still matches what its consumers expect, without needing a full integration environment |
| **Snapshot testing** | Captures a component's rendered output and flags any unexpected changes on future runs |

### Choosing the Right Balance

- Write **unit tests** for business logic, algorithms, and edge cases — they're cheap and give fast, precise feedback.
- Write **integration tests** where components meet real infrastructure (database queries, external APIs) — these are the areas most likely to break in ways unit tests can't catch.
- Write **E2E tests** sparingly, for the handful of critical flows that must never break (e.g., "a user can complete checkout").

## 18.3 Test-Driven Development (TDD)

TDD is a software development approach where tests are written **before** the implementation code, driving the design of the code itself.

### The Red-Green-Refactor Cycle

1. **Red** — write a test for a small piece of desired functionality. Run it; it should fail (since the functionality doesn't exist yet).
2. **Green** — write the *minimum* amount of code necessary to make the test pass. Don't over-engineer at this stage.
3. **Refactor** — clean up the code (remove duplication, improve naming/structure) while keeping all tests passing.
4. Repeat, incrementally building up functionality one small test at a time.

```python
# Step 1 — RED: write a failing test first
def test_calculate_total_with_no_discount():
    assert calculate_total(100, discount=0) == 100
# calculate_total() doesn't exist yet — this fails to even run

# Step 2 — GREEN: write the minimal code to pass
def calculate_total(price, discount):
    return price - discount

# Step 3 — add another test, watch it fail, then extend the implementation
def test_calculate_total_with_percentage_discount():
    assert calculate_total(100, discount=0.1, is_percentage=True) == 90

def calculate_total(price, discount, is_percentage=False):
    if is_percentage:
        return price - (price * discount)
    return price - discount

# Step 4 — REFACTOR: clean up once tests pass, tests stay green throughout
```

### Why Practice TDD

- **Forces clarity of intent** — writing the test first requires precisely defining what "correct" means before writing any implementation.
- **Naturally produces high test coverage**, since no code is written without an accompanying test.
- **Encourages simpler, more modular design** — code that's hard to test (tightly coupled, side-effect-heavy) becomes obvious immediately, pushing toward cleaner interfaces.
- **Provides a tight feedback loop and safety net** for refactoring, since regressions are caught the moment they're introduced.
- **Prevents over-engineering** — writing only enough code to pass the current test discourages speculative, unused functionality.

### Common Criticisms and Trade-offs

- Can feel slower up front, especially for developers unfamiliar with the discipline.
- Not always well-suited to exploratory work (e.g., prototyping, UI/UX experimentation) where requirements are still being discovered.
- Poorly written tests (overly coupled to implementation details) can make refactoring *harder*, not easier — tests should verify behavior/outputs, not internal implementation specifics.
- Requires discipline to maintain consistently, especially under deadline pressure.

### TDD vs. Traditional "Test-After" Development

| | TDD (test-first) | Test-After |
|---|---|---|
| Test written | Before the implementation | After the implementation |
| Design influence | Tests actively shape the code's design | Code design is fixed before tests are written |
| Coverage | Naturally comprehensive (by construction) | Depends on developer discipline afterward |
| Feedback loop | Immediate — a failing test is expected and instructive | Delayed — bugs may already be embedded in the design |

### Related Practices

- **BDD (Behavior-Driven Development)** — extends TDD by writing tests in a more human-readable, business-facing format (e.g., Given/When/Then via tools like Cucumber), bridging communication between developers and non-technical stakeholders.
- **Continuous Integration (CI)** — running the full automated test suite automatically on every code change (e.g., via GitHub Actions, Jenkins, CircleCI), catching regressions before they reach production.

## 18.4 Code Reviews

A code review is the process of having one or more other developers examine proposed code changes before they're merged into the shared codebase.

### Why Code Review Matters

- **Catches bugs and design issues** that the original author might miss due to familiarity with their own code.
- **Spreads knowledge** across the team — reviewers learn what changed and why, reducing single points of knowledge failure ("bus factor").
- **Maintains consistency** in style, patterns, and architectural decisions across a codebase.
- **Mentorship and skill growth** — a natural venue for less experienced developers to learn from more experienced ones, and vice versa.
- **Shared ownership** — the codebase becomes a team responsibility rather than any one person's private domain.

### What to Look For in a Review

- **Correctness** — does the code actually do what it's supposed to do? Are edge cases handled?
- **Readability** — can another developer understand this code without extensive explanation? Are names clear and intention-revealing?
- **Design** — does the change fit sensibly into the existing architecture? Is it appropriately modular (see Section 17.2)?
- **Test coverage** — are there tests for the new/changed behavior, including edge cases?
- **Performance implications** — could this introduce a bottleneck (e.g., an N+1 query, an unnecessary loop over a large dataset)?
- **Security** — does it introduce injection risks, expose sensitive data, or mishandle authentication/authorization?
- **Consistency** — does it follow the project's established conventions and style guide?

### The Review Process (Typical Flow)

1. Author opens a **pull/merge request** with a clear description of *what* changed and *why*.
2. Automated checks run (linting, tests, CI build) — many issues are caught before a human ever looks at the code.
3. Reviewer(s) read the diff, leave comments/questions, and either approve, request changes, or discuss.
4. Author addresses feedback (updates code, responds to comments, or explains reasoning).
5. Once approved, the change is merged.

### Giving Good Feedback

- **Be specific and actionable** — "This loop is O(n²); consider using a hash map here" is more useful than "this seems slow."
- **Explain the *why*, not just the *what*** — helps the author learn, not just comply.
- **Distinguish severity** — separate must-fix issues (bugs, security problems) from optional suggestions (style preferences, nice-to-haves) using clear labels (e.g., "nit:", "blocking:", "question:").
- **Ask questions instead of issuing commands** where appropriate — "What happens if `items` is empty here?" invites discussion rather than dictating a fix.
- **Praise good solutions**, not just flag problems — reinforces good practices and keeps the tone constructive.
- **Focus on the code, not the person** — critique the work, not the author, and keep feedback objective and professional.

### Receiving Feedback Well

- Treat review comments as being about the code, not a personal judgment.
- Ask for clarification if feedback is unclear rather than guessing at intent.
- It's fine to push back with reasoning if you disagree — code review is a discussion, not a one-way directive — but stay open to being wrong.

### Best Practices for an Effective Review Culture

- **Keep changes small** — large pull requests are harder to review thoroughly and more likely to hide bugs; prefer many small, focused changes over rare, massive ones.
- **Review promptly** — long delays block the author and encourage large, batched changes; fast turnaround keeps the whole team moving.
- **Automate what can be automated** — let linters and formatters handle style so human reviewers can focus on logic, design, and correctness instead of nitpicking whitespace.
- **Establish clear conventions** (style guides, architectural decisions) upfront, so reviews focus on substance rather than re-litigating preferences on every PR.
- **Everyone gets reviewed** — including senior developers — to reinforce that review is a normal part of the process, not a sign of distrust.