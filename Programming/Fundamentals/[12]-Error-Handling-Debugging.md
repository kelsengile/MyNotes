[Previous](./[11]-Functional-Programming-Concepts.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[13]-Text-Processing.md)

# Lesson 12 - Error Handling & Debugging

## 12.1 Common Bug Types

Understanding the different categories of bugs helps you know where to look and what tools to reach for.

- **Syntax errors** — Code that violates the rules of the language (missing brackets, typos in keywords, mismatched quotes). Caught at compile time or parse time, before the program runs.
- **Runtime errors** — Errors that occur while the program is executing, such as dividing by zero, accessing an out-of-bounds array index, or calling a method on `null`/`undefined`.
- **Logic errors** — The code runs without crashing but produces the wrong result. These are often the hardest to find because there's no error message to point you to the problem — the program simply behaves incorrectly.
- **Off-by-one errors** — A specific and very common logic error involving loop boundaries or index calculations (e.g., using `<=` instead of `<`).
- **Null/undefined reference errors** — Attempting to use a variable that hasn't been initialized or that points to nothing.
- **Type errors** — Using a value in a way that's incompatible with its type, such as treating a string as a number.
- **Concurrency bugs** — Race conditions, deadlocks, and other issues that arise when multiple threads or processes access shared resources without proper coordination.
- **Memory-related bugs** — Memory leaks, dangling pointers, buffer overflows, and use-after-free errors, common in lower-level languages like C and C++.
- **Integration/environment bugs** — Code works locally but fails in another environment due to differing dependencies, configuration, or system resources.
- **Edge-case bugs** — Failures triggered only by unusual or extreme inputs (empty strings, very large numbers, unexpected encodings) that weren't considered during development.

---

## 12.2 Try/Catch, Exceptions, and Error Codes

There are two broad philosophies for signaling and handling errors in code: **exceptions** and **error codes** (also called return-value error handling).

### Exceptions

An exception is an object that represents an error condition, "thrown" at the point where the error occurs and "caught" by code further up the call stack that knows how to handle it.

```javascript
// JavaScript example
try {
  const data = JSON.parse(userInput);
  processData(data);
} catch (error) {
  console.error("Failed to parse input:", error.message);
} finally {
  cleanup(); // Runs whether or not an error occurred
}
```

```python
# Python example
try:
    value = int(user_input)
    result = 100 / value
except ValueError:
    print("That wasn't a valid number.")
except ZeroDivisionError:
    print("Cannot divide by zero.")
else:
    print(f"Result: {result}")  # Runs only if no exception occurred
finally:
    print("Done processing.")
```

**Key concepts:**
- **`try`** — The block of code where an error might occur.
- **`catch` / `except`** — The block that handles the error if one is thrown.
- **`finally`** — Code that runs regardless of whether an exception occurred, typically used for cleanup (closing files, releasing locks, etc.).
- **Throwing/raising** — Explicitly signaling that an error has occurred (`throw new Error(...)` in JS, `raise ValueError(...)` in Python).
- **Custom exceptions** — Defining your own exception types/classes to represent domain-specific errors, making `catch` blocks more precise.
- **Exception propagation** — If an exception isn't caught, it moves up the call stack until it either finds a handler or crashes the program.

