[Previous](./[3]-Editing-And-Navigating-Code.md) | [Table of Contents](./[0]-Introduction-to-VisualStudioCode.md) | [Next](./[5]-Extensions-And-Customization.md)

*Core Editor Features*

# Lesson 4 - Debugging In VS Code

## 4.1 launch.json And Debug Configurations

Unlike a full IDE that infers how to run your program, VS Code needs an explicit **debug configuration**, stored in a `launch.json` file inside a `.vscode` folder at your project root. The Run and Debug view (Activity Bar → 🐞) can generate this file for you the first time you click "Run and Debug."

```json
// .vscode/launch.json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Launch app.js",
      "program": "${workspaceFolder}/app.js"
    }
  ]
}
```

Key fields:

- **`type`** — which debugger to use (`node`, `python`, `chrome`, `cppdbg`, etc.), provided by the relevant language extension.
- **`request`** — `"launch"` starts a new process under the debugger; `"attach"` connects to a process already running (e.g. a server started separately).
- **`program`**, **`args`**, **`env`** — the entry file, command-line arguments, and environment variables, mirroring what a JetBrains Run/Debug Configuration provides.

Multiple configurations can exist in the same `launch.json`, selectable from the dropdown at the top of the Run and Debug view — similar in concept to switching Run Configurations in a JetBrains IDE.

---

## 4.2 Breakpoints And Stepping Through Code

Click in the gutter to the left of a line number to set a breakpoint (a red dot appears), then start debugging with `F5`.

```
10   function divide(a, b) {
11 ●     return a / b;      ← breakpoint
12   }
```

Once execution pauses at a breakpoint, the floating debug toolbar and the **Run and Debug** side panel take over:

| Control | Shortcut | Behavior |
|---|---|---|
| Continue | `F5` | Resumes until the next breakpoint |
| Step Over | `F10` | Runs the current line without entering function calls |
| Step Into | `F11` | Enters the function being called |
| Step Out | `Shift+F11` | Finishes the current function, returns to caller |
| Restart | `Ctrl/Cmd+Shift+F5` | Restarts the debug session |
| Stop | `Shift+F5` | Ends the debug session |

The side panel shows **Variables** (locals and closures, expandable for objects), **Watch** (expressions you pin manually), and **Call Stack** (the chain of function calls that led to the current line) — the same core concepts as a JetBrains debugger, just laid out in VS Code's panel style.

**Conditional breakpoints** — right-click a breakpoint dot and choose "Edit Breakpoint" to add a condition (e.g. `i === 500`) or a hit count, so execution only pauses when that condition is met.

---

## 4.3 The Debug Console

The **Debug Console** tab (next to Terminal, at the bottom) serves two purposes while a debug session is active:

1. It prints anything your program writes to standard output/error (e.g. `console.log`, `print`), interleaved with debugger events.
2. It doubles as a REPL: type an expression at the prompt and press Enter to evaluate it in the current paused scope — comparable to "Evaluate Expression" in a JetBrains IDE.

```
Debug Console
> items.filter(i => i.price > 100).length
3
```

This is often faster than adding a temporary `console.log` and rerunning the whole program — you can inspect and even mutate variables live, in-place, while still paused at the breakpoint.

---

## 4.4 Debugging Different Languages And Runtimes

VS Code ships with Node.js and browser (Chrome/Edge) debugging built in, since both are part of the core product's JavaScript focus. Every other language requires its extension to supply a debug adapter that implements the **Debug Adapter Protocol (DAP)** — the debugging equivalent of the Language Server Protocol from Lesson 3.

Common setups:

| Language | Required Extension | Debug Type in launch.json |
|---|---|---|
| Python | Python (Microsoft) | `debugpy` |
| C/C++ | C/C++ (Microsoft) | `cppdbg` |
| Java | Extension Pack for Java | `java` |
| Go | Go (Google) | `go` |
| Browser JS/TS | (built-in) | `chrome` / `msedge` |

Because DAP is a shared standard, the debugging *experience* — breakpoints, stepping, variables, call stack — looks and behaves the same regardless of which language you're debugging, even though a different extension is doing the work behind the scenes for each one.

[Previous](./[3]-Editing-And-Navigating-Code.md) | [Table of Contents](./[0]-Introduction-to-VisualStudioCode.md) | [Next](./[5]-Extensions-And-Customization.md)
