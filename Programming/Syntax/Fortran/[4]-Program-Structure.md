[Previous](./[3]-Development-Environment.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[5]-Variables-and-Data-Types.md)

*Core Syntax*

# Lesson 4 - Program Structure & `implicit none`

Every Fortran program follows the same overall shape. In this lesson you will learn the pieces of that shape, how to write comments and long lines, and why one short statement, `implicit none`, appears at the top of almost every program written today.

---

## 4.1 The `program` Unit

A Fortran source file is made of **program units**. The one that runs first is the **main program**, which has this structure:

```fortran
program name
    ! 1. Specification part: declarations
    ! 2. Execution part: statements that do the work
contains
    ! 3. Optional: internal procedures (Lesson 15)
end program name
```

The three parts must come in this order:

1. **Specification part** (declarations): `implicit none`, `use` statements, and variable declarations. You tell the compiler what exists.
2. **Execution part**: the statements that run, such as assignments, `print`, `if`, and `do`. You tell the computer what to do.
3. **Internal procedures** (optional): functions and subroutines placed after `contains`.

A complete example:

```fortran
program greeting
    implicit none

    ! --- specification part ---
    character(len=20) :: name
    integer :: year

    ! --- execution part ---
    name = "Ada"
    year = 1843
    print *, "Hello, ", name
    print *, "Published notes in", year
end program greeting
```

Output:

```
 Hello, Ada                 
 Published notes in        1843
```

Points to note:

- `program greeting` opens the unit and `end program greeting` closes it. Repeating the name after `end program` is optional but recommended, because it helps readers and lets the compiler check for mismatches.
- Declarations must come **before** any executable statement. Declaring a variable after a `print` is an error.
- The program name is not a variable and does not need to match the file name.
- The `stop` statement ends the program early. It is optional at the end, because reaching `end program` stops the program anyway.

### Other program units

The main program is one of several unit types. You will meet the others later:

| Unit | Purpose | Lesson |
|------|---------|--------|
| `program` | The entry point | this lesson |
| `module` | A collection of reusable definitions and procedures | 16 |
| `subroutine`, `function` | Reusable blocks of code | 15 |

---

## 4.2 Free-Form Source and Comments

Modern Fortran uses **free-form** source, meaning you can indent however you like and start in any column. (Old code used fixed-form with strict column rules. See Lesson 42.)

### Comments

A comment starts with an exclamation mark `!` and runs to the end of the line. The compiler ignores it:

```fortran
program comments_demo
    implicit none
    real :: speed   ! speed in metres per second

    ! A full-line comment
    speed = 9.8
    print *, speed
end program comments_demo
```

There is no block-comment syntax in Fortran. To comment out several lines, put `!` at the start of each. Editors usually have a shortcut for this.

An `!` inside a string is just a character, not a comment:

```fortran
print *, "Wow! This prints fully."
```

### Source layout rules

| Rule | Detail |
|------|--------|
| Maximum line length | 132 characters (the standard limit; `gfortran` reports an error for longer lines unless you use `-ffree-line-length-none`) |
| Maximum name length | 63 characters |
| Indentation | Free. Four spaces is the usual convention in this course. |
| Blank lines | Allowed anywhere and ignored |

---

## 4.3 Why `implicit none` Matters

Fortran has a surprising historical feature: if you use a variable name that you never declared, the compiler quietly creates it for you. This is called **implicit typing**. The rule is based on the first letter:

- Names starting with `i`, `j`, `k`, `l`, `m`, or `n` become `integer`.
- Every other name becomes `real`.

(This is the origin of the old joke "God is real, unless declared integer.")

That was convenient on punch cards, but it turns every typo into a hidden bug. Consider this program, which does **not** use `implicit none`:

```fortran
program risky
    real :: velocity
    velocity = 12.0
    print *, "Velocity is", velocty    ! typo: 'velocty'
end program risky
```

It compiles without complaint. The typo `velocty` becomes a brand-new real variable that was never given a value, so the program prints garbage (or zero) instead of 12.0.

Now add `implicit none`:

```fortran
program safe
    implicit none
    real :: velocity
    velocity = 12.0
    print *, "Velocity is", velocty    ! typo
end program safe
```

The compiler stops at once:

```
Error: Symbol 'velocty' at (1) has no IMPLICIT type
```

`implicit none` switches implicit typing off, so every variable must be declared and every misspelling is caught at compile time.

> **Rule:** put `implicit none` in every program, module, and (for standalone code) every procedure, directly after any `use` statements and before the declarations. This course always does.

When you put `implicit none` in a module, it covers all procedures inside that module, so you do not repeat it in each one (Lesson 16).

---

## 4.4 Statements, Continuation Lines, and Case Insensitivity

### One statement per line

Normally each line holds one statement. You may place several statements on one line by separating them with a semicolon `;`, though this is best used sparingly:

```fortran
a = 1; b = 2; c = a + b
```

### Continuation lines

When a statement is too long to read comfortably, finish the line with an ampersand `&` and continue on the next line:

```fortran
program continuation_demo
    implicit none
    real :: total

    total = 1.5 + 2.5 + 3.5 &
          + 4.5 + 5.5 &
          + 6.5
    print *, "Total:", total
end program continuation_demo
```

Output:

```
 Total:   24.0000000    
```

The standard allows up to 255 continuation lines for one statement.

Long strings can also be split. Put a second `&` at the start of the next line so the string resumes exactly where you want it to:

```fortran
print *, "This is a very long message that we split &
         &across two lines."
```

### Case insensitivity

Fortran ignores upper and lower case in names and keywords. These lines are identical to the compiler:

```fortran
program CaseDemo
    implicit none
    integer :: Count
    COUNT = 5
    Print *, count
end program casedemo
```

Output:

```
           5
```

`Count`, `COUNT`, and `count` are the same variable. Case **does** matter inside character strings: `"Hello"` and `"hello"` are different text.

For readability, most modern code uses lowercase for everything, which is the style used in this course.

### Summary of the rules

| Feature | Rule |
|---------|------|
| Comment | `!` to end of line |
| Continue a line | End the line with `&` |
| Several statements on a line | Separate with `;` |
| Case | Ignored in code, preserved in strings |
| Names | Start with a letter, then letters, digits, or `_`; at most 63 characters |

---

[Previous](./[3]-Development-Environment.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[5]-Variables-and-Data-Types.md)