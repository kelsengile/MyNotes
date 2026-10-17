[Previous](./[4]-Program-Structure.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[6]-Kinds-and-Precision.md)

*Core Syntax*

# Lesson 5 - Variables & Basic Data Types

Programs work by storing, changing, and combining data. This lesson introduces variables, the five built-in data types of Fortran, how to declare them with starting values, how to make constants that cannot change, and how to choose good names.

---

## 5.1 What Is a Variable?

A **variable** is a named location in the computer's memory that holds a value. You can picture it as a labeled box:

```
   age            temperature        city
 ┌──────┐        ┌──────────┐      ┌────────────┐
 │  25  │        │   21.5   │      │  "Manila"  │
 └──────┘        └──────────┘      └────────────┘
 integer             real          character(len=12)
```

Every variable in Fortran has:

- a **name** you use to refer to it,
- a **type** that says what kind of data it holds (whole number, decimal number, text, and so on),
- a **value** that can change as the program runs (unless it is a constant, see 5.4).

Because Fortran is **statically typed**, the type is fixed when you declare the variable. An `integer` variable cannot later hold text.

### Assignment

The `=` sign in Fortran means **assignment**: calculate the right-hand side, then store the result in the variable on the left.

```fortran
count = 10          ! store 10 in count
count = count + 1   ! take count (10), add 1, store 11 back in count
```

The second line would be nonsense as a mathematical equation, but it is a normal and very common assignment. (Testing for equality uses `==`, covered in Lesson 7.)

---

## 5.2 `integer`, `real`, `complex`, `logical`, `character`

Fortran has five intrinsic (built-in) data types.

| Type | Holds | Example values |
|------|-------|----------------|
| `integer` | Whole numbers | `-7`, `0`, `42` |
| `real` | Numbers with a fractional part | `3.14`, `-0.001`, `2.5e6` |
| `complex` | A real part plus an imaginary part | `(3.0, 4.0)` means 3 + 4i |
| `logical` | Truth values | `.true.`, `.false.` |
| `character` | Text | `"Hello"`, `'Fortran'` |

### `integer`

Whole numbers with no decimal point. The default `integer` uses 4 bytes and can hold values from -2,147,483,648 to 2,147,483,647 on all common compilers. Going beyond that range **overflows** silently (Lesson 6 shows how to get larger integers).

### `real`

Numbers with a decimal point, stored in floating-point form. The default `real` takes 4 bytes and gives roughly 7 significant decimal digits. Literal values can be written in plain or scientific notation:

```fortran
x = 3.5
y = 6.02e23      ! 6.02 x 10^23
z = 1.0e-9       ! 0.000000001
```

Always write a decimal point in real literals. In `x = 1/3` the compiler does integer arithmetic and gives 0; write `1.0/3.0` instead (more in Lesson 7).

### `complex`

A pair of real numbers, written in parentheses: `(real part, imaginary part)`.

```fortran
z = (3.0, 4.0)
print *, abs(z)      ! magnitude: 5.0
print *, z%re        ! real part (Fortran 2008)
print *, z%im        ! imaginary part
print *, conjg(z)    ! complex conjugate: (3.0, -4.0)
```

Arithmetic with `+`, `-`, `*`, `/`, `**` works directly on complex numbers, which is a major reason Fortran is popular in engineering and physics.

### `logical`

Only two values exist: `.true.` and `.false.` (the dots are part of the syntax). They are the result of comparisons and the input to `if` statements:

```fortran
is_hot = temperature > 30.0
```

### `character`

Text. You choose the length with `len=`. Strings can be written with either double quotes `"..."` or single quotes `'...'`. To put a quote character inside a string, either use the other kind of quote or double the quote:

```fortran
msg1 = "It's fine"
msg2 = 'It''s fine'
```

Lesson 14 covers strings in depth.

### All five together

```fortran
program types_demo
    implicit none
    integer :: count
    real :: temperature
    complex :: z
    logical :: is_hot
    character(len=12) :: city

    count = 3
    temperature = 31.5
    z = (3.0, 4.0)
    is_hot = temperature > 30.0
    city = "Manila"

    print *, "count       =", count
    print *, "temperature =", temperature
    print *, "z           =", z
    print *, "|z|         =", abs(z)
    print *, "real part   =", z%re
    print *, "is_hot      =", is_hot
    print *, "city        =", city
end program types_demo
```

