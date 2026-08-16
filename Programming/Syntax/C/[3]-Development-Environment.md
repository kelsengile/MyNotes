[Previous](./[2]-Compiling-and-Running.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[4]-Variables-and-Data-Types.md)

*Getting Started*

# Lesson 3 - Your Development Environment

## 3.1 Choosing an Editor or IDE

You don't need a heavyweight IDE to write C, but good tooling catches mistakes early. Popular choices:

- **VS Code** — lightweight, with the "C/C++" extension providing syntax highlighting, autocomplete, and inline error checking.
- **CLion** — a full C/C++ IDE with built-in project and build management.
- **Vim / Neovim** — highly customizable, popular for terminal-based workflows, especially combined with a language server (`clangd`).
- **Visual Studio** — the natural choice if you're already using MSVC on Windows.

Whichever you choose, make sure it can run your compiler and show you build errors inline — that feedback loop is what actually speeds up learning.

---

## 3.2 Setting Up a Debugger

A debugger lets you pause a running program, inspect variables, and step through code line by line — far more effective than scattering `printf` calls everywhere.

- **GDB** (GNU Debugger) pairs with GCC on Linux.
- **LLDB** pairs with Clang, and is the default debugger on macOS.
- **Visual Studio's debugger** integrates directly with MSVC on Windows.

To debug a program, compile with `-g` to include debug symbols, then launch it under the debugger:

```bash
gcc -g main.c -o main
gdb ./main
```

Debugging in depth is covered in Lesson 47.

---

## 3.3 Compiler Warnings: -Wall and -Wextra

C lets you make mistakes that many other languages would refuse to compile — reading an uninitialized variable, comparing mismatched types, and more. Compiler warnings catch a large fraction of these before you even run the program.

```bash
gcc -Wall -Wextra -o main main.c
```

- `-Wall` enables a broad set of common, useful warnings.
- `-Wextra` enables additional warnings not covered by `-Wall`.

Consider this buggy code:

```c
int add(int a, int b) {
    int result;
    return result;  // using result before assigning it!
}
```

With `-Wall`, GCC will warn: `'result' is used uninitialized in this function`. Get in the habit of compiling with these flags from your very first program — treat every warning as a bug to fix, not something to ignore.

---

## 3.4 A Recommended Project Layout

Even small C projects benefit from a consistent folder structure:

```
my_project/
├── src/          # .c source files
├── include/      # .h header files
├── build/        # compiled object files and executables (often git-ignored)
├── Makefile
└── README.md
```

Keeping headers and source separated, and build artifacts out of version control, makes projects easier to navigate as they grow — a pattern you'll use starting with the multi-file projects in Lesson 22 onward.

---

[Previous](./[2]-Compiling-and-Running.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[4]-Variables-and-Data-Types.md)
