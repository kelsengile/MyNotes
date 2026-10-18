[Previous](./[14]-Packages-And-Platform-Features.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[16]-Debugging-And-Performance.md)

*Quality And Delivery*

# Lesson 15 - Testing Flutter Apps

Automated tests catch bugs before your users do and make refactoring safe. Flutter supports three levels of tests, each with different speed and scope.

| Type | Tests | Speed |
|---|---|---|
| Unit | One function or class | Very fast |
| Widget | One widget or screen, in a simulated environment | Fast |
| Integration | The whole app on a device or emulator | Slow |

A healthy project has **many unit tests, a good number of widget tests, and a few integration tests**.

---

## 15.1 Unit tests

Unit tests check plain Dart logic with no UI. The `test` package comes with `flutter_test`. Test files live in `test/` and end with `_test.dart`.

```dart
// lib/calculator.dart
class Calculator {
  int add(int a, int b) => a + b;

  double divide(int a, int b) {
    if (b == 0) throw ArgumentError('Cannot divide by zero');
    return a / b;
  }
}
```

```dart
// test/calculator_test.dart
import 'package:flutter_test/flutter_test.dart';
import 'package:my_app/calculator.dart';

void main() {
  group('Calculator', () {
    late Calculator calc;

    setUp(() {
      calc = Calculator();      // runs before every test
    });

    test('adds two numbers', () {
      expect(calc.add(2, 3), 5);
    });

    test('divides numbers', () {
      expect(calc.divide(10, 4), 2.5);
    });

    test('throws on divide by zero', () {
      expect(() => calc.divide(1, 0), throwsArgumentError);
    });
  });
}
```

Run tests:

```bash
flutter test                         # everything
flutter test test/calculator_test.dart
flutter test --coverage              # writes coverage/lcov.info
```

**Concepts:**

- `test()` defines one case, `group()` bundles related cases.
- `expect(actual, matcher)` checks results. Matchers include `equals`, `isTrue`, `isNull`, `contains`, `isA<Type>()`, `throwsA(...)`, `closeTo(value, delta)`.
- `setUp` / `tearDown` run around each test.
- Async tests: mark the body `async` and `await` the result.

Good unit tests are why you keep logic in plain classes, not buried in widgets.

---

## 15.2 Widget tests

Widget tests build a widget in a fake environment, then simulate taps and check what is on screen.

```dart
// lib/counter.dart
class Counter extends StatefulWidget {
  const Counter({super.key});
  @override
  State<Counter> createState() => _CounterState();
}

class _CounterState extends State<Counter> {
  int _count = 0;
  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Text('Count: $_count'),
        ElevatedButton(
          onPressed: () => setState(() => _count++),
          child: const Text('Add'),
        ),
      ],
    );
  }
}
```

```dart
// test/counter_test.dart
import 'package:flutter/material.dart';
import 'package:flutter_test/flutter_test.dart';
import 'package:my_app/counter.dart';

void main() {
  testWidgets('counter increments when button is tapped', (tester) async {
    await tester.pumpWidget(
      const MaterialApp(home: Scaffold(body: Counter())),
    );

    expect(find.text('Count: 0'), findsOneWidget);

    await tester.tap(find.text('Add'));
    await tester.pump();                      // rebuild after setState

    expect(find.text('Count: 1'), findsOneWidget);
    expect(find.text('Count: 0'), findsNothing);
  });
}
```

**Key tools:**

| Tool | Purpose |
|---|---|
| `tester.pumpWidget(w)` | Build and render a widget |
| `tester.pump()` | Rebuild one frame |
| `tester.pumpAndSettle()` | Pump until all animations finish |
| `tester.tap(finder)` | Simulate a tap |
| `tester.enterText(finder, 'abc')` | Type into a field |
| `tester.drag(finder, offset)` / `fling` | Scroll or swipe |

**Finders:** `find.text('x')`, `find.byType(Icon)`, `find.byKey(const Key('save'))`, `find.byIcon(Icons.add)`, `find.widgetWithText(...)`. **Matchers:** `findsOneWidget`, `findsNothing`, `findsNWidgets(3)`, `findsWidgets`.

