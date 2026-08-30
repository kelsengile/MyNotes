[Previous](./[4]-Setting-Up-Your-Environment.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[6]-Control-Flow.md)

# Lesson 5 - Core Syntax & Basics

Every programming language has its own specific syntax, but underneath the surface-level differences, a common set of fundamental building blocks shows up almost everywhere. This section covers those universal basics.

## 5.1 Variables and Data Types

A **variable** is a named container for storing a value that a program can reference and change. Instead of writing literal values repeatedly, you give a piece of data a name and refer to it by that name.

```python
age = 25
name = "Alice"
is_student = True
```

### Common Data Types
Most languages provide some version of these fundamental types:
- **Integer (int)** — whole numbers, e.g., `42`, `-7`
- **Floating-point (float/double)** — numbers with decimal points, e.g., `3.14`, `-0.5`
- **String (str)** — text, e.g., `"Hello, world"`
- **Boolean (bool)** — one of two values: `true` or `false`
- **None/Null** — represents "no value" or the absence of a value

### Composite / Structured Types
Beyond these basics, most languages also offer ways to group multiple values together:
- **Array/List** — an ordered collection of values, e.g., `[1, 2, 3]`
- **Dictionary/Map/Object** — a collection of key-value pairs, e.g., `{"name": "Alice", "age": 25}`
- **Tuple** — a fixed-size, ordered collection, often immutable

### Naming Variables
Most languages share similar rules and conventions:
- Names typically can't start with a number or contain spaces
- Descriptive names (`total_price` rather than `x`) make code easier to read and maintain
- Common naming conventions include `camelCase` (JavaScript, Java), `snake_case` (Python, Ruby), and `PascalCase` (often for classes/types across many languages)

---

## 5.2 Type Systems (Static vs. Dynamic, Strong vs. Weak)

A language's **type system** governs how it handles the data types of variables — when types are checked, and how strictly they're enforced. There are two related but distinct spectrums here.

### Static vs. Dynamic Typing
This is about **when** type checking happens:
- **Statically typed** languages (e.g., Java, C++, Rust, TypeScript) check types *before* the program runs (at compile time). Variables are typically declared with an explicit type, and mismatches are caught early.
  ```java
  int age = 25; // must always hold an integer
  ```
- **Dynamically typed** languages (e.g., Python, JavaScript, Ruby) check types *while* the program runs. A variable can be reassigned to a different type entirely, and errors related to type mismatches only surface at runtime.
  ```python
  age = 25       # age is an int
  age = "twenty" # perfectly legal — age is now a string
  ```

**Trade-offs:** Static typing tends to catch certain classes of bugs earlier and can make large codebases easier to navigate and refactor safely. Dynamic typing tends to allow faster, more flexible prototyping, at the cost of some errors only appearing at runtime.

### Strong vs. Weak Typing
This is about **how strictly** a language enforces type rules, especially around implicit conversions between types.
- **Strongly typed** languages are strict about mixing types without explicit conversion (e.g., Python will raise an error if you try to add a string and an integer directly).
- **Weakly typed** languages are more permissive, automatically converting between types in ways that can sometimes produce surprising results (e.g., JavaScript's `"5" + 1` produces the string `"51"`, while `"5" - 1` produces the number `4`).

These two spectrums are independent — a language can be, for example, dynamically *and* strongly typed (Python), or dynamically *and* weakly typed (JavaScript), or statically *and* strongly typed (Java, Rust).

---

## 5.3 Operators (Arithmetic, Comparison, Logical, Bitwise)

Operators are symbols that perform operations on values (called **operands**).

### Arithmetic Operators
Perform mathematical calculations:
| Operator | Meaning | Example |
|---|---|---|
| `+` | Addition | `5 + 3` → `8` |
| `-` | Subtraction | `5 - 3` → `2` |
| `*` | Multiplication | `5 * 3` → `15` |
| `/` | Division | `5 / 2` → `2.5` |
| `%` | Modulo (remainder) | `5 % 2` → `1` |
| `**` / `^` | Exponentiation | `2 ** 3` → `8` |

### Comparison Operators
Compare two values, producing a boolean result:
| Operator | Meaning |
|---|---|
| `==` | Equal to |
| `!=` | Not equal to |
| `>` | Greater than |
| `<` | Less than |
| `>=` | Greater than or equal to |
| `<=` | Less than or equal to |

Note: many languages distinguish between `==` (equality, sometimes with type conversion) and `===` (strict equality, no type conversion) — JavaScript is a well-known example of this distinction.

### Logical Operators
Combine or invert boolean values:
| Operator | Meaning | Example |
|---|---|---|
| `and` / `&&` | True if both operands are true | `true and false` → `false` |
| `or` / `\|\|` | True if at least one operand is true | `true or false` → `true` |
| `not` / `!` | Inverts a boolean | `not true` → `false` |

### Bitwise Operators
Operate directly on the individual bits of a number's binary representation — used less often in everyday application code, but important in systems programming, performance-critical code, and working with flags/masks:
| Operator | Meaning |
|---|---|
| `&` | AND — bit is 1 only if both bits are 1 |
| `\|` | OR — bit is 1 if at least one bit is 1 |
| `^` | XOR — bit is 1 if bits differ |
| `~` | NOT — flips all bits |
| `<<` | Left shift — shifts bits left, effectively multiplying by 2 per shift |
| `>>` | Right shift — shifts bits right, effectively dividing by 2 per shift |

---

## 5.4 Input and Output

**Input/Output (I/O)** refers to how a program receives data from the outside world and how it sends data back out.

### Output
The most basic form of output is printing to the console/terminal:
```python
print("Hello, world!")
```
```javascript
console.log("Hello, world!");
```

### Input
Programs often need to receive data — from a user typing at the keyboard, from a file, from a network request, or from another program:
```python
name = input("What's your name? ")
print("Hello, " + name)
```

Beyond simple console I/O, programs commonly handle:
- **File I/O** — reading from and writing to files on disk
- **Network I/O** — sending and receiving data over the internet (e.g., API requests)
- **Standard streams** — `stdin` (input), `stdout` (normal output), and `stderr` (error output), a convention inherited from Unix that many languages and command-line tools still follow

---

## 5.5 Comments and Code Style

### Comments
**Comments** are lines in source code that are ignored by the compiler/interpreter — they exist purely for humans reading the code.
```python
# This is a single-line comment in Python
"""
This is a
multi-line comment/docstring in Python
"""
```
```javascript
// This is a single-line comment in JavaScript
/*
This is a
multi-line comment in JavaScript
*/
```

Good comments explain **why** something is done a certain way, especially when the reasoning isn't obvious from the code itself — rather than simply restating **what** the code does line-by-line, which is often already clear from reading it.

### Code Style
**Code style** refers to the conventions around formatting and structuring code — indentation, spacing, naming, line length, and so on. Consistent style makes code significantly easier to read, especially in a team setting.

Many languages and communities have established style guides:
- Python: **PEP 8**
- JavaScript: various (Airbnb style guide, StandardJS, Prettier defaults)
- Google publishes style guides for several languages

Tools called **linters** and **formatters** (e.g., ESLint, Prettier, Black) automatically check or enforce style rules, removing debates over formatting from day-to-day development and keeping a codebase visually consistent regardless of who wrote which part.

### Why This Matters
Code is read far more often than it's written — by teammates, by future maintainers, and by your own future self returning to old code. Clear naming, thoughtful comments, and consistent style aren't cosmetic extras; they directly affect how quickly a codebase can be understood, debugged, and safely modified.

[Previous](./[4]-Setting-Up-Your-Environment.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[6]-Control-Flow.md)
