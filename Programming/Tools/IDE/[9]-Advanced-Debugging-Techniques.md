[Previous](./[8]-Introduction-To-Debugging.md) | [Table of Contents](./[0]-Introduction-to-IDEs.md) | [Next](./[10]-Extensions-And-Plugins.md)

*Debugging*

# Lesson 9 - Advanced Debugging Techniques

## 9.1 Conditional And Logpoint Breakpoints

A **conditional breakpoint** only pauses execution when a condition you specify is true — useful when a bug only shows up on, say, the 500th iteration of a loop, so you don't have to click Continue hundreds of times. A **logpoint** is similar but doesn't pause at all; it just prints a message to the console when hit, acting like a temporary print statement that doesn't require editing the source code.

**Example conditional breakpoint** on a loop:

```python
for i in range(1000):
    process(items[i])   # breakpoint with condition: i == 500
```

The debugger runs all 500 prior iterations at full speed and pauses only once, exactly when `i` reaches 500 — no manually clicking Continue 500 times.

---

## 9.2 Debug Configurations And Launch Profiles

A launch configuration tells the IDE exactly how to start your program for debugging — which file or command to run, what arguments to pass, and which environment variables to set. Projects often need several of these (for example, one to debug the app normally and another to debug it with test data), and IDEs let you save and switch between them by name.

**Example launch configurations for one project:**

| Name | Runs | Environment variable set |
|---|---|---|
| "Debug App" | `main.py` | `ENV=development` |
| "Debug with Sample Data" | `main.py --seed=sample` | `ENV=development` |
| "Debug Against Staging" | `main.py` | `ENV=staging` |

---

## 9.3 Remote Debugging

Remote debugging attaches the IDE's debugger to a program running somewhere other than your local machine — a server, a container, or a physical device like a phone. The program runs elsewhere but still reports back its state to your IDE, letting you set breakpoints and step through code as if it were running locally, which is essential for diagnosing issues that only appear in a production-like environment.

**Example scenario:** a bug only reproduces on a Linux server, not on a developer's Windows laptop. Attaching the IDE's debugger to the running process on that server (over SSH, or via a container) lets the developer set a breakpoint and inspect the exact failing state — without trying to guess the cause purely from log files.

---

## 9.4 Debugging Automated Tests

Most IDEs let you run a specific automated test directly in debug mode rather than debugging the whole application, pausing at breakpoints inside just that test and the code it exercises. This is often the fastest way to investigate a failing test, since it skips straight to the relevant code path instead of manually reproducing the conditions that trigger it.

**Example:** a test named `test_calculate_discount_applies_correctly` fails in CI. Right-clicking it and choosing "Debug Test" runs just that one test with a breakpoint inside `calculate_discount()`, pausing at the exact moment the assertion fails — rather than running the entire test suite or manually writing a throwaway script to reproduce the same inputs.

---

[Previous](./[8]-Introduction-To-Debugging.md) | [Table of Contents](./[0]-Introduction-to-IDEs.md) | [Next](./[10]-Extensions-And-Plugins.md)
