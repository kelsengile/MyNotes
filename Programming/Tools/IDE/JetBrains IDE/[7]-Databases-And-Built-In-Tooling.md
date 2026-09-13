[Previous](./[6]-Version-Control-Integration.md) | [Table of Contents](./[0]-Introduction-to-JetBrainsIDE.md)

*Working With Data And Tools*

# Lesson 7 - Databases And Built-In Tooling

## 7.1 The Built-In Database Tool Window

The Database tool window (found in Ultimate-tier IDEs, or as a bundled feature in some others) lets you connect to a database and browse it without opening a separate client like DBeaver or pgAdmin.

```
Database
 ├── 🐘 PostgreSQL - local
 │    ├── public
 │    │    ├── users
 │    │    ├── orders
 │    │    └── products
 │    └── information_schema
 └── 🐬 MySQL - staging
```

Core capabilities:

- **Connect to almost anything** — PostgreSQL, MySQL, SQLite, MongoDB, Oracle, SQL Server, and more, via built-in or downloadable drivers.
- **Table data editor** — view and edit rows in a spreadsheet-like grid, including filtering and sorting without writing SQL.
- **Schema diagrams** — visualize tables and their foreign-key relationships as an entity-relationship diagram.
- **Query console** — write and run SQL directly against the connected database, with the same autocomplete and inspections used for source code.

```sql
-- Autocomplete knows your actual schema:
SELECT u.name, o.total
FROM users u
JOIN orders o ON o.user_id = u.id   -- suggests real column names as you type
WHERE o.total > 100;
```

---

## 7.2 The HTTP Client For API Testing

The built-in HTTP Client lets you send HTTP requests and inspect responses directly inside the IDE, similar to Postman or Insomnia, using plain-text `.http` files that can be committed to version control alongside your code.

```http
### Get a single user
GET https://api.example.com/users/42
Authorization: Bearer {{auth_token}}

### Create a new order
POST https://api.example.com/orders
Content-Type: application/json

{
  "userId": 42,
  "items": ["sku-123", "sku-456"]
}
```

Each `###` starts a new request in the same file, and clicking the green ▶ gutter icon next to a request runs it and opens the response in a split pane. Variables like `{{auth_token}}` can be defined in an accompanying environment file, so the same `.http` file works against local, staging, and production endpoints just by switching environments.

---

## 7.3 Task Runners And Build Tool Integration

JetBrains IDEs integrate directly with your project's build tool, exposing its tasks in a dedicated tool window instead of requiring the terminal:

| Language Ecosystem | Build Tool | Tool Window |
|---|---|---|
| Java/Kotlin | Maven / Gradle | Maven / Gradle |
| JavaScript/TypeScript | npm / yarn / pnpm | npm |
| .NET | MSBuild | Solution Explorer |
| Python | (via Run Configurations) | N/A — uses Run/Debug configs |

```
Gradle
 ├── Tasks
 │    ├── build
 │    │    ├── build
 │    │    └── clean
 │    ├── verification
 │    │    └── test
 │    └── application
 │         └── run
```

Double-clicking a task (e.g. `test`) runs it exactly as the command line would (`./gradlew test`), but output is routed to the Run tool window with clickable stack traces — clicking a line in a failing test's stack trace jumps straight to that line of source code.

---

## 7.4 Language-Specific Tooling Across The JetBrains Family

While the platform is shared, each IDE bundles tooling specific to its language's ecosystem on top of the common features covered in this Topic:

- **IntelliJ IDEA** — a built-in decompiler (view bytecode as readable Java), Spring-specific navigation (jump from a controller straight to its `@RequestMapping` endpoint diagram), and JVM profiler.
- **PyCharm** — a scientific mode with an interactive plotting pane for NumPy/Pandas/Matplotlib output, virtual environment management, and Jupyter notebook support directly in the editor.
- **WebStorm** — built-in support for React/Vue/Angular component navigation, live browser preview with hot reload, and a visual regression tool for CSS.
- **Rider** — a built-in Unity integration (inspect GameObjects and scenes) and full .NET solution/project management identical to Visual Studio's model.
- **CLion** — CMake integration, embedded GDB/LLDB debugging, and remote/cross-compilation toolchain support for embedded targets.
- **GoLand** — built-in `go vet` and `golint` integration, and goroutine-aware debugging that visualizes concurrent execution.

This is the core idea behind the whole JetBrains family: the ~80% of features you've learned in this Topic — navigation, refactoring, debugging, VCS, databases — transfer directly to any of these IDEs. The remaining ~20% is language-specific tooling layered on top, which you pick up naturally as you work in that ecosystem.

[Previous](./[6]-Version-Control-Integration.md) | [Table of Contents](./[0]-Introduction-to-JetBrainsIDE.md)
