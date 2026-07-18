# Best Practices

Writing code that *works* is only half the job. Professional software also needs to be easy to read, easy to change, and easy to hand off to someone else (including your future self). This lesson covers four foundational habits that separate maintainable codebases from messy ones.

---

## 25.1 Naming Conventions

Good names are the cheapest form of documentation you'll ever write. A well-named variable, function, or class explains *what* it does without needing a comment.

### Why it matters
- Reduces cognitive load — readers don't have to reverse-engineer intent.
- Makes bugs easier to spot (a `userList` that holds a single `User` object looks obviously wrong).
- Speeds up onboarding for new team members.

### General rules
- **Be descriptive, not clever.** `getUserById` beats `gub`.
- **Use consistent casing per language convention:**
  - `camelCase` — JavaScript, Java variables/methods
  - `PascalCase` — classes, types, components
  - `snake_case` — Python variables/functions
  - `UPPER_SNAKE_CASE` — constants
- **Booleans read like yes/no questions:** `isActive`, `hasPermission`, `canEdit`.
- **Functions are verbs; variables/classes are nouns:** `calculateTotal()`, not `total()` for a function that computes something.
- **Avoid abbreviations** unless they're universally understood (`id`, `url`, `html`).
- **Avoid misleading names.** Don't call something `tempList` if it's used permanently.

### Example

```javascript
// Bad
let d = 7;
function calc(x, y) { return x * y; }

// Good
let daysInWeek = 7;
function calculateArea(width, height) { return width * height; }
```

---

## 25.2 Code Readability & DRY Principle

### Readability
Code is read far more often than it's written. Readable code:
- Uses short, single-purpose functions (ideally doing one thing).
- Avoids deep nesting — prefer early returns over pyramids of `if` statements.
- Uses whitespace and consistent formatting (often enforced with linters/formatters like Prettier, Black, or ESLint).
- Prefers clarity over "clever" one-liners.

```python
# Hard to read
def f(x): return [i**2 for i in x if i%2==0]

# Readable
def get_squares_of_even_numbers(numbers):
    even_numbers = [n for n in numbers if n % 2 == 0]
    return [n ** 2 for n in even_numbers]
```

### DRY — Don't Repeat Yourself
If you find yourself copy-pasting logic, extract it into a function, class, or module. Duplication means every future bug fix or change has to be made in multiple places — and it's easy to forget one.

```javascript
// Repeated logic (violates DRY)
const tax1 = price1 * 0.08;
const tax2 = price2 * 0.08;

// DRY version
function calculateTax(price, rate = 0.08) {
  return price * rate;
}
const tax1 = calculateTax(price1);
const tax2 = calculateTax(price2);
```

**Caution:** DRY doesn't mean *zero* duplication at all costs. Sometimes two pieces of similar-looking code represent different concepts that will evolve independently — forcing them into one shared function can create awkward, overly generic code. Rule of thumb: abstract once the duplication is a genuine *maintenance* problem, not just visually similar text.

---

## 25.3 Documentation

Documentation bridges the gap between "the code works" and "someone else (or future you) can understand and use it."

### Levels of documentation
1. **Inline comments** — explain *why*, not *what* (the code already says what).
   ```python
   # Retry 3 times because the payment API is flaky under load
   for attempt in range(3):
       ...
   ```
2. **Function/method docstrings** — describe purpose, parameters, return values, and exceptions.
   ```python
   def calculate_discount(price: float, percent: float) -> float:
       """
       Calculate the discounted price.

       Args:
           price: Original price before discount.
           percent: Discount percentage (0-100).

       Returns:
           The final price after applying the discount.
       """
       return price * (1 - percent / 100)
   ```
3. **README files** — project overview, setup instructions, usage examples.
4. **API documentation** — endpoints, request/response formats (often auto-generated via tools like Swagger/OpenAPI, JSDoc, or Sphinx).
5. **Architecture/decision docs** — explain *why* a major design choice was made (e.g., ADRs — Architecture Decision Records).

### Best practices
- Keep documentation close to the code it describes so it's more likely to stay updated.
- Update docs in the same commit/PR as the code change.
- Prefer self-explanatory code over excessive comments — but don't skip comments for non-obvious logic (regex, algorithms, workarounds).
- Outdated documentation is often worse than no documentation — it actively misleads.

---

## 25.4 Technical Debt

**Technical debt** is the implied cost of future rework caused by choosing an easy/quick solution now instead of a better, more time-consuming approach.

### Common causes
- Tight deadlines forcing shortcuts.
- Lack of tests, making refactors risky.
- Outdated dependencies or frameworks.
- Copy-pasted code instead of proper abstractions.
- Insufficient documentation.
- Changing requirements that leave old design decisions obsolete.

### Types of technical debt
| Type | Description |
|---|---|
| **Deliberate** | Knowingly taking a shortcut to hit a deadline, planning to fix it later. |
| **Accidental** | Debt introduced due to lack of experience or foresight. |
| **Bit rot** | Code that was fine when written but degrades in quality as the surrounding system evolves. |

### Managing technical debt
- **Track it** — log debt in the backlog/issue tracker so it isn't forgotten.
- **Refactor incrementally** — pay down debt in small, regular increments rather than one big rewrite.
- **Balance speed vs. quality** — some debt is a reasonable trade-off; the danger is debt that's never repaid.
- **Add tests before refactoring** — reduces risk of breaking existing functionality.
- **Communicate trade-offs** — make sure the team/stakeholders know when debt is being taken on and why.

> **Analogy:** Technical debt is like financial debt — a little can help you move fast now, but if it's ignored, "interest" (bugs, slow development, frustrated developers) accumulates until the codebase becomes difficult to work with.

---

## Key Takeaways
- **Names** should communicate intent clearly and follow consistent conventions.
- **Readable, DRY code** is easier to maintain, test, and extend.
- **Documentation** should explain the *why*, stay close to the code, and be kept up to date.
- **Technical debt** is inevitable — the goal isn't to avoid it entirely, but to manage it deliberately.