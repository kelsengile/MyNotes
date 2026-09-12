[Previous](./[5]-File-And-Code-Navigation.md) | [Table of Contents](./[0]-Introduction-to-IDEs.md) | [Next](./[7]-The-Integrated-Terminal.md)

*Building And Running Code*

# Lesson 6 - Build Systems And Task Runners

## 6.1 What Is a Build System

For compiled languages, source code has to be translated into a runnable program before it can be executed — this process is called building, and the tool that manages it is a build system (examples include Make, Gradle, MSBuild, or webpack for bundling JavaScript). An IDE typically integrates with the project's build system so you can trigger a build with a single click instead of memorizing command-line invocations.

| Build system | Language/ecosystem |
|---|---|
| Make | C, C++, many others |
| Gradle | Java, Kotlin, Android |
| MSBuild | C#, .NET |
| webpack / Vite | JavaScript/TypeScript bundling |

---

## 6.2 Build Configurations

Most projects support multiple build configurations for different purposes — commonly a **Debug** configuration that keeps extra information for troubleshooting and skips optimizations, and a **Release** configuration that strips that out and optimizes for performance. IDEs let you switch between configurations from a dropdown or menu, and remember separate settings (like output paths) for each.

| Configuration | Optimizations | Debug symbols | Typical output size |
|---|---|---|---|
| Debug | Off | Included | Larger |
| Release | On | Stripped | Smaller, faster |

---

## 6.3 Tasks And Scripts

Beyond compiling, projects often need repeatable actions — running tests, formatting code, generating documentation, or deploying a build. IDEs let you define these as **tasks**, short scripts or commands tied to a name and often a keyboard shortcut, so common actions don't require retyping a command every time.

**Example task definition (VS Code style):**

```json
{
  "label": "Run Tests",
  "type": "shell",
  "command": "npm test",
  "problemMatcher": []
}
```

Once saved, running "Run Tests" from the command palette (or its assigned shortcut) executes `npm test` without anyone needing to open a terminal and type it by hand.

---

## 6.4 Output And Problems Panels

When a build runs, its console output — compiler messages, warnings, and errors — appears in an **Output panel**. Many IDEs also parse that output into a separate **Problems panel**, turning raw error text into a clickable list that jumps straight to the offending line, which is much faster than reading through a wall of log text.

**Example:** a raw compiler error like:

```
src/App.js:14:22: error: Cannot find name 'usrName'. Did you mean 'userName'?
```

becomes a single clickable row in the Problems panel reading "Cannot find name 'usrName'" — clicking it opens `App.js` with the cursor already placed on line 14, column 22.

---

[Previous](./[5]-File-And-Code-Navigation.md) | [Table of Contents](./[0]-Introduction-to-IDEs.md) | [Next](./[7]-The-Integrated-Terminal.md)
