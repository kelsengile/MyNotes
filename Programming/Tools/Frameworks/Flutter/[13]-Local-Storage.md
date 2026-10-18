[Previous](./[12]-Networking-And-APIs.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[14]-Packages-And-Platform-Features.md)

*Data And Platform Features*

# Lesson 13 - Local Storage

Apps often need to keep data on the device: settings, a login token, cached content, or an entire offline database. Pick the tool based on **how much** data and **how sensitive** it is.

| Need | Use |
|---|---|
| A few small settings (theme, flags) | `shared_preferences` |
| Structured, relational data with queries | SQLite (`sqflite` / Drift) |
| Fast local objects or documents | Hive / Isar |
| Tokens, passwords, secrets | `flutter_secure_storage` |

---

## 13.1 shared_preferences

A simple key-value store for small, non-sensitive values. It stores `int`, `double`, `bool`, `String`, and `List<String>`.

Add it: `flutter pub add shared_preferences`

```dart
import 'package:shared_preferences/shared_preferences.dart';

Future<void> saveSettings() async {
  final prefs = await SharedPreferences.getInstance();
  await prefs.setBool('darkMode', true);
  await prefs.setString('username', 'ada');
  await prefs.setInt('launchCount', 5);
  await prefs.setStringList('tags', ['a', 'b']);
}

Future<void> loadSettings() async {
  final prefs = await SharedPreferences.getInstance();
  final dark = prefs.getBool('darkMode') ?? false;   // null if missing
  final name = prefs.getString('username') ?? 'guest';
  print('$dark $name');
}

Future<void> clear() async {
  final prefs = await SharedPreferences.getInstance();
  await prefs.remove('username');
  // await prefs.clear();   // wipes everything
}
```

**Typical use:** load saved values before `runApp`, for example restoring the theme:

```dart
Future<void> main() async {
  WidgetsFlutterBinding.ensureInitialized();
  final prefs = await SharedPreferences.getInstance();
  final isDark = prefs.getBool('darkMode') ?? false;
  runApp(MyApp(startDark: isDark));
}
```

**Limits:** it is not designed for large or complex data, it is **not encrypted**, and writes are not guaranteed to be instant on all platforms. Newer versions also offer `SharedPreferencesAsync` and `SharedPreferencesWithCache` for more predictable behavior.

---

## 13.2 SQLite and Drift

**SQLite** is a full relational database stored in a single file. Use it for large lists, relationships (users have orders), searching, and sorting.

**`sqflite`** gives raw SQL access. Add it: `flutter pub add sqflite path`

```dart
import 'package:sqflite/sqflite.dart';
import 'package:path/path.dart';

Future<Database> openAppDb() async {
  final dbPath = join(await getDatabasesPath(), 'app.db');
  return openDatabase(
    dbPath,
    version: 1,
    onCreate: (db, version) async {
      await db.execute('''
        CREATE TABLE notes (
          id INTEGER PRIMARY KEY AUTOINCREMENT,
          title TEXT NOT NULL,
          body TEXT
        )
      ''');
    },
  );
}

Future<void> demo() async {
  final db = await openAppDb();

  final id = await db.insert('notes', {'title': 'First', 'body': 'Hello'});

  final rows = await db.query('notes', orderBy: 'id DESC');
  print(rows); // List<Map<String, Object?>>

  await db.update('notes', {'title': 'Edited'}, where: 'id = ?', whereArgs: [id]);
  await db.delete('notes', where: 'id = ?', whereArgs: [id]);
}
```

Always use `?` placeholders with `whereArgs` and never string concatenation, which prevents SQL injection. To change the schema later, bump `version` and handle `onUpgrade` with migrations.

**Drift** is a type-safe layer on top of SQLite. You describe tables in Dart and it generates the queries and models, with reactive streams that update the UI when data changes:

```dart
class Notes extends Table {
  IntColumn get id => integer().autoIncrement()();
  TextColumn get title => text()();
  TextColumn get body => text().nullable()();
}

// After code generation:
// final all = await db.select(db.notes).get();
// final stream = db.select(db.notes).watch();   // live updates
```

Choose `sqflite` for simple needs and Drift for larger apps with migrations and complex queries. Drift also works on web and desktop.

---

## 13.3 Hive and Isar

These are **NoSQL** databases that store Dart objects directly, without writing SQL. They are fast and simple for caching and offline data.

**Hive** is a lightweight key-value box database. The community-maintained `hive_ce` package continues the original project. Add it: `flutter pub add hive_ce hive_ce_flutter`

```dart
import 'package:hive_ce_flutter/hive_flutter.dart';

Future<void> main() async {
  await Hive.initFlutter();
  await Hive.openBox('settings');
  runApp(const MyApp());
}

void useBox() {
  final box = Hive.box('settings');
  box.put('theme', 'dark');
  box.put('user', {'name': 'Ada', 'age': 36});   // simple types and maps
  final theme = box.get('theme', defaultValue: 'light');
  box.delete('theme');
}
```

For custom classes you register a `TypeAdapter` (generated with annotations).

**Isar** is an object database with indexes, queries, and links between objects, designed for larger local datasets:

```dart
@collection
class Task {
  Id id = Isar.autoIncrement;
  late String title;
  bool done = false;
}

// await isar.writeTxn(() => isar.tasks.put(Task()..title = 'Buy milk'));
// final open = await isar.tasks.filter().doneEqualTo(false).findAll();
```

**Maintenance note:** package maintenance changes over time. Before adopting Hive or Isar, check pub.dev for recent releases and open issues. If long-term support is a priority, SQLite with Drift is a very safe, well-maintained choice.

**Choosing:**

| Choice | Strength |
|---|---|
| Hive | Very simple key-value caching |
| Isar | Fast object queries without SQL |
| Drift/SQLite | Relational data, migrations, mature tooling |

---

## 13.4 Secure storage

`shared_preferences`, Hive, and SQLite files are **plain and readable** on a rooted or backed-up device. For secrets (auth tokens, refresh tokens, encryption keys) use **`flutter_secure_storage`**, which stores data in the platform's secure store:

- **iOS / macOS:** Keychain
- **Android:** encrypted storage backed by the Android Keystore

Add it: `flutter pub add flutter_secure_storage`

```dart
import 'package:flutter_secure_storage/flutter_secure_storage.dart';

const storage = FlutterSecureStorage();

Future<void> secureDemo() async {
  await storage.write(key: 'auth_token', value: 'abc123');

  final token = await storage.read(key: 'auth_token'); // null if missing
  print(token);

  await storage.delete(key: 'auth_token');
  // await storage.deleteAll();
}
```

**Good practices:**

- Store **only small secrets** here, not large data.
- **Log out = delete** the stored tokens.
- Never hard-code secrets in source code. Anything bundled in the app can be extracted.
- Don't log sensitive values.
- On the web, secure storage is weaker (it uses browser storage), so avoid storing highly sensitive secrets client-side there.
- For biometric protection, combine with `local_auth`.

**Putting it together,** a typical app uses `shared_preferences` for UI settings, secure storage for the login token, and a database (Drift/SQLite or Isar) for cached content and offline data.

---

[Previous](./[12]-Networking-And-APIs.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[14]-Packages-And-Platform-Features.md)