**Best practices:**
- Catch specific exception types rather than using broad catch-alls, so you don't accidentally swallow unrelated bugs.
- Don't use exceptions for normal control flow — they're for exceptional conditions, not routine logic.
- Always clean up resources (files, connections, locks) in a `finally` block or equivalent (e.g., `with` statements in Python, `using` in C#).
- Preserve the original error when re-throwing or wrapping, so the root cause isn't lost.

### Error Codes

Instead of throwing, a function returns a special value or status code indicating success or failure. This is common in C, Go, and many systems-level APIs.

```go
// Go example
result, err := doSomething()
if err != nil {
    log.Println("operation failed:", err)
    return err
}
```

**Trade-offs:**

| Aspect | Exceptions | Error Codes |
|---|---|---|
| Visibility | Can be "invisible" if not documented | Explicit — must be checked at each call site |
| Forces handling | No — can be ignored/uncaught | Depends on language (Go relies on convention; Rust's `Result` forces handling) |
| Performance | Slight overhead when thrown | Generally cheaper |
| Readability | Cleaner "happy path" code | Can lead to repetitive `if err != nil` checks |
| Common in | Java, Python, JavaScript, C++ | C, Go, Rust (`Result<T, E>`), syscalls |

Some modern languages use a hybrid: Rust's `Result` and `Option` types, or Swift's `Result` type, encode success/failure into the type system itself, forcing the caller to handle both cases explicitly without traditional exceptions.

---

## 12.3 Debugging Tools and Techniques

### Techniques

- **Print/console debugging** — Inserting `print()`, `console.log()`, or similar statements to inspect variable values at various points. Fast and universal, but can clutter code and requires re-running the program.
- **Rubber duck debugging** — Explaining your code line-by-line to an inanimate object (or a person). The act of articulating the logic often reveals the flaw.
- **Bisection / binary search debugging** — Narrowing down the location of a bug by testing the midpoint of the code (or git history) and repeatedly halving the search space. `git bisect` automates this for regressions.
- **Reproducing the bug** — Before fixing anything, find the smallest, most reliable set of steps that reproduces the issue consistently.
- **Reading the stack trace** — Understanding the call sequence that led to an error, starting from the innermost (most recent) call.
- **Hypothesis-driven debugging** — Form a specific hypothesis about the cause, design a test that would prove or disprove it, then run it — rather than randomly changing code.

### Tools

- **Debuggers (interactive)** — Tools like GDB (C/C++), pdb/debugpy (Python), the Chrome DevTools debugger, and IDE-integrated debuggers (VS Code, IntelliJ, PyCharm) let you:
  - Set **breakpoints** to pause execution at a specific line.
  - **Step through** code line-by-line (step over, step into, step out).
  - Inspect and modify variable values at runtime.
  - View the **call stack** at any paused point.
  - Set **conditional breakpoints** that only trigger under certain conditions (e.g., `i == 42`).
  - **Watch expressions** that show a value's changes over time.
- **Browser DevTools** — Inspect the DOM, network requests, console output, and JavaScript execution directly in the browser.
- **Linters and static analyzers** (ESLint, Pylint, SonarQube) — Catch potential bugs, style issues, and code smells before the program even runs.
- **Profilers** — Identify performance bottlenecks, memory leaks, and CPU/memory usage hotspots (e.g., Chrome Performance tab, `cProfile` in Python, VisualVM for Java).
- **Memory debugging tools** — Valgrind, AddressSanitizer for detecting memory leaks and invalid memory access in C/C++.
- **Version control bisection** — `git bisect` to find the exact commit that introduced a regression.
- **Unit and integration tests** — Writing tests that isolate the failing behavior helps confirm a fix and prevents regressions.
- **Network inspectors** — Tools like Postman, curl, or browser Network tabs to debug API/HTTP-related issues.
- **Remote debugging** — Attaching a debugger to a process running on a different machine or container (common in production/staging environments).

---

## 12.4 Logging

Logging is the practice of recording information about a program's execution to files, consoles, or centralized systems, so behavior can be understood after the fact — especially useful in production, where interactive debugging isn't practical.

### Log Levels

Most logging frameworks support a hierarchy of severity levels:

| Level | Purpose |
|---|---|
| `TRACE` | Extremely fine-grained, step-by-step execution detail |
| `DEBUG` | Diagnostic information useful during development |
| `INFO` | General operational messages (e.g., "server started") |
| `WARN` | Something unexpected happened, but the program can continue |
| `ERROR` | A failure occurred that needs attention |
| `FATAL/CRITICAL` | A severe error causing the program to abort |

### Best Practices

- **Log at the right level** — Don't flood production logs with `DEBUG`-level noise; reserve `ERROR` for genuine failures.
- **Structured logging** — Emit logs as structured data (e.g., JSON) rather than free-form text, so they can be easily queried and filtered: `{"level": "error", "user_id": 123, "message": "payment failed"}`.
- **Include context** — Timestamps, request IDs, user IDs, and stack traces make logs far more useful for tracing an issue.
- **Avoid logging sensitive data** — Never log passwords, API keys, credit card numbers, or other personally identifiable information (PII).
- **Correlation/trace IDs** — In distributed systems, attach a unique ID to a request so its path across multiple services can be reconstructed.
- **Log rotation and retention** — Prevent log files from growing indefinitely; archive or delete old logs based on a retention policy.
- **Centralized logging** — Aggregate logs from multiple services/servers into a single searchable system (e.g., ELK Stack — Elasticsearch/Logstash/Kibana, Splunk, Datadog, Grafana Loki) rather than SSH-ing into individual machines.
- **Don't over-log** — Excessive logging adds noise, hurts performance, and increases storage costs — log what's actionable, not everything.

### Logging vs. Debugging

| | Debugging | Logging |
|---|---|---|
| When used | Development, actively investigating a bug | Continuously, in dev and production |
| Interactivity | Interactive (breakpoints, stepping) | Passive (records events as they happen) |
| Output | Ephemeral (in-session) | Persistent (files, log systems) |
| Best for | Diagnosing a known, reproducible issue | Understanding system behavior over time, catching unknown issues |

Together, thoughtful error handling, the right debugging tools, and disciplined logging form the backbone of building software that fails gracefully and is easy to diagnose when things go wrong.

[Previous](./[11]-Functional-Programming-Concepts.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[13]-Text-Processing.md)
