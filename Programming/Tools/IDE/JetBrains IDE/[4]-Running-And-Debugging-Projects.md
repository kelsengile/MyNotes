[Previous](./[3]-Code-Intelligence-And-Navigation.md) | [Table of Contents](./[0]-Introduction-to-JetBrainsIDE.md) | [Next](./[5]-Customizing-The-JetBrains-Environment.md)

*Core IDE Features*

# Lesson 4 - Running And Debugging Projects

## 4.1 Run/Debug Configurations

A Run/Debug Configuration tells the IDE exactly how to launch your program: which file or class is the entry point, what arguments to pass, which environment variables to set, and which interpreter or JDK to use.

```
Run/Debug Configuration: "MyApp"
 ├── Type: Application
 ├── Main class: com.example.Main
 ├── Program arguments: --port=8080
 ├── Environment variables: DB_URL=localhost:5432
 └── JRE: 21 (corretto-21.0.1)
```

You can create multiple configurations for the same project (e.g. "Run Locally," "Run With Test Data," "Run Against Staging DB") and switch between them from the dropdown in the top toolbar, next to the Run and Debug buttons.

---

## 4.2 Breakpoints And The Debugger Window

A **breakpoint** pauses execution at a specific line so you can inspect the program's state. Click the gutter next to a line number to set one — a red dot appears.

```
12   public int divide(int a, int b) {
13 ●     int result = a / b;      ← breakpoint set here
14       return result;
15   }
```

When execution hits the breakpoint, the Debug tool window opens with:

- **Frames** — the call stack, showing which methods called which, top-to-bottom.
- **Variables** — the current values of all variables in scope, which can be expanded for objects/arrays.
- **Step controls** — Step Over (`F8`, runs the current line without entering called methods), Step Into (`F7`, enters the called method), Step Out (`Shift+F8`, finishes the current method and returns to its caller), and Resume Program (`F9`, continues until the next breakpoint).

**Conditional breakpoints** (right-click a breakpoint) only pause when an expression is true — useful for a bug that only appears on, say, the 500th loop iteration.

---

## 4.3 Watches And Evaluate Expression

Beyond the automatically-shown local variables, two tools let you inspect anything on demand while paused at a breakpoint:

- **Watches** — pin specific expressions (e.g. `user.getOrders().size()`) so their value is always shown while debugging, even as you step through different methods.
- **Evaluate Expression** (`Alt+F8`) — run an arbitrary expression or even a small snippet of code in the current paused context, without modifying or rerunning your program. Useful for testing "what would this return right now?" without adding a temporary `System.out.println`.

```
Evaluate Expression:
> orders.stream().filter(o -> o.getTotal() > 100).count()
= 3
```

---

## 4.4 The Built-In Terminal

Every JetBrains IDE ships with an embedded terminal (`Alt+F12`) that opens already positioned in your project's root directory, using your system's default shell (bash, zsh, PowerShell, etc.).

Common uses:

- Running scripts or CLI tools (`npm install`, `pip install -r requirements.txt`, `./gradlew build`) without leaving the IDE.
- Quick Git commands not exposed in the GUI.
- Multiple terminal tabs for running a dev server in one and issuing commands in another simultaneously.

```
┌─────────────────────────────────────────┐
│ Terminal ▾  +                            │
├─────────────────────────────────────────┤
│ user@machine my-project % npm run dev    │
│ > Local:   http://localhost:3000         │
└─────────────────────────────────────────┘
```

Because the terminal is embedded, its working directory always matches your project, avoiding the classic "wrong folder" mistake from switching between an external terminal and the IDE.

[Previous](./[3]-Code-Intelligence-And-Navigation.md) | [Table of Contents](./[0]-Introduction-to-JetBrainsIDE.md) | [Next](./[5]-Customizing-The-JetBrains-Environment.md)
