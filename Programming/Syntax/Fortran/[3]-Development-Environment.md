[Previous](./[2]-Compiling-and-Running.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[4]-Program-Structure.md)

*Getting Started*

# Lesson 3 - Your Development Environment (editors, debuggers, warnings)

A good setup catches mistakes for you. This lesson covers editors with Fortran support, the debugger, the compiler warning flags that act like a second pair of eyes, and a project layout that keeps things organized as your code grows.

---

## 3.1 Choosing an Editor or IDE

Fortran source is plain text, so any text editor works. Editors with Fortran support add syntax highlighting, auto-completion, error underlining, and formatting.

| Editor | Fortran support | Good for |
|--------|-----------------|----------|
| **Visual Studio Code** | The *Modern Fortran* extension, which uses the `fortls` language server | Most beginners: free, cross-platform, easy setup |
| **Vim / Neovim** | Built-in highlighting plus `fortls` through an LSP plugin | Keyboard-driven work, remote servers |
| **Emacs** | Built-in `f90-mode`, optional `fortls` | Long-time Emacs users |
| **Geany / Code::Blocks** | Basic Fortran support | Lightweight IDE experience |
| **JetBrains CLion** | Fortran plugin | Mixed C/Fortran projects |

### Recommended setup: VS Code

1. Install [VS Code](https://code.visualstudio.com).
2. Install the **Modern Fortran** extension from the Extensions panel (search "Modern Fortran").
3. Install the language server `fortls`:
   ```bash
   pip install fortls
   ```
4. Optionally install the formatter `fprettify` (`pip install fprettify`) to automatically indent your code consistently.

With this you get hover documentation, jump-to-definition, and live warnings as you type.

> **Tip:** Make sure your files end in `.f90`. Editors choose the Fortran mode from the extension.

---

## 3.2 Setting Up a Debugger

A **debugger** lets you pause a running program, look at the values of variables, and step through one line at a time. It is far more effective than adding `print` statements everywhere.

### Compile for debugging

Debuggers need extra information in the executable. Add `-g` and turn optimization off with `-O0`, because optimized code is rearranged and does not match your source lines:

```bash
gfortran -g -O0 -fbacktrace myprog.f90 -o myprog
```

### GDB (Linux, Windows MSYS2)

`gdb` is the standard GNU debugger (install with `sudo apt install gdb`). A short session:

```bash
gdb ./myprog
```

| GDB command | Meaning |
|-------------|---------|
| `break 12` | Pause when line 12 is reached |
| `break main` | Pause at the start of the program |
| `run` | Start the program |
| `next` (or `n`) | Run the next line, stepping over procedure calls |
| `step` (or `s`) | Run the next line, stepping *into* procedure calls |
| `print x` | Show the value of variable `x` |
| `print a(1:3)` | Show part of an array |
| `continue` (or `c`) | Run until the next breakpoint |
| `backtrace` (or `bt`) | Show the chain of procedure calls that led here |
| `quit` | Leave GDB |

### LLDB (macOS)

On macOS use `lldb`, which comes with the Xcode command line tools. The ideas are the same; commands are similar (`b`, `run`, `n`, `s`, `p`, `bt`).

### Debugging inside VS Code

VS Code can drive these debuggers with graphical breakpoints. Install the *C/C++* extension (for GDB) or *CodeLLDB* (for LLDB), and then add a launch configuration pointing at your executable. Lesson 44 covers debugging in depth.

---

## 3.3 Compiler Warnings: `-Wall -Wextra -fcheck=all`

The compiler can warn about code that is *legal* but *suspicious*, and it can insert checks that stop the program the moment something goes wrong at runtime. The compiler does these things only if you ask.

### Compile-time warnings

| Flag | What it catches |
|------|-----------------|
| `-Wall` | Common problems: unused variables, variables used before being set, suspicious conversions |
| `-Wextra` | Further problems, such as unused dummy arguments |
| `-Wconversion` | Implicit conversions that may lose precision (for example double to single) |
| `-pedantic` or `-std=f2018` | Features that are not in the Fortran standard (compiler extensions) |
| `-Wimplicit-interface` | Calls to procedures without an explicit interface |

### Runtime checks

| Flag | What it does |
|------|--------------|
| `-fcheck=all` | Turns on every check: array bounds, unallocated arrays, mismatched array sizes, and more |
| `-fcheck=bounds` | Only array-bounds checking |
| `-fbacktrace` | Prints the call chain when the program crashes |
| `-ffpe-trap=invalid,zero,overflow` | Stops on floating-point errors such as division by zero or `NaN` creation |

### A worked example

This program contains two mistakes. Save it as `warn_demo.f90`:

```fortran
program warn_demo
    implicit none
    integer :: i, unused
    real :: a(3)

    do i = 1, 4
        a(i) = real(i)
    end do
    print *, a
end program warn_demo
```

Compile it with warnings:

```bash
gfortran -Wall -Wextra warn_demo.f90 -o warn_demo
```

The compiler reports both problems before the program ever runs:

```
Warning: Array reference at (1) out of bounds (4 > 3) in loop beginning at (2)
Warning: Unused variable 'unused' declared at (1) [-Wunused-variable]
```

Without those flags the program would compile silently. Writing past the end of `a` corrupts other memory, and it may appear to work, crash, or give wrong answers at random. This is one of the most dangerous bugs in Fortran.

Now compile with runtime checks and run:

```bash
gfortran -g -fcheck=all -fbacktrace warn_demo.f90 -o warn_demo
./warn_demo
```

```
At line 7 of file warn_demo.f90
Fortran runtime error: Index '4' of dimension 1 of array 'a' above upper bound of 3

Error termination. Backtrace:
...
```

The error names the exact line, the array, and the bad index.

> **Rule of thumb:** always develop with `-Wall -Wextra -fcheck=all -fbacktrace`. Treat every warning as something to understand and fix, not ignore.

---

## 3.4 A Recommended Project Layout

For anything bigger than a single file, give each kind of file its own place. The layout below is the one `fpm` expects, so you get it automatically with `fpm new`:

```
myproject/
├── app/            Main programs (the thing you run)
│   └── main.f90
├── src/            Modules and procedures (reusable code)
│   ├── constants.f90
│   └── geometry.f90
├── test/           Programs that check your code is correct
│   └── check.f90
├── data/           Input data files
├── docs/           Notes and documentation
├── fpm.toml        Project settings for fpm
└── README.md       What the project does and how to build it
```

Guidelines that scale well:

- **One module per file**, with the file named after the module (`geometry.f90` holds `module geometry`).
- Keep the main program short. It should read input, call procedures from `src/`, and write output.
- Keep source code and generated files separate. Build output (`build/`, `*.o`, `*.mod`) should be listed in `.gitignore` if you use Git.
- Put every program you can run on its own in `app/` or `test/` so `fpm` can find it.

---

[Previous](./[2]-Compiling-and-Running.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[4]-Program-Structure.md)