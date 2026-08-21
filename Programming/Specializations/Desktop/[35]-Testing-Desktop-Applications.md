[Previous](./[34]-Version-Control-with-Git.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[36]-Debugging-and-Profiling.md)

*Architecture & Best Practices*

# Lesson 35 - Testing Desktop Applications

## 35.1 Unit Tests

Unit tests verify a single piece of logic in isolation, without touching the UI, filesystem, or database — the payoff of separating logic from UI in Lesson 32 is that ViewModels/Models become straightforward to unit test:

```csharp
[Test]
public void CalculateTotal_SumsAllPrices()
{
    var result = CalculateTotal(new List<int> { 10, 20, 30 });
    Assert.AreEqual(60, result);
}
```

---

## 35.2 Integration Tests

Integration tests verify that multiple pieces work together correctly — a ViewModel actually saving to a real (test) database, or a settings object round-tripping through serialization and back. These are slower and more brittle than unit tests but catch problems unit tests can't, like a wrong SQL query or a broken file path.

---

## 35.3 UI/End-to-End Tests

UI tests automate real interaction with the running app — clicking buttons, typing into fields, asserting what appears on screen — using tools like WinAppDriver (Windows), XCUITest (macOS), or Playwright/Spectron-style tooling for Electron. These are the slowest and most fragile tests (small UI changes can break them) so they're typically reserved for critical user flows rather than exhaustive coverage.

---

## 35.4 The Testing Pyramid

A healthy desktop test suite has many fast unit tests, a moderate number of integration tests, and a small number of UI/end-to-end tests — inverting this (mostly UI tests) leads to a slow, flaky suite that developers stop trusting. Run unit and integration tests on every commit (via CI); reserve UI tests for pre-release verification if they're too slow for every commit.

[Previous](./[34]-Version-Control-with-Git.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[36]-Debugging-and-Profiling.md)
