[Previous](./[2]-Compiling-and-Running.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[4]-CPP-Standards.md)

*Getting Started*

# Lesson 3 - Development Environment

## 3.1 Choosing An Editor/IDE

You can write C++ in any text editor, but a good setup saves time. Popular choices:

- **Visual Studio Code** — lightweight, cross-platform, with the **C/C++** and **CMake Tools** extensions providing autocomplete, debugging, and build integration.
- **Visual Studio** (Windows) — a full IDE tightly integrated with MSVC, with a powerful built-in debugger.
- **CLion** — a cross-platform C++ IDE with deep CMake integration and refactoring tools.
- **Vim/Neovim + clangd** — for developers who prefer terminal-based workflows with language-server-powered autocomplete.

Whichever you choose, make sure it can find your installed compiler so it can offer accurate autocomplete and error checking.

---

## 3.2 Useful Compiler Flags

Compiler flags change how your code is built. A few you'll use constantly:

| Flag | Purpose |
|---|---|
| `-Wall -Wextra` | Enable most useful warnings |
| `-g` | Include debug symbols for use with a debugger |
| `-O0`, `-O2` | Optimization level (`-O0` = none, best for debugging; `-O2` = typical release optimization) |
| `-std=c++20` | Select the C++ standard to compile against |

Example:

```bash
g++ -Wall -Wextra -g -std=c++20 main.cpp -o main
```

Turning on warnings early catches many bugs — like uninitialized variables or comparing signed and unsigned numbers — before they become runtime problems.

---

## 3.3 Debugging Basics (gdb/lldb)

A **debugger** lets you pause a running program, inspect variables, and step through code line by line.

- **GDB** (GNU Debugger) — used with GCC-built programs on Linux.
- **LLDB** — used with Clang, common on macOS.
- Both **Visual Studio** and **VS Code** wrap these tools in a graphical interface.

Basic GDB workflow:

```bash
g++ -g main.cpp -o main
gdb ./main
```

Inside GDB:

```
break main       # set a breakpoint at main()
run              # start the program
next             # step to the next line
print myVariable # inspect a variable's value
continue         # resume until the next breakpoint
```

---

## 3.4 Project Layout Conventions

As projects grow, a consistent folder structure keeps things manageable. A common layout:

```
my-project/
├── src/          # .cpp source files
├── include/      # .h / .hpp header files
├── build/        # generated build output (not committed to version control)
├── tests/        # unit tests
└── CMakeLists.txt
```

Keeping headers and source files separate, and generated build artifacts out of version control, makes projects easier to navigate and share with collaborators.

[Previous](./[2]-Compiling-and-Running.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[4]-CPP-Standards.md)
