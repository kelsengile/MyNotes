[Previous](./[13]-Remote-And-Collaborative-Development.md) | [Table of Contents](./[0]-Introduction-to-IDEs.md) | [Next](./[15]-Productivity-Tips-And-Shortcuts.md)

*Productivity And Best Practices*

# Lesson 14 - Code Quality Tools

## 14.1 Linters

A linter analyzes code against a set of rules and flags violations — things like unused variables, inconsistent naming, or patterns known to cause bugs. Unlike a compiler error, a lint warning doesn't necessarily stop the code from running; it's a style and quality check, and most IDEs display lint results inline as you type, the same way they show syntax errors.

**Example lint warnings:**

```
warning: 'userAge' is assigned a value but never used
warning: unexpected console.log statement
warning: prefer 'const' over 'let' for variables that are never reassigned
```

None of these would stop the program from running — the code is valid — but each points to something worth cleaning up before it ships or gets reviewed.

---

## 14.2 Formatters

A formatter automatically rewrites code to follow a consistent style — indentation, spacing, line length, quote style — without changing what the code actually does. Most IDEs can run a formatter automatically every time you save a file, which removes formatting disagreements from code review entirely, since everyone's code ends up looking the same regardless of personal typing habits.

**Before formatting:**
```js
function add(a,b){
return a+b
}
```

**After formatting (e.g. Prettier, default settings):**
```js
function add(a, b) {
  return a + b;
}
```

Both versions run identically — the formatter only changes whitespace, spacing, and punctuation style, never the logic.

---

## 14.3 Static Analysis

Static analysis examines code without running it to find deeper issues than a linter typically catches — potential null reference errors, security vulnerabilities, or overly complex functions that are likely to contain bugs. Some static analysis tools are built into the IDE directly, while others run as a separate step and report results back into the Problems panel.

| Tool type | Catches | Example finding |
|---|---|---|
| Linter | Style and simple correctness issues | Unused variable |
| Static analyzer | Deeper structural/security issues | Possible null dereference, SQL injection risk |
| Compiler | Whether the code is valid at all | Type mismatch, syntax error |

---

## 14.4 Refactoring Tools

Refactoring means restructuring existing code without changing its behavior — renaming a variable everywhere it's used, extracting a block of code into its own function, or moving a class to a different file. IDEs automate these operations safely, updating every reference across the project at once, which is far less error-prone than making the same changes by hand with find-and-replace.

**Example — Extract Function refactor:**

Before:
```js
const total = items.reduce((sum, i) => sum + i.price, 0) * 1.08;
```

After ("Extract Function" applied to the reduce expression):
```js
const total = calculateSubtotal(items) * 1.08;

function calculateSubtotal(items) {
  return items.reduce((sum, i) => sum + i.price, 0);
}
```

The IDE handles pulling the expression into a new function, naming its parameter, and wiring up the call site — all in one operation.

---

[Previous](./[13]-Remote-And-Collaborative-Development.md) | [Table of Contents](./[0]-Introduction-to-IDEs.md) | [Next](./[15]-Productivity-Tips-And-Shortcuts.md)
