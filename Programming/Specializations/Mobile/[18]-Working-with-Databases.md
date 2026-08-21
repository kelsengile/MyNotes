[Previous](./[17]-Local-Storage-and-Persistence.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[19]-Making-Network-Requests.md)

*State & Data*

# Lesson 18 - Working with Databases (SQLite, Realm)

## 18.1 When You Need a Real Database

Once an app has structured, related, or large amounts of local data — think a note-taking app with thousands of notes, tags, and folders — key-value storage isn't enough. You need a database that supports querying, relationships, and efficient lookups even as data grows.

---

## 18.2 SQLite

**SQLite** is a lightweight, file-based relational database built into both iOS and Android, and is the most common choice for structured local storage. Data is organized into tables with rows and columns, queried using SQL:

```sql
CREATE TABLE notes (id INTEGER PRIMARY KEY, title TEXT, body TEXT, created_at INTEGER);
SELECT * FROM notes WHERE title LIKE '%groceries%' ORDER BY created_at DESC;
```

Most frameworks provide a wrapper so you don't write raw SQL by hand:

- **Flutter**: `sqflite`, or the higher-level `drift` package.
- **Android**: **Room**, which generates SQL from annotated Kotlin classes.
- **iOS**: **Core Data** (Apple's own object-graph framework, backed by SQLite under the hood) or direct SQLite wrappers like GRDB.

---

## 18.3 NoSQL / Object Databases: Realm

**Realm** is an alternative, object-oriented mobile database — instead of writing SQL, you define model classes and query them directly as objects, which many developers find more natural in an object-oriented codebase:

```dart
class Note extends RealmObject {
  late String title;
  late String body;
}
final notes = realm.all<Note>().query('title CONTAINS \$0', 'groceries');
```

---

## 18.4 Migrations

As an app evolves, its database schema changes — a new column, a renamed table. A **migration** is a versioned script that transforms an existing user's local database from an old schema to a new one without losing their data. Forgetting to handle migrations is a common cause of crashes after an app update, since the old on-device database won't match what the new app code expects.

[Previous](./[17]-Local-Storage-and-Persistence.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[19]-Making-Network-Requests.md)
