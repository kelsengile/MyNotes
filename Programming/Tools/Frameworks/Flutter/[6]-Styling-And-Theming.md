[Previous](./[5]-Material-And-Cupertino.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[7]-Forms-And-User-Input.md)

*Building The UI*

# Lesson 6 - Styling And Theming

A theme centralizes your app's colors, text styles, and component looks so you define them once and every screen stays consistent. This lesson covers themes, typography, dark mode, and custom fonts and assets.

---

## 6.1 ThemeData and color schemes

Set a theme on `MaterialApp`:

```dart
MaterialApp(
  theme: ThemeData(
    useMaterial3: true,
    colorScheme: ColorScheme.fromSeed(seedColor: Colors.indigo),
  ),
  home: const HomePage(),
)
```

`ColorScheme.fromSeed` generates a full, harmonious palette from one color. The scheme contains named roles such as `primary`, `onPrimary`, `secondary`, `surface`, `error`, and their containers.

Read theme values anywhere using `Theme.of(context)`:

```dart
Container(
  color: Theme.of(context).colorScheme.primaryContainer,
  child: Text(
    'Styled',
    style: TextStyle(color: Theme.of(context).colorScheme.onPrimaryContainer),
  ),
)
```

Prefer theme roles over hard-coded colors. Your UI then adapts automatically when the theme changes.

**Customizing a component globally** with component themes:

```dart
ThemeData(
  colorScheme: ColorScheme.fromSeed(seedColor: Colors.indigo),
  appBarTheme: const AppBarTheme(centerTitle: true),
  elevatedButtonTheme: ElevatedButtonThemeData(
    style: ElevatedButton.styleFrom(
      shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(12)),
      padding: const EdgeInsets.symmetric(horizontal: 24, vertical: 14),
    ),
  ),
  inputDecorationTheme: const InputDecorationTheme(
    border: OutlineInputBorder(),
  ),
)
```

To restyle one part of the tree only, wrap it in a `Theme` widget with `data: Theme.of(context).copyWith(...)`.

---

## 6.2 Typography

Text styles come from `Theme.of(context).textTheme`, which holds the Material 3 type scale:

| Group | Roles |
|---|---|
| Display | `displayLarge`, `displayMedium`, `displaySmall` |
| Headline | `headlineLarge`, `headlineMedium`, `headlineSmall` |
| Title | `titleLarge`, `titleMedium`, `titleSmall` |
| Body | `bodyLarge`, `bodyMedium`, `bodySmall` |
| Label | `labelLarge`, `labelMedium`, `labelSmall` |

```dart
Text('Page title', style: Theme.of(context).textTheme.headlineMedium);
Text('Body copy', style: Theme.of(context).textTheme.bodyMedium);
```

Tweak a style with `copyWith`:

```dart
Text(
  'Important',
  style: Theme.of(context).textTheme.titleLarge?.copyWith(
        fontWeight: FontWeight.bold,
        color: Colors.red,
      ),
)
```

Useful `TextStyle` properties: `fontSize`, `fontWeight`, `fontStyle`, `letterSpacing`, `height` (line height multiplier), `decoration`. On `Text` itself: `maxLines`, `overflow: TextOverflow.ellipsis`, `textAlign`.

Rich text with mixed styles uses `Text.rich`:

```dart
Text.rich(
  TextSpan(
    text: 'Hello ',
    children: [
      TextSpan(text: 'world', style: TextStyle(fontWeight: FontWeight.bold)),
    ],
  ),
)
```

Flutter also respects the user's system **text scale** setting, so avoid fixed-height containers around text.

---

## 6.3 Dark mode

Provide a light and a dark theme, and let `themeMode` choose:

```dart
MaterialApp(
  theme: ThemeData(
    colorScheme: ColorScheme.fromSeed(seedColor: Colors.indigo),
  ),
  darkTheme: ThemeData(
    colorScheme: ColorScheme.fromSeed(
      seedColor: Colors.indigo,
      brightness: Brightness.dark,
    ),
  ),
  themeMode: ThemeMode.system, // follow the device setting
  home: const HomePage(),
)
```

`ThemeMode` values: `system`, `light`, `dark`.

**Letting the user toggle it.** Hold the mode in state and rebuild `MaterialApp`:

```dart
class MyApp extends StatefulWidget {
  const MyApp({super.key});
  @override
  State<MyApp> createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  ThemeMode _mode = ThemeMode.system;

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      theme: ThemeData(colorScheme: ColorScheme.fromSeed(seedColor: Colors.teal)),
      darkTheme: ThemeData(
        colorScheme: ColorScheme.fromSeed(
          seedColor: Colors.teal,
          brightness: Brightness.dark,
        ),
      ),
      themeMode: _mode,
      home: Scaffold(
        body: Center(
          child: Switch(
            value: _mode == ThemeMode.dark,
            onChanged: (on) => setState(
              () => _mode = on ? ThemeMode.dark : ThemeMode.light,
            ),
          ),
        ),
      ),
    );
  }
}
```

To remember the choice after restart, save it with `shared_preferences` (Lesson 13). To check the current brightness: `Theme.of(context).brightness == Brightness.dark`.

---

## 6.4 Custom fonts and assets

**Assets** (images, JSON, etc.) must be declared in `pubspec.yaml`. Indentation matters in YAML:

```yaml
flutter:
  uses-material-design: true

  assets:
    - assets/images/
    - assets/data/config.json

  fonts:
    - family: Poppins
      fonts:
        - asset: assets/fonts/Poppins-Regular.ttf
        - asset: assets/fonts/Poppins-Bold.ttf
          weight: 700
```

Declaring a folder (ending in `/`) includes the files directly inside it. After editing `pubspec.yaml`, run `flutter pub get` (editors usually do this on save).

**Using assets:**

```dart
Image.asset('assets/images/logo.png', width: 120);

// Load text/JSON
import 'package:flutter/services.dart' show rootBundle;
final raw = await rootBundle.loadString('assets/data/config.json');
```

**Using the font** across the app via the theme, or on a single widget:

```dart
ThemeData(fontFamily: 'Poppins')

Text('Custom', style: TextStyle(fontFamily: 'Poppins', fontWeight: FontWeight.w700))
```

**Google Fonts without bundling files:** the `google_fonts` package downloads fonts at runtime.

```dart
import 'package:google_fonts/google_fonts.dart';

Text('Hello', style: GoogleFonts.lato(fontSize: 20));
```

Bundling fonts is safer for offline use and store release, and `google_fonts` is quicker for prototypes.

---

[Previous](./[5]-Material-And-Cupertino.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[7]-Forms-And-User-Input.md)