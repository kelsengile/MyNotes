[Previous](./[10]-Navigation-And-Routing.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[12]-Networking-And-APIs.md)

*Navigation And State*

# Lesson 11 - State Management

**State** is any data that can change and affects what the user sees. This lesson starts with `setState`, explains why bigger apps need more, and surveys the most common solutions: Provider, Riverpod, and Bloc.

---

## 11.1 setState and its limits

**Ephemeral state** belongs to a single widget, such as the selected tab or a checkbox. `setState` is the right tool:

```dart
class _LikeButtonState extends State<LikeButton> {
  bool _liked = false;

  @override
  Widget build(BuildContext context) {
    return IconButton(
      icon: Icon(_liked ? Icons.favorite : Icons.favorite_border),
      onPressed: () => setState(() => _liked = !_liked),
    );
  }
}
```

**App state** is shared across many screens: the logged-in user, a shopping cart, settings. `setState` struggles here:

- **Prop drilling:** passing data and callbacks through many constructor layers.
- **Wasted rebuilds:** calling `setState` high in the tree rebuilds everything below it.
- **Mixed concerns:** business logic ends up inside UI code, which makes it hard to test.

State management solutions let widgets **read** shared state and **react** to changes without that plumbing. A useful rule:

> If only one widget cares, use `setState`. If several distant widgets care, lift it into a state management solution.

---

## 11.2 InheritedWidget and Provider

**`InheritedWidget`** is Flutter's built-in way to make data available to everything below it in the tree. `Theme.of(context)` and `MediaQuery.of(context)` are built on it. Writing your own is verbose, so most people use the **`provider`** package, which wraps it.

Add it: `flutter pub add provider`

**1. Create a model** that notifies listeners:

```dart
import 'package:flutter/foundation.dart';

class CartModel extends ChangeNotifier {
  final List<String> _items = [];

  List<String> get items => List.unmodifiable(_items);
  int get count => _items.length;

  void add(String item) {
    _items.add(item);
    notifyListeners();   // tells listening widgets to rebuild
  }

  void clear() {
    _items.clear();
    notifyListeners();
  }
}
```

**2. Provide it** above the widgets that need it:

```dart
void main() {
  runApp(
    ChangeNotifierProvider(
      create: (_) => CartModel(),
      child: const MyApp(),
    ),
  );
}
```

**3. Use it** in widgets:

```dart
// Rebuilds when the model changes
final cart = context.watch<CartModel>();
Text('Items: ${cart.count}');

// Reads without listening, for event handlers
ElevatedButton(
  onPressed: () => context.read<CartModel>().add('Book'),
  child: const Text('Add'),
)

// Rebuild only part of the tree
Consumer<CartModel>(
  builder: (context, cart, child) => Text('${cart.count}'),
)
```

**Important:** use `context.watch` inside `build`, and `context.read` inside callbacks such as `onPressed`. Use `MultiProvider` to provide several objects.

---

## 11.3 Riverpod

**Riverpod** (by the author of Provider) fixes several Provider limits: providers are global declarations, not tied to the widget tree, they are compile-time safe, and they handle async data well.

Add it: `flutter pub add flutter_riverpod`

**Setup:** wrap the app in a `ProviderScope`:

```dart
import 'package:flutter_riverpod/flutter_riverpod.dart';

void main() {
  runApp(const ProviderScope(child: MyApp()));
}
```

**A simple state provider using a `Notifier`:**

```dart
class CounterNotifier extends Notifier<int> {
  @override
  int build() => 0;                 // initial state

  void increment() => state = state + 1;
}

final counterProvider =
    NotifierProvider<CounterNotifier, int>(CounterNotifier.new);
```

**Using it:** extend `ConsumerWidget`, which gives you a `ref`:

```dart
class CounterPage extends ConsumerWidget {
  const CounterPage({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final count = ref.watch(counterProvider);

    return Scaffold(
      body: Center(child: Text('$count')),
      floatingActionButton: FloatingActionButton(
        onPressed: () => ref.read(counterProvider.notifier).increment(),
        child: const Icon(Icons.add),
      ),
    );
  }
}
```

**Async data** with `FutureProvider`:

```dart
final userProvider = FutureProvider<String>((ref) async {
  await Future.delayed(const Duration(seconds: 1));
  return 'Ada';
});

// In build:
final user = ref.watch(userProvider);
return user.when(
  data: (name) => Text('Hello $name'),
  loading: () => const CircularProgressIndicator(),
  error: (e, st) => Text('Error: $e'),
);
```

`ref.watch` rebuilds on change (in `build`), `ref.read` reads once (in callbacks), and `ref.listen` triggers side effects such as showing a snackbar. Providers can depend on other providers, which makes composing data straightforward.

---

## 11.4 Bloc and choosing an approach

**Bloc** (Business Logic Component) enforces a strict flow: the UI sends **events**, the Bloc processes them, and emits new **states**. This makes behavior very predictable and testable. **Cubit** is the simpler form, exposing methods instead of events.

Add it: `flutter pub add flutter_bloc`

```dart
import 'package:flutter_bloc/flutter_bloc.dart';

class CounterCubit extends Cubit<int> {
  CounterCubit() : super(0);

  void increment() => emit(state + 1);
  void decrement() => emit(state - 1);
}

// Provide
BlocProvider(
  create: (_) => CounterCubit(),
  child: const CounterView(),
)

// Use
class CounterView extends StatelessWidget {
  const CounterView({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: BlocBuilder<CounterCubit, int>(
          builder: (context, count) => Text('$count'),
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () => context.read<CounterCubit>().increment(),
        child: const Icon(Icons.add),
      ),
    );
  }
}
```

For richer flows, a full `Bloc<Event, State>` handles events using `on<MyEvent>((event, emit) { ... })`. Use `BlocListener` for side effects like navigation.

**Choosing an approach:**

| Solution | Best for | Trade-off |
|---|---|---|
| `setState` | Local, single-widget state | Does not scale to shared state |
| Provider | Small to medium apps, simple sharing | Context-bound, less safe for async |
| Riverpod | Most new projects, async-heavy apps | Newer concepts to learn |
| Bloc / Cubit | Large teams, strict structure, complex flows | More boilerplate |

**Practical advice:**

- No single "best" exists. Pick one, learn it well, and stay consistent within a project.
- Keep business logic **out of widgets** and in notifiers, cubits, or services, so it can be unit tested.
- Keep state **as local as possible** and lift it up only when needed.
- Treat state as **immutable**: replace it with a new value rather than mutating a list in place when using Riverpod or Bloc.

---

[Previous](./[10]-Navigation-And-Routing.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[12]-Networking-And-APIs.md)