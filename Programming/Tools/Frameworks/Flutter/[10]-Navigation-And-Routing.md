[Previous](./[9]-Animations.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[11]-State-Management.md)

*Navigation And State*

# Lesson 10 - Navigation And Routing

Most apps have several screens. Flutter calls a screen a **route**. This lesson covers the basic stack-based `Navigator`, named routes, the `go_router` package, passing data, and deep links.

---

## 10.1 Navigator basics

The `Navigator` keeps a **stack** of routes. `push` adds a screen on top, and `pop` removes the top screen.

```dart
// Go to a new screen
Navigator.push(
  context,
  MaterialPageRoute(builder: (context) => const DetailPage()),
);

// Go back
Navigator.pop(context);
```

A full example:

```dart
class HomePage extends StatelessWidget {
  const HomePage({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Home')),
      body: Center(
        child: ElevatedButton(
          onPressed: () {
            Navigator.push(
              context,
              MaterialPageRoute(builder: (_) => const DetailPage()),
            );
          },
          child: const Text('Open details'),
        ),
      ),
    );
  }
}

class DetailPage extends StatelessWidget {
  const DetailPage({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Details')), // back button appears automatically
      body: Center(
        child: ElevatedButton(
          onPressed: () => Navigator.pop(context),
          child: const Text('Back'),
        ),
      ),
    );
  }
}
```

**Other methods:**

| Method | Effect |
|---|---|
| `pushReplacement` | Replace the current route (no way back to it) |
| `pushAndRemoveUntil` | Push and clear earlier routes (for example after login) |
| `popUntil` | Pop until a condition is met |
| `maybePop` | Pop only if allowed |

**Returning a result** from a screen:

```dart
// Caller
final choice = await Navigator.push<String>(
  context,
  MaterialPageRoute(builder: (_) => const PickerPage()),
);

// In PickerPage
Navigator.pop(context, 'Option A');
```

After an `await`, check `if (!context.mounted) return;` before using `context` again.

---

## 10.2 Named routes and go_router

**Named routes** map string names to screens in `MaterialApp`:

```dart
MaterialApp(
  initialRoute: '/',
  routes: {
    '/': (context) => const HomePage(),
    '/settings': (context) => const SettingsPage(),
  },
)

Navigator.pushNamed(context, '/settings');
```

Named routes are fine for small apps, but they are weak at path parameters, nested navigation, and web URLs. Flutter's recommended solution for larger apps is the **`go_router`** package, which is declarative and URL-based.

Add it: `flutter pub add go_router`

```dart
import 'package:go_router/go_router.dart';

final _router = GoRouter(
  initialLocation: '/',
  routes: [
    GoRoute(
      path: '/',
      builder: (context, state) => const HomePage(),
      routes: [
        GoRoute(
          path: 'product/:id',           // path parameter
          builder: (context, state) {
            final id = state.pathParameters['id']!;
            return ProductPage(id: id);
          },
        ),
      ],
    ),
    GoRoute(
      path: '/settings',
      builder: (context, state) => const SettingsPage(),
    ),
  ],
);

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp.router(routerConfig: _router);
  }
}
```

Navigating:

```dart
context.go('/product/42');     // replace the stack with this location
context.push('/settings');     // push on top of the stack
context.pop();                 // go back
```

`go` follows the URL structure (good for the web), and `push` behaves like `Navigator.push`.

**Redirects** protect screens, for example sending logged-out users to login:

```dart
GoRouter(
  redirect: (context, state) {
    final loggedIn = authService.isLoggedIn;
    if (!loggedIn && state.matchedLocation != '/login') return '/login';
    return null; // no redirect
  },
  routes: [/* ... */],
)
```

`ShellRoute` / `StatefulShellRoute` keep a persistent bottom navigation bar while the content changes.

---

## 10.3 Passing data between screens

**1. Constructor arguments** are the simplest and type-safe approach:

```dart
class ProductPage extends StatelessWidget {
  const ProductPage({super.key, required this.product});
  final Product product;
  // ...
}

Navigator.push(
  context,
  MaterialPageRoute(builder: (_) => ProductPage(product: myProduct)),
);
```

**2. Named route arguments:**

```dart
Navigator.pushNamed(context, '/product', arguments: myProduct);

// In the destination's build:
final product = ModalRoute.of(context)!.settings.arguments as Product;
```

**3. go_router parameters:**

```dart
// Path parameter (visible in the URL), for small identifiers
context.go('/product/42');
state.pathParameters['id'];

// Query parameter: /search?q=flutter
context.go('/search?q=flutter');
state.uri.queryParameters['q'];

// Extra: any object, not preserved in the URL on web refresh
context.push('/product', extra: myProduct);
state.extra as Product;
```

**Best practice:** pass **ids** through the route, then load the full object in the destination from your data layer or state management. This survives web refreshes and deep links, whereas passing whole objects does not.

**Passing data back:** use the result from `pop` (see 10.1) or shared state (Lesson 11).

---

## 10.4 Deep links

A **deep link** opens a specific screen from outside the app: a URL tapped in a message, a browser, or a QR code.

Types:

- **Custom scheme:** `myapp://product/42`
- **App Links (Android) / Universal Links (iOS):** normal `https://example.com/product/42` URLs that open your app if installed. These are preferred, because they are verified against your domain.

**With go_router,** deep linking is built in. Each route path is already a link target, so `https://example.com/product/42` resolves to `product/:id`.

**Android setup**, in `android/app/src/main/AndroidManifest.xml`, inside the main `<activity>`:

```xml
<intent-filter android:autoVerify="true">
  <action android:name="android.intent.action.VIEW" />
  <category android:name="android.intent.category.DEFAULT" />
  <category android:name="android.intent.category.BROWSABLE" />
  <data android:scheme="https" android:host="example.com" />
</intent-filter>
```

Host a `assetlinks.json` file at `https://example.com/.well-known/assetlinks.json` to verify ownership.

**iOS setup:**

- Add the **Associated Domains** capability with `applinks:example.com` in Xcode.
- Host an `apple-app-site-association` file at `https://example.com/.well-known/apple-app-site-association`.

**Web:** URLs work automatically with `MaterialApp.router`. Use `usePathUrlStrategy()` (from `flutter_web_plugins`) to remove the `#` from URLs.

**Testing:**

```bash
# Android
adb shell am start -a android.intent.action.VIEW -d "https://example.com/product/42"

# iOS simulator
xcrun simctl openurl booted "https://example.com/product/42"
```

Always handle invalid links gracefully, using go_router's `errorBuilder` to show a "not found" page.

---

[Previous](./[9]-Animations.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[11]-State-Management.md)