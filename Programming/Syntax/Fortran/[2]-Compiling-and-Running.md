[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[3]-Development-Environment.md)

*Getting Started*

# Lesson 2 - Compiling & Running: Compilers, Modules & `fpm`

Writing code is only half the job. This lesson shows what the compiler actually does, how to compile one file or many, why Fortran creates extra `.mod` files, how `fpm` automates all of it, and which compiler flags you will use most.

---

## 2.1 From Source Code to Executable

Turning `.f90` text into a running program takes several stages. `gfortran` hides them, but understanding them makes error messages much easier to read.

```
 source (.f90)
      │  1. Preprocess   (only for .F90 files: handles #ifdef, #define)
      ▼
 compile  ──►  object file (.o)   +   module file (.mod)
      │  2. Compile each source file separately
      ▼
 link     ──►  executable
         3. The linker joins object files and libraries into one program
```

- **Compiling** checks your syntax and translates each file to an *object file* (`.o`, or `.obj` on Windows). Syntax errors appear here.
- **Linking** joins the object files with any libraries. Errors such as `undefined reference to ...` appear here and usually mean a file or library is missing from the command.
- A **module** (Lesson 16) also produces a `.mod` file, which describes what the module contains so other files can use it.

### File extensions matter

| Extension | Meaning |
|-----------|---------|
| `.f90` | Free-form source (the modern style, used in this course). The "90" is historical and applies to all modern standards. |
| `.F90` | Free-form source that is first run through the C preprocessor. |
| `.f`, `.for`, `.f77` | Fixed-form legacy source (see Lesson 42). |

---

## 2.2 Compiling a Single File

Take this program, saved as `circle.f90`:

```fortran
program circle
    implicit none
    real :: radius, area
    real, parameter :: pi = 3.14159265

    radius = 2.5
    area = pi * radius**2
    print *, "Radius:", radius
    print *, "Area:  ", area
end program circle
```

Compile and run it:

```bash
gfortran circle.f90 -o circle
./circle
```

Output:

```
 Radius:   2.50000000    
 Area:     19.6349545    
```

- `gfortran circle.f90` compiles and links in one step.
- `-o circle` names the executable. Without it, `gfortran` produces `a.out` (Linux/macOS) or `a.exe` (Windows).

You can also perform the two stages separately:

```bash
gfortran -c circle.f90          # produces circle.o only
gfortran circle.o -o circle     # links it into an executable
```

---

## 2.3 Compiling Multiple Files and `.mod` Files

Larger programs are split across several files. Here are two files. First `mathutils.f90`, which holds a module:

```fortran
module mathutils
    implicit none
contains
    function square(x) result(y)
        real, intent(in) :: x
        real :: y
        y = x * x
    end function square
end module mathutils
```

And `main.f90`, which uses it:

```fortran
program main
    use mathutils
    implicit none
    print *, "5 squared is", square(5.0)
end program main
```

(Modules are explained fully in [Lesson 16](./[16]-Modules.md). For now, think of one as a toolbox other files can open with `use`.)

### Order matters

When the compiler reaches `use mathutils` it needs `mathutils.mod`, and that file only exists after the module has been compiled. So the module file must be compiled **first**:

```bash
gfortran -c mathutils.f90      # creates mathutils.o and mathutils.mod
gfortran -c main.f90           # reads mathutils.mod, creates main.o
gfortran mathutils.o main.o -o app
./app
```

Output:

```
 5 squared is   25.0000000    
```

For a short project you can do it all in one command, as long as the module file is listed first:

```bash
gfortran mathutils.f90 main.f90 -o app
```

If you list `main.f90` first, you will see an error like `Cannot open module file 'mathutils.mod'`.

### Keeping `.mod` files tidy

By default `.mod` files land in the current folder. Use `-J` to choose where they are written and `-I` to tell the compiler where to look for them:

