[Table of Contents](./[0]-Introduction-to-JetBrainsIDE.md) | [Next](./[2]-The-JetBrains-Interface.md)

*Getting Started*

# Lesson 1 - Installing JetBrains IDEs And The Toolbox App

## 1.1 The JetBrains Toolbox App

The Toolbox App is JetBrains' official installer and update manager. Instead of downloading each IDE separately from the website, you install Toolbox once, and it handles installing, updating, and uninstalling every JetBrains IDE on your machine.

```
Toolbox App
 ├── IntelliJ IDEA   [Install] [Update]
 ├── PyCharm         [Install] [Update]
 ├── WebStorm        [Install] [Update]
 ├── Rider           [Install] [Update]
 └── CLion           [Install] [Update]
```

Benefits of using Toolbox instead of manual downloads:

- **One-click updates** — no hunting for the latest `.exe`, `.dmg`, or `.tar.gz` file.
- **Multiple versions side by side** — keep a stable version and an EAP (Early Access Program) build installed at once.
- **Centralized settings** — manage JVM options and installation locations for every IDE from one place.
- **Command-line launchers** — Toolbox can generate shell shortcuts (e.g. `idea`, `pycharm`, `webstorm`) so you can open a project from the terminal.

Download: [jetbrains.com/toolbox-app](https://www.jetbrains.com/toolbox-app/)

---

## 1.2 Choosing The Right JetBrains IDE For Your Language

Each JetBrains IDE targets a specific language ecosystem, but they all sit on the same underlying platform (internally called "IntelliJ Platform"). Pick based on what you're building, not which one is "better":

| IDE | Primary Language(s) | Typical Use Case |
|---|---|---|
| IntelliJ IDEA | Java, Kotlin | Backend services, Android (via plugin), enterprise apps |
| PyCharm | Python | Data science, scripting, Django/Flask web apps |
| WebStorm | JavaScript, TypeScript | Frontend frameworks, Node.js backends |
| Rider | C#, .NET | ASP.NET, Unity game development |
| CLion | C, C++ | Systems programming, embedded development |
| GoLand | Go | Microservices, CLI tools |

If you work across multiple languages, you don't need a separate IDE for each — IntelliJ IDEA Ultimate and WebStorm both support plugins that add multi-language support, but a dedicated IDE usually offers deeper, faster tooling for its primary language.

---

## 1.3 Free vs Commercial Licensing (Community vs Ultimate)

JetBrains IDEs come in two tiers:

- **Community Edition** — free and open-source. Available for IntelliJ IDEA and PyCharm. Covers core language support, basic refactoring, and version control.
- **Ultimate/Commercial Edition** — paid (with a free trial). Adds framework support (Spring, Django, React), database tools, HTTP client, and remote development features. Rider, WebStorm, CLion, and GoLand are commercial-only, though they offer free licenses for students, open-source maintainers, and educators.

```
                 ┌─────────────────────┐
                 │   IntelliJ Platform │
                 └──────────┬──────────┘
           ┌─────────────────┴─────────────────┐
   ┌───────▼────────┐               ┌───────────▼──────────┐
   │ Community (Free)│               │ Ultimate (Paid)      │
   │ - Core editing  │               │ - Everything in Free │
   │ - Basic VCS     │               │ - Frameworks         │
   │ - Debugger      │               │ - Database tools     │
   └─────────────────┘               │ - HTTP Client        │
                                      └───────────────────────┘
```

A free student license or open-source license unlocks the Ultimate feature set at no cost — check eligibility on the JetBrains website before assuming you must pay.

---

## 1.4 Creating Your First Project

Once your IDE of choice is installed, creating a project follows the same general flow across the whole family:

1. Open the IDE and select **New Project** from the Welcome screen.
2. Choose a project type or template (e.g. "Pure Python", "Spring Boot", "React App").
3. Set the project name, location, and language/framework version (e.g. Python 3.12, Java 21, Node 20).
4. Choose whether to use a version control system (Git) from the start — this can also be added later.
5. Click **Create**. The IDE indexes the project (analyzing files to power autocomplete and navigation) before the editor becomes fully interactive.

**Tip:** the first indexing pass can take a minute or two on larger projects. This is normal — the IDE is building the project-wide understanding that powers its code intelligence features, covered in [Lesson 3](./[3]-Code-Intelligence-And-Navigation.md).

[Table of Contents](./[0]-Introduction-to-JetBrainsIDE.md) | [Next](./[2]-The-JetBrains-Interface.md)
