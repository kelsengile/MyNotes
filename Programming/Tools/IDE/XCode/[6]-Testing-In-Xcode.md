[Previous](./[5]-Building,-Running,-And-Debugging.md) | [Table of Contents](./[0]-Introduction-to-XCode.md) | [Next](./[7]-Archiving-And-Distributing-An-App.md)

*Building And Debugging Apps*

# Lesson 6 - Testing In Xcode

## 6.1 XCTest And Unit Tests

**XCTest** is Apple's built-in testing framework, automatically included when a project is created with its "Include Tests" option checked (Lesson 1.3). Unit tests live in a separate test target (e.g. `MyAppTests`) so test code never ships inside the actual app.

```swift
import XCTest
@testable import MyApp

final class CalculatorTests: XCTestCase {
    func testAddition() {
        let result = Calculator.add(2, 3)
        XCTAssertEqual(result, 5)
    }

    func testDivisionByZero() {
        XCTAssertThrowsError(try Calculator.divide(10, by: 0))
    }
}
```

- **`@testable import`** — grants the test target access to `internal` (default-visibility) symbols in the app target, not just fully `public` ones.
- **`XCTAssert...` family** — `XCTAssertEqual`, `XCTAssertTrue`, `XCTAssertNil`, `XCTAssertThrowsError`, and others express what the test expects.
- **Running tests** — `Cmd+U` runs the whole test suite; clicking the diamond icon in the gutter next to a specific test method runs just that one.
- **Test Navigator** (Navigator pane, diamond icon) — lists every test method across the project, with pass/fail status icons after a run, and lets you re-run any subset directly from the list.

---

## 6.2 UI Testing And The Test Recorder

**UI Tests** (in a target like `MyAppUITests`) simulate a real user tapping through the actual running app, rather than testing isolated logic like unit tests do.

```swift
func testLoginFlow() {
    let app = XCUIApplication()
    app.launch()

    app.textFields["Username"].tap()
    app.textFields["Username"].typeText("test_user")
    app.secureTextFields["Password"].tap()
    app.secureTextFields["Password"].typeText("password123")
    app.buttons["Login"].tap()

    XCTAssertTrue(app.staticTexts["Welcome"].exists)
}
```

The **Test Recorder** lowers the barrier to writing these: click the red circle record button at the bottom of a UI test method, then interact with the app running in the Simulator — Xcode generates the corresponding `XCUIApplication` code automatically for every tap, swipe, and text entry, which you can then edit or add assertions to.

**Accessibility identifiers** — UI tests query elements by label or accessibility identifier (`app.buttons["Login"]`), which is also why adding accessibility identifiers to UI elements benefits both real accessibility (VoiceOver) and UI test reliability at the same time.

---

## 6.3 Instruments For Performance Profiling

**Instruments** is a separate profiling application bundled with Xcode (launched via **Product → Profile**, `Cmd+I`), providing much deeper performance analysis than the quick CPU/Memory graphs seen in the Debug Navigator (Lesson 5.3).

```
Instruments
 ├── Time Profiler     — where CPU time is actually spent, call-by-call
 ├── Allocations        — memory allocation tracking over time
 ├── Leaks               — detects memory that's allocated but never freed
 ├── Core Animation      — frame-rate and rendering performance
 └── Network             — every network request the app makes, with timing
```

A typical workflow: launch the "Leaks" instrument, use the app normally (or run it through a UI test), and watch the timeline for spikes indicating memory that isn't being released — then use the detail pane to see exactly which line of code allocated the leaking object.

Because Instruments always profiles a **Release**-like build configuration by default, performance numbers gathered here are far more representative of real-world behavior than timing observed while stepping through a Debug build in the regular debugger.

---

## 6.4 Code Coverage Reports

Code coverage measures what percentage of your app's actual code was executed while the test suite ran, highlighting untested logic paths.

Enable it via **Edit Scheme → Test → Options → Code Coverage**, then run tests (`Cmd+U`). Results appear in the **Report Navigator** (the icon showing a speech bubble/document):

```
Report Navigator → Coverage
 MyApp                     78%
 ├── Calculator.swift       95%
 ├── UserService.swift      62%   ← under-tested
 └── NetworkManager.swift   40%   ← under-tested
```

- **Gutter highlighting** — opening a source file after a coverage-enabled test run shows a colored bar in the gutter next to each line: covered lines are green-tinted, uncovered lines are red-tinted, directly in the editor.
- **Per-line hit counts** — hovering over the gutter bar for a line shows exactly how many times that line executed during the test run, useful for spotting a loop that ran far more (or fewer) times than expected.

Coverage percentage alone isn't a complete measure of test quality — 100% coverage can still miss important edge cases — but it's an effective way to quickly spot code that has no tests exercising it at all.

[Previous](./[5]-Building,-Running,-And-Debugging.md) | [Table of Contents](./[0]-Introduction-to-XCode.md) | [Next](./[7]-Archiving-And-Distributing-An-App.md)
