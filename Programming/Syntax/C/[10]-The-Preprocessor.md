[Previous](./[9]-Functions-and-Scope.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[11]-Pointers-Fundamentals.md)

*Core Syntax*

# Lesson 10 - The Preprocessor

## 10.1 What Is the Preprocessor?

Before your code is compiled, it passes through the **preprocessor** — a text-substitution step that handles any line starting with `#`. It doesn't understand C syntax; it just performs textual find-and-replace and file inclusion. You can see the result of this step yourself:

```bash
gcc -E main.c
```

This prints the fully expanded source code, right before it's handed to the actual compiler.

---

## 10.2 #include

`#include` pastes the contents of another file directly into the current one:

```c
#include <stdio.h>     // angle brackets: search system/standard library paths
#include "my_header.h" // quotes: search the current directory first
```

Angle brackets (`<...>`) are used for standard library and system headers; double quotes (`"..."`) are used for your own project's headers. This distinction matters more once you're organizing multi-file projects, covered starting in Lesson 22.

---

## 10.3 #define and Macros

`#define` creates a **macro** — a name that the preprocessor replaces with something else, everywhere it appears:

```c
#define MAX_SCORE 100

if (score > MAX_SCORE) { ... }
// after preprocessing becomes:
if (score > 100) { ... }
```

Macros can also take arguments, functioning like a simple function, but with pure text substitution instead of a real function call:

```c
#define SQUARE(x) ((x) * (x))

int result = SQUARE(5);   // becomes ((5) * (5)) -> 25
```

---

## 10.4 Conditional Compilation

`#ifdef`, `#ifndef`, `#if`, `#else`, and `#endif` let you include or exclude code before compilation even happens — commonly used for platform-specific code or debug-only logic:

```c
#include <stdio.h>

#define DEBUG 1

int main(void) {
#if DEBUG
    printf("Debug mode is on\n");
#endif
    printf("Program running\n");
    return 0;
}
```

Platform-specific example:

```c
#ifdef _WIN32
    #include <windows.h>
#else
    #include <unistd.h>
#endif
```

`#ifndef` combined with `#define` is the classic pattern for **include guards**, which prevent a header file from being included more than once in the same translation unit — covered fully in Lesson 22.

---

## 10.5 Common Pitfalls with Macros

Because macros are pure text substitution, they can behave surprisingly. Always parenthesize macro parameters and the whole expansion:

```c
#define BAD_SQUARE(x) x * x

int result = BAD_SQUARE(2 + 3);
// expands to: 2 + 3 * 2 + 3  =  11, NOT 25!

#define GOOD_SQUARE(x) ((x) * (x))

int result2 = GOOD_SQUARE(2 + 3);
// expands to: ((2 + 3) * (2 + 3)) = 25, correct
```

Macros also have no concept of type-checking or scope the way functions do, and can make error messages confusing since the compiler sees only the expanded text. For anything beyond simple constants, prefer a real function or a `const` variable — reserve macros for cases where compile-time text substitution or conditional compilation is genuinely needed.

---

[Previous](./[9]-Functions-and-Scope.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[11]-Pointers-Fundamentals.md)
