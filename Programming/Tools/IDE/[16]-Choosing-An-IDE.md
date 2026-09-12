[Previous](./[15]-Productivity-Tips-And-Shortcuts.md) | [Table of Contents](./[0]-Introduction-to-IDEs.md)

*Next Steps*

# Lesson 16 - Choosing An IDE

## 16.1 Matching IDEs To Project Needs

There's no universally "best" IDE — the right choice depends on what you're building. A general-purpose editor with extensions is flexible and lightweight, while a specialized IDE built for one ecosystem often provides deeper, more accurate tooling for that specific use case at the cost of being less flexible outside it. Consider what the project actually needs before picking a tool for its popularity alone.

| Factor | Leans toward a specialized IDE | Leans toward a general-purpose editor |
|---|---|---|
| Ecosystem depth needed | High (e.g. deep Android tooling) | Low to moderate |
| Number of languages used | One, consistently | Several, mixed |
| Hardware constraints | Fewer (can afford a heavier tool) | Tighter (need something lightweight) |

---

## 16.2 Language-Specific Considerations

Some languages have a clear dominant IDE because of how tightly it's built into that ecosystem — Xcode for Apple-platform development, or Android Studio for Android. Others, like Python or JavaScript, are well supported across many different IDEs, so the choice there comes down more to personal preference and team convention than any single tool having a unique advantage.

| Language | Dominant/official IDE? | Well-supported alternatives |
|---|---|---|
| Swift (iOS/macOS) | Xcode (effectively required) | Limited alternatives |
| Java (Android) | Android Studio (effectively required) | IntelliJ IDEA (same base) |
| Python | None dominant | VS Code, PyCharm, Jupyter |
| JavaScript/TypeScript | None dominant | VS Code, WebStorm |

---

## 16.3 Performance And Resource Usage

Heavier IDEs with deep language analysis generally use more memory and CPU than lightweight editors, which matters more on older hardware or when working with very large codebases. It's worth trying a candidate IDE on a project close to the size you'll actually be working with, since performance characteristics that seem fine on a small test project don't always hold up at scale.

| IDE weight | Typical RAM usage | Feels best on |
|---|---|---|
| Lightweight (VS Code, Sublime) | Lower | Older or resource-limited hardware |
| Full IDE (JetBrains, Visual Studio) | Higher | Modern hardware, large codebases needing deep analysis |

---

## 16.4 Where To Go Next

The concepts in this Topic apply across virtually every IDE you'll encounter, so the best next step is hands-on practice: install a candidate IDE for the language you're learning, open a small project, and work through setting up a build, hitting a breakpoint, and installing one extension. From there, explore the Topics on specific languages and tools elsewhere in this repository to see these concepts applied in context.

**A reasonable first-session checklist:**

1. Install the IDE and open a small, existing project (not a blank one).
2. Trigger a build or run the project once, successfully.
3. Set a single breakpoint and hit it.
4. Install exactly one extension you already know you'll need.

Finishing all four in one sitting is enough to move from "installed but unfamiliar" to "comfortable enough to start real work."

---

[Previous](./[15]-Productivity-Tips-And-Shortcuts.md) | [Table of Contents](./[0]-Introduction-to-IDEs.md)
