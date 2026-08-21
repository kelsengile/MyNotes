[Previous](./[34]-Dependency-Management.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[36]-Debugging-and-Profiling.md)

*Architecture & Best Practices*

# Lesson 35 - Testing Mobile Apps (Unit, Widget, UI Tests)

## 35.1 The Testing Pyramid

Mobile testing is typically split into three layers, from fastest/cheapest to slowest/most expensive:

1. **Unit tests** — test a single function, class, or ViewModel in isolation.
2. **Widget/Component tests** — test a single UI component's rendering and behavior without a full running app.
3. **UI/Integration (end-to-end) tests** — test entire user flows on a real device or simulator, exactly as a user would experience them.

A healthy test suite has many unit tests, a moderate number of widget tests, and relatively few full UI tests, since UI tests are slow and more brittle to write and maintain.

---

## 35.2 Unit Testing

Unit tests verify business logic without touching the UI at all — e.g. testing that a ViewModel correctly formats a price, or that a validation function correctly rejects an invalid email.

```swift
// XCTest (iOS)
func testEmailValidation() {
    XCTAssertTrue(isValidEmail("user@example.com"))
    XCTAssertFalse(isValidEmail("not-an-email"))
}
```

```kotlin
// JUnit (Android)
@Test
fun emailValidation() {
    assertTrue(isValidEmail("user@example.com"))
    assertFalse(isValidEmail("not-an-email"))
}
```

---

## 35.3 Widget / Component Testing

Widget tests (Flutter's term) or view/component tests render a single UI piece in a lightweight test environment and assert on its output, without launching the full app or a real device.

```dart
testWidgets('Counter increments', (tester) async {
  await tester.pumpWidget(Counter());
  await tester.tap(find.byType(ElevatedButton));
  await tester.pump();
  expect(find.text('Count: 1'), findsOneWidget);
});
```

This layer catches UI bugs (wrong text, missing elements, broken interactions) much faster than a full end-to-end test would.

---

## 35.4 UI / Integration Testing

These tests drive the actual app — tapping buttons, entering text, navigating screens — on a simulator, emulator, or real device, verifying complete user flows like "sign up, then land on the home screen."

- **iOS**: `XCUITest`
- **Android**: `Espresso` or `UI Automator`
- **Flutter**: `integration_test` package
- **React Native**: `Detox`

Because these tests spin up the full app, they run much slower and are more prone to flakiness (timing issues, animations), so teams reserve them for critical user journeys rather than exhaustive coverage.

---

## 35.5 Test-Driven Habits

Writing tests as you build — rather than only after something breaks — catches regressions early and documents expected behavior. Most teams also run their test suite automatically on every pull request via **continuous integration (CI)**, so broken code is caught before it merges.

[Previous](./[34]-Dependency-Management.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[36]-Debugging-and-Profiling.md)