Output:

```
 count       =           3
 temperature =   31.5000000    
 z           =             (3.00000000,4.00000000)
 |z|         =   5.00000000    
 real part   =   3.00000000    
 is_hot      = T
 city        =Manila      
```

Logical values print as `T` or `F`.

---

## 5.3 Declaring and Initializing Variables

A declaration has the form:

```
type [, attributes] :: name [= initial value] [, name2 ...]
```

The double colon `::` separates the type information from the names. It is optional in the simplest form but always recommended, and required whenever you add attributes or an initial value.

```fortran
integer :: i, j, k              ! three integers
real :: x, y                    ! two reals
real :: a = 1.5, b = 2.5        ! declared and given starting values
character(len=20) :: name = "Fortran"
logical :: done = .false.
```

### Attributes

Attributes go between the type and `::`, separated by commas. You will meet many of them in later lessons:

| Attribute | Meaning |
|-----------|---------|
| `parameter` | A named constant (see 5.4) |
| `dimension(n)` | An array (Lesson 11) |
| `allocatable` | An array whose size is chosen at run time (Lesson 13) |
| `intent(in)` | A procedure argument that is only read (Lesson 15) |
| `save` | Keeps its value between procedure calls (Lesson 18) |

### Uninitialized variables

A variable that has been declared but never given a value holds **whatever was left in memory**. It is not automatically zero. Reading it is a bug:

```fortran
integer :: total
print *, total          ! unpredictable value!
```

Always give a variable a value before you use it, either with an initializer in the declaration or with an assignment. Compiling with `-Wall` helps catch this.

### Initialization happens once

An initial value in a declaration (`integer :: n = 0`) sets the value one time, before the program starts running. Inside procedures, it also gives the variable the `save` attribute automatically, so it is **not** reset on each call. This surprises many newcomers, and Lesson 18 explains it. When you want a variable reset at each call, assign it in the executable part instead.

---

## 5.4 Constants with `parameter`

A **named constant** has a value that is fixed when the program is compiled and can never change. Use the `parameter` attribute:

```fortran
program constants_demo
    implicit none
    real, parameter :: pi = 3.14159265
    real, parameter :: gravity = 9.81
    integer, parameter :: max_students = 30
    character(len=*), parameter :: course = "Intro to Fortran"
    real :: radius, area

    radius = 2.0
    area = pi * radius**2

    print *, "Course: ", course
    print *, "Area:   ", area
    print *, "Seats:  ", max_students
end program constants_demo
```

Output:

```
 Course: Intro to Fortran
 Area:      12.5663710    
 Seats:            30
```

Details:

- A parameter **must** be given a value in its declaration.
- Trying to change it, as in `pi = 3.0`, is a compile-time error.
- `character(len=*), parameter` lets the compiler work out the string length from the value.
- Named constants can be used where the compiler needs a value at compile time, such as an array size: `real :: grid(max_students)`.
- Why not just use a variable? A `parameter` documents intent, prevents accidental changes, and lets the compiler optimize.

---

## 5.5 Naming Conventions

### Rules (enforced by the compiler)

- Start with a **letter**.
- Continue with letters, digits, or underscores `_`.
- Use at most **63 characters**.
- No spaces, hyphens, or other symbols. `total-cost` would be read as `total` minus `cost`.
- Upper and lower case are **not** distinguished (`Total` and `total` are the same name).
- You cannot give two different things the same name in the same scope.

Fortran has no reserved words. You *could* name a variable `real` or `program`, but this is confusing and you should avoid it.

### Style (agreed by community convention)

| Guideline | Good | Poor |
|-----------|------|------|
| Use descriptive names | `average_speed` | `as1` |
| Use lowercase with underscores (`snake_case`) | `max_iterations` | `MaxIterations` |
| Short names are fine for short-lived loop counters and math symbols | `i`, `j`, `x`, `dt` | |
| Name constants clearly; some people use uppercase | `pi`, `MAX_SIZE` | `c1` |
| Avoid names that look alike | `total_a`, `total_b` | `l`, `O`, `I` |
| Do not name a variable after an intrinsic function | `total` | `sum`, `max`, `size` |

The last rule matters: naming a variable `sum` hides the intrinsic function `sum()` in that scope, causing confusing errors later.

---

[Previous](./[4]-Program-Structure.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[6]-Kinds-and-Precision.md)