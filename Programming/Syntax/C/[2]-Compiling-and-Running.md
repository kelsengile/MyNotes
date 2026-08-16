[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[3]-Development-Environment.md)

*Getting Started*

# Lesson 2 - Compiling And Running

## 2.1 From Source Code to Executable

Turning a `.c` file into a program you can run happens in stages:

1. **Preprocessing** — handles `#include` and `#define` directives, producing expanded source code.
2. **Compilation** — translates the preprocessed code into assembly, then into an **object file** (`.o` or `.obj`) containing machine code.
3. **Linking** — combines one or more object files with any needed libraries into a final **executable**.

Compilers like `gcc` and `clang` run all three stages for you with a single command by default.

---

## 2.2 Compiling a Single File

Given a file `hello.c`:

```c
#include <stdio.h>

int main(void) {
    printf("Hello, C!\n");
    return 0;
}
```

Compile and run it:

```bash
gcc hello.c -o hello
./hello
```

- `-o hello` names the output executable `hello` (otherwise GCC defaults to `a.out`).
- `./hello` runs the program from the current directory.

On Windows with MSVC:

```bash
cl hello.c
hello.exe
```

---

## 2.3 The Linker and Multiple Files

Real projects span multiple `.c` files. Each is compiled to an object file, then linked together:

```bash
gcc -c math_utils.c -o math_utils.o
gcc -c main.c -o main.o
gcc math_utils.o main.o -o app
./app
```

The `-c` flag tells the compiler to stop after producing an object file, without linking. This lets you recompile only the files that changed, rather than the whole project — a big time-saver as projects grow.

---

## 2.4 Introduction to Makefiles

Typing out compile commands by hand gets tedious. A **Makefile** automates the process. Create a file named `Makefile`:

```makefile
app: main.o math_utils.o
	gcc main.o math_utils.o -o app

main.o: main.c
	gcc -c main.c

math_utils.o: math_utils.c
	gcc -c math_utils.c

clean:
	rm -f *.o app
```

Then just run:

```bash
make
```

`make` only rebuilds files that changed since the last build, based on file timestamps. Note that the indented lines under each target **must** use a literal tab character, not spaces — this is a common source of Makefile errors. Build systems are covered in depth in Lesson 24.

---

## 2.5 Common Compiler Flags

A few flags you'll use constantly:

| Flag | Purpose |
|---|---|
| `-o <name>` | Set the output file name |
| `-c` | Compile only, don't link |
| `-g` | Include debug information (for use with a debugger) |
| `-O2` | Enable optimizations |
| `-Wall -Wextra` | Enable helpful compiler warnings (see Lesson 3) |
| `-std=c17` | Target a specific C standard version |

Example combining several:

```bash
gcc -Wall -Wextra -g -std=c17 main.c -o main
```

---

[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[3]-Development-Environment.md)
