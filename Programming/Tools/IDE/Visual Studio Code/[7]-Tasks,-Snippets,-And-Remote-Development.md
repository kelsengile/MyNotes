[Previous](./[6]-Source-Control-With-Git-In-VS-Code.md) | [Table of Contents](./[0]-Introduction-to-VisualStudioCode.md)

*Development Workflows*

# Lesson 7 - Tasks, Snippets, And Remote Development

## 7.1 tasks.json And Automating Builds

Tasks let VS Code run external commands (build scripts, linters, test runners) from inside the editor, defined in `.vscode/tasks.json` at the project root.

```json
// .vscode/tasks.json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Build",
      "type": "shell",
      "command": "npm run build",
      "group": { "kind": "build", "isDefault": true },
      "problemMatcher": ["$tsc"]
    }
  ]
}
```

- **Run a task** — Command Palette → "Tasks: Run Task," or `Ctrl/Cmd+Shift+B` for the task marked `"isDefault": true` in the `"build"` group.
- **`problemMatcher`** — parses the command's output for errors/warnings and surfaces them in the **Problems** panel with clickable links to the relevant file and line, instead of leaving them as plain terminal text.
- **Task dependencies** — a task can declare `"dependsOn"` other tasks, letting a single command (e.g. "Build and Test") chain several steps together.

This is VS Code's lightweight equivalent to the Gradle/Maven/npm task windows built into JetBrains IDEs — the difference is that in VS Code you define the wiring yourself in `tasks.json`, rather than the IDE auto-discovering tasks from a build file.

---

## 7.2 Custom Code Snippets

Snippets are reusable code templates triggered by typing a short prefix and pressing `Tab`. VS Code ships with built-in snippets per language, and custom ones can be defined per user or per project.

Create custom snippets via Command Palette → "Snippets: Configure User Snippets," which opens a `.json` file for the chosen language:

```json
// javascript.json (user snippets)
{
  "Console Log": {
    "prefix": "clg",
    "body": [
      "console.log('$1:', $1);",
      "$2"
    ],
    "description": "Log a labeled variable to the console"
  }
}
```

Typing `clg` then `Tab` expands to `console.log('':, );` with the cursor placed at `$1` (the tab stop), and pressing `Tab` again moves to `$2`. Multiple tab stops let a single snippet fill in several parts of a template in sequence — extremely useful for boilerplate you type often, like a new React component skeleton or a standard test-case structure.

---

## 7.3 Remote Development (SSH, Containers, WSL)

The **Remote Development** extension pack lets VS Code's interface run locally while the actual files, terminal, and extensions execute somewhere else entirely — the editor UI is just a thin client talking to a VS Code Server process on the remote side.

```
 Local Machine                    Remote Target
┌─────────────┐    SSH/Container  ┌─────────────────┐
│ VS Code UI    │ ───────────────▶ │ VS Code Server     │
│ (what you see)│                  │ Your actual files   │
└─────────────┘                  │ Extensions run here  │
                                   └─────────────────┘
```

Three remote modes, each solving a different problem:

- **Remote - SSH** — edit files on a remote server (e.g. a cloud VM) as though they were local, useful when the code or its dependencies must live on that machine.
- **Dev Containers** — open a project inside a Docker container defined by a `.devcontainer/devcontainer.json` file, guaranteeing every teammate develops with an identical environment (same OS, tool versions, dependencies) regardless of their host machine.
- **WSL (Windows Subsystem for Linux)** — on Windows, edit files that live inside a Linux WSL distribution, useful for tools or scripts that assume a Linux environment.

```json
// .devcontainer/devcontainer.json
{
  "image": "mcr.microsoft.com/devcontainers/python:3.12",
  "postCreateCommand": "pip install -r requirements.txt"
}
```

In every mode, extensions relevant to the code itself (linters, language servers) install and run on the **remote** side, while a small set of UI-only extensions (like themes) still run locally — VS Code splits this automatically per extension.

---

## 7.4 Live Share For Collaboration

**Live Share** (a Microsoft extension) turns a VS Code session into a real-time collaborative workspace, similar to a Google Docs session but for code.

- **Shared editing** — a host shares a session link; guests join (in VS Code or even a browser) and can see and edit the same files simultaneously, with each participant's cursor shown in a distinct color.
- **Shared terminal and debugging** — the host can optionally share their integrated terminal and even an active debug session, letting a guest step through code and inspect variables without needing the project set up on their own machine.
- **Follow mode** — a guest can "follow" the host's cursor and scroll position, useful for a presenter walking a group through code during a pairing session or a code review.

```
┌───────────────────────────────────────┐
│ Live Share Session: "Debug session #4"  │
│  🟢 You (host)                          │
│  🔵 Alex (guest) — editing app.js        │
│  🟡 Sam (guest) — following you          │
└───────────────────────────────────────┘
```

Because guests don't need the project cloned locally, Live Share is particularly useful for quick pair-programming or interview-style sessions where setting up a full local environment for a one-off collaboration would be overkill.

[Previous](./[6]-Source-Control-With-Git-In-VS-Code.md) | [Table of Contents](./[0]-Introduction-to-VisualStudioCode.md)