```bash
mkdir build
gfortran -J build -c mathutils.f90 -o build/mathutils.o
gfortran -I build -c main.f90 -o build/main.o
gfortran build/mathutils.o build/main.o -o app
```

> **Important:** `.mod` files are compiler-specific *and* version-specific. A `.mod` created by `gfortran` cannot be read by `ifx`, and one from an old `gfortran` may not work in a newer one. Never share them; always recompile from source.

---

## 2.4 Using `fpm` Projects

Typing compile commands by hand gets tedious. `fpm` finds your source files, works out the correct order, and runs the compiler for you.

### Creating a project

```bash
fpm new myproject
cd myproject
```

This creates the following layout:

```
myproject/
├── app/
│   └── main.f90        <- the main program
├── src/
│   └── myproject.f90   <- a module with library code
├── test/
│   └── check.f90       <- a test program
└── fpm.toml            <- project settings
```

The generated `fpm.toml` looks like this (it also includes your name and email if available):

```toml
name = "myproject"
version = "0.1.0"
license = "license"
```

### The commands you need

| Command | What it does |
|---------|--------------|
| `fpm build` | Compiles everything |
| `fpm run` | Builds (if needed) and runs the program in `app/` |
| `fpm test` | Builds and runs programs in `test/` |
| `fpm clean` | Deletes build files |
| `fpm run --profile release` | Builds with optimization turned on |

Try it:

```bash
fpm run
```

The first run compiles the project and then prints a short greeting from the template code. The exact wording depends on your `fpm` version.

### Choosing the compiler and flags

`fpm` uses `gfortran` unless told otherwise:

```bash
fpm build --compiler ifx
fpm build --flag "-Wall -Wextra"
```

You can also set the compiler through the `FPM_FC` environment variable.

### Adding dependencies

`fpm` can download libraries straight from Git. Add them to `fpm.toml`:

```toml
[dependencies]
stdlib = { git = "https://github.com/fortran-lang/stdlib" }
```

The next `fpm build` fetches and compiles them automatically. This is one of the biggest advantages over compiling by hand.

---

## 2.5 Common Compiler Flags

Flags are options that change how the compiler behaves. The table lists those you will use constantly with `gfortran`.

| Flag | Purpose |
|------|---------|
| `-o name` | Name of the output file |
| `-c` | Compile only, do not link |
| `-g` | Include debugging information |
| `-O0` | No optimization (fastest compile, best for debugging) |
| `-O2` | Standard optimization (good default for release builds) |
| `-O3` | Aggressive optimization |
| `-march=native` | Optimize for the exact CPU you are on (the result may not run on other machines) |
| `-Wall` | Enable a broad set of useful warnings |
| `-Wextra` | Enable additional warnings |
| `-std=f2018` | Reject anything that is not standard Fortran 2018 (also `f2008`, `f2003`) |
| `-fcheck=all` | Add runtime checks (array bounds, uninitialized pointers, and more) |
| `-fimplicit-none` | Behave as if `implicit none` were in every unit |
| `-ffree-line-length-none` | Allow source lines longer than 132 characters |
| `-J dir` | Write `.mod` files to `dir` |
| `-I dir` | Search `dir` for `.mod` and include files |
| `-fopenmp` | Enable OpenMP parallelism (Lesson 36) |
| `-l name`, `-L dir` | Link a library (e.g. `-llapack`) and say where to find it |

Two recommended combinations:

```bash
# While developing: catch mistakes early
gfortran -g -O0 -Wall -Wextra -std=f2018 -fcheck=all -fbacktrace prog.f90 -o prog

# For real calculations: fast
gfortran -O2 -march=native prog.f90 -o prog
```

The `-fcheck=all` flag slows a program noticeably, so use it while developing and testing, and drop it for production runs. Lesson 3 explains the warning flags in more detail.

The complete flag list is in the [GNU Fortran manual](https://gcc.gnu.org/onlinedocs/gfortran/Invoking-GNU-Fortran.html).

---

[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[3]-Development-Environment.md)