**Tips:** wrap the widget in `MaterialApp` (and any `Provider`/`ProviderScope` it needs) because many widgets depend on those ancestors. Add `Key`s to important widgets so tests can find them reliably. **Golden tests** compare a screenshot to a saved image with `expectLater(find.byType(MyWidget), matchesGoldenFile('my_widget.png'))`.

---

## 15.3 Integration tests

Integration tests run the **real app** on a device, emulator, or browser. They verify full flows such as logging in and then seeing a dashboard.

Add to `pubspec.yaml`:

```yaml
dev_dependencies:
  integration_test:
    sdk: flutter
  flutter_test:
    sdk: flutter
```

Create `integration_test/app_test.dart`:

```dart
import 'package:flutter/material.dart';
import 'package:flutter_test/flutter_test.dart';
import 'package:integration_test/integration_test.dart';
import 'package:my_app/main.dart' as app;

void main() {
  IntegrationTestWidgetsFlutterBinding.ensureInitialized();

  testWidgets('user can log in and see the home screen', (tester) async {
    app.main();
    await tester.pumpAndSettle();

    await tester.enterText(find.byKey(const Key('email')), 'ada@example.com');
    await tester.enterText(find.byKey(const Key('password')), 'secret123');
    await tester.tap(find.byKey(const Key('login_button')));
    await tester.pumpAndSettle();

    expect(find.text('Welcome'), findsOneWidget);
  });
}
```

Run on a connected device or emulator:

```bash
flutter test integration_test
# or choose a device
flutter test integration_test -d <device-id>
```

**Notes:**

- They use the same `tester` API as widget tests, but the app is genuinely running with real rendering and plugins.
- They are **slower and more fragile**, so keep them for critical journeys such as sign-up, purchase, and core features.
- Run them in CI on emulators or a device farm (such as Firebase Test Lab).
- Use a **test backend or fake data** so results are predictable.

---

## 15.4 Mocking dependencies

A test should not hit the real internet or database. **Mocking** replaces a dependency with a controllable fake. This is easy if your code receives dependencies through its constructor.

**Design for testability:**

```dart
abstract class PostRepository {
  Future<List<String>> fetchTitles();
}

class PostService {
  PostService(this.repository);
  final PostRepository repository;

  Future<String> firstTitle() async {
    final titles = await repository.fetchTitles();
    return titles.isEmpty ? 'No posts' : titles.first;
  }
}
```

**1. Write a manual fake**, which is the simplest approach:

```dart
class FakePostRepository implements PostRepository {
  FakePostRepository(this.titles);
  final List<String> titles;

  @override
  Future<List<String>> fetchTitles() async => titles;
}

void main() {
  test('returns first title', () async {
    final service = PostService(FakePostRepository(['Hello', 'World']));
    expect(await service.firstTitle(), 'Hello');
  });

  test('handles empty list', () async {
    final service = PostService(FakePostRepository([]));
    expect(await service.firstTitle(), 'No posts');
  });
}
```

**2. Use a mocking package** such as `mocktail` (`flutter pub add --dev mocktail`), which needs no code generation:

```dart
import 'package:mocktail/mocktail.dart';

class MockPostRepository extends Mock implements PostRepository {}

void main() {
  test('uses repository result', () async {
    final repo = MockPostRepository();
    when(() => repo.fetchTitles()).thenAnswer((_) async => ['A']);

    final service = PostService(repo);

    expect(await service.firstTitle(), 'A');
    verify(() => repo.fetchTitles()).called(1);
  });

  test('propagates errors', () {
    final repo = MockPostRepository();
    when(() => repo.fetchTitles()).thenThrow(Exception('offline'));

    expect(() => PostService(repo).firstTitle(), throwsException);
  });
}
```

(`mockito` is another popular option and uses code generation.)

**In widget tests,** inject fakes through your state management: for Riverpod use `ProviderScope(overrides: [...])`, for Provider supply a fake in the provider, and for Bloc use a mock Bloc with `bloc_test`.

**Rules of thumb:**

- Mock **boundaries** (network, database, platform), not your own pure logic.
- Test **behavior**, such as what the user sees and what results come out, not internal details.
- Keep tests small, independent, and named after what they prove.

---

[Previous](./[14]-Packages-And-Platform-Features.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[16]-Debugging-And-Performance.md)