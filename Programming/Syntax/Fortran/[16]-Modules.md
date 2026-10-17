[Previous](./[15]-Functions-and-Subroutines.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[17]-Recursion-and-Pure-Procedures.md)

*Procedures & Modules*

# Lesson 16 - Modules & `use` Statements

Internal procedures (Lesson 15) are fine for small programs, but they can only be used by the program that contains them. As soon as you want to share code between programs, or split a project across several files, you need **modules**. A module is a named container for procedures, constants, variables, and (later) types. This lesson shows how to write one, control what it exposes, bring its contents into another unit with `use`, and understand why modules give you the safety of explicit interfaces.

---

## 16.1 Why Modules?

A module groups related code under one name, so other program units can borrow it:

```
        ┌───────────── module geometry ─────────────┐
        │  constants:   pi                          │
        │  procedures:  circle_area, rectangle_area │
        └───────────────────┬───────────────────────┘
                            │  use geometry
          ┌─────────────────┼─────────────────┐
     program main      program test      another module
```

Modules are the standard way to organize modern Fortran. They bring several benefits:

- **Reuse.** Write a procedure once and use it from any number of programs.
- **Explicit interfaces.** The compiler knows exactly what arguments every module procedure expects, and checks each call (16.4). This catches a whole family of bugs.
- **Information hiding.** You decide which names are visible from outside and which stay private (16.2).
- **Shared data.** Constants and variables declared in a module are available to everyone who uses it, which replaces the old `common` blocks (Lesson 42).
- **Namespaces.** `use ... only` and renaming (16.3) let you avoid name clashes.

### The shape of a module

```fortran
module name
    ! 1. use statements
    implicit none
    ! 2. declarations: constants, variables, (types)
contains
    ! 3. procedures: subroutines and functions
end module name
```

It looks like a main program, except that it starts with `module`, has no executable statements before `contains`, and cannot be run by itself. A single `implicit none` at the top covers **every** procedure in the module, so you do not repeat it inside each one (Lesson 4.3).

### Practical habits

- Put **one module per file**, and name the file after the module: `geometry.f90` holds `module geometry`.
- Compile modules **before** the files that use them, because compiling a module creates a `.mod` file that the other files read (Lesson 2.3). Tools such as `fpm` work out the order automatically.
- A common first module holds the project's kind constants (Lesson 6.3).

---

## 16.2 `contains`, `public`, and `private`

Everything between the top of a module and `contains` is the **specification part**, and the procedures come after `contains`. Procedures in a module are called **module procedures**.

### Controlling visibility

By default every name in a module is **public**, meaning that any unit that uses the module can see it. That is rarely what you want. A module usually has a small, clear set of tools meant for outside use, and some helper procedures and data that are just internal details. You control this with two attributes:

| Attribute | Effect |
|-----------|--------|
| `public` | The name can be used by units that `use` the module |
| `private` | The name can only be used inside the module |

There are two ways to apply them. The recommended style is to make everything private by default and then list what to export:

```fortran
module geometry
    implicit none
    private                              ! hide everything by default
    public :: circle_area, pi            ! ...except these
```

Alternatively, mark names one at a time:

```fortran
real, public  :: pi = 3.14159
real, private :: counter
```

Hiding internals gives you freedom: you can later change or remove private procedures without breaking any code that uses the module.

### Module variables and `protected`

Variables declared in a module keep their value for as long as the program runs, and every unit that uses them sees the same variable. They are useful for shared settings, but they also let any code change them from anywhere, which makes programs hard to follow. When outside code should read a value but not change it, add the `protected` attribute:

```fortran
integer, protected :: max_shapes = 10      ! visible, but only the module may change it
```

The module's own procedures can assign to it, for example through a subroutine such as `set_max_shapes`. An attempt to assign to it from outside is a compile error, such as:

```
Error: Variable 'k' is PROTECTED and cannot appear in a variable definition context (assignment) at (1)
```

Constants made with `parameter` are always safe to share, so a module is the ideal home for them.

### One catch: `private` hides imported names too

If a module uses another module (or `iso_fortran_env`) and then declares `private`, the names it imported also become private. A unit that uses the module must import them itself. The example below shows this with `dp`.

### Example

```fortran
module geometry
    use, intrinsic :: iso_fortran_env, only: dp => real64
    implicit none
    private
    public :: pi, circle_area, rectangle_area, times_called
    public :: max_shapes, set_max_shapes

    real(dp), parameter :: pi = 3.141592653589793_dp
    integer, protected :: max_shapes = 10
    integer :: calls = 0                     ! private module variable
contains
    function circle_area(r) result(a)
        real(dp), intent(in) :: r
        real(dp) :: a
        calls = calls + 1
        a = pi * square(r)
    end function circle_area

    function rectangle_area(w, h) result(a)
        real(dp), intent(in) :: w, h
        real(dp) :: a
        calls = calls + 1
        a = w * h
    end function rectangle_area

    function times_called() result(n)
        integer :: n
        n = calls
    end function times_called

    subroutine set_max_shapes(n)
        integer, intent(in) :: n
        max_shapes = n
    end subroutine set_max_shapes

    function square(x) result(y)            ! private helper
        real(dp), intent(in) :: x
        real(dp) :: y
        y = x * x
    end function square
end module geometry

program module_demo
    use, intrinsic :: iso_fortran_env, only: dp => real64
    use geometry
    implicit none

    print *, "circle area (r=2)   :", circle_area(2.0_dp)
    print *, "rectangle area 3x4  :", rectangle_area(3.0_dp, 4.0_dp)
    print *, "pi                  :", pi
    print *, "procedure calls made:", times_called()
    print *, "max_shapes          :", max_shapes
    call set_max_shapes(25)
    print *, "max_shapes now      :", max_shapes
end program module_demo
```

Output:

```
 circle area (r=2)   :   12.566370614359172     
 rectangle area 3x4  :   12.000000000000000     
 pi                  :   3.1415926535897931     
 procedure calls made:           2
 max_shapes          :          10
 max_shapes now      :          25
```

The program imported `dp` on its own, because `geometry` is `private` by default and does not pass it on. The helper `square` and the counter `calls` are hidden. If the program tried to call `square(2.0_dp)`, it would stop with an error like `Function 'square' at (1) has no IMPLICIT type`, as though the function did not exist.

### Using it from separate files

In a real project each module lives in its own file. With the layout recommended in Lesson 3.4, `geometry.f90` sits in `src/` and the main program in `app/`. Compiled by hand, the module goes first:

```bash
gfortran -c geometry.f90
gfortran -c main.f90
gfortran geometry.o main.o -o app
```

With `fpm` you only type `fpm run`.

---

## 16.3 `use`, `only`, and Renaming

A program unit gains access to a module with the `use` statement. It must come **before** `implicit none` and before any other declarations:

```fortran
program main
    use geometry                  ! 1. use statements
    implicit none                 ! 2. implicit none
    ! 3. declarations
```

### Three forms

| Statement | Effect |
|-----------|--------|
| `use geometry` | Imports **every** public name from the module |
| `use geometry, only: circle_area, pi` | Imports **only** the names listed |
| `use geometry, only: area => circle_area` | Imports `circle_area`, but calls it `area` here |

(An *intrinsic* module such as `iso_fortran_env` is imported with `use, intrinsic :: iso_fortran_env`, as seen in Lesson 6.3.)

### Why `only` is recommended

- **Readability.** A reader sees exactly where each name comes from, without searching through modules.
- **Fewer collisions.** Importing everything from several modules can bring in two different things with the same name. Using `only` keeps the list under your control.
- **Safer changes.** If the module later adds a new name, it cannot accidentally clash with one of yours.

### Renaming to resolve clashes

Suppose two modules each define a procedure called `area`. Importing both with a plain `use` would make the name ambiguous and the compiler would report an error when `area` is used. Renaming removes the conflict:

```fortran
use circle_mod, only: circle_area => area
use square_mod, only: square_area => area
```

The syntax is `local_name => name_in_module`, with the arrow pointing from the new name to the original. The same renaming works on kind names, which gives the popular idiom `use, intrinsic :: iso_fortran_env, only: dp => real64`.

### Modules using modules

A module may itself `use` another module, so libraries can be built in layers. Remember the rule from 16.2: what a module imports is public or private according to the module's own `public` and `private` statements.

### Example

```fortran
module circle_mod
    implicit none
    real, parameter :: pi = 3.14159265
contains
    function area(r) result(a)
        real, intent(in) :: r
        real :: a
        a = pi * r**2
    end function area
end module circle_mod

module square_mod
    implicit none
contains
    function area(side) result(a)
        real, intent(in) :: side
        real :: a
        a = side**2
    end function area
end module square_mod

program rename_demo
    use circle_mod, only: circle_area => area, pi
    use square_mod, only: square_area => area
    implicit none

    print '(A, F8.3)', "circle of radius 2 :", circle_area(2.0)
    print '(A, F8.3)', "square of side 2   :", square_area(2.0)
    print '(A, F8.5)', "pi                 :", pi
end program rename_demo
```

Output:

```
circle of radius 2 :  12.566
square of side 2   :   4.000
pi                 : 3.14159
```

Both modules contain a function named `area`, and both can be used side by side because the program renamed them on import.

---

## 16.4 Explicit Interfaces

An **interface** is what the compiler knows about a procedure from the outside: its name, the number of arguments, and the type, rank, and attributes of each one. If the compiler knows the interface when it compiles a call, it can check that the call is correct. The interface is then called **explicit**.

Procedures in a module, and internal procedures after `contains`, always have an explicit interface, which is a big reason to use them. Suppose a module function expects a `real(dp)` argument and a program passes an integer:

```fortran
print *, twice(1)          ! twice expects real(dp)
```

The compiler refuses to build the program:

```
Error: Type mismatch in argument 'r' at (1); passed INTEGER(4) to REAL(8)
```

With an **implicit** interface, such as old-style external procedures, the compiler cannot check anything. The call would compile and run, giving garbage as the procedure reads an integer's bits as a real number. Many of the most baffling Fortran bugs of the past came from exactly this.

### Features that *require* an explicit interface

Some features cannot work without the compiler seeing the interface. All the features below are therefore only available for module and internal procedures (or procedures with an interface block):

| Feature | Lesson |
|---------|--------|
| Assumed-shape array arguments `x(:)` | 15.1 |
| `optional` arguments | 15.4 |
| Keyword arguments `call f(x, tol=1e-6)` | 15.4 |
| Allocatable and pointer dummy arguments | 13 |
| Array-valued function results | 15.2 |
| `pure` and `elemental` procedures | 17 |

### Interface blocks

Sometimes the procedure you want to call is **external**: it was compiled separately and is not in a module, for example old library code or a routine from another language. You can still give the compiler an explicit interface by writing an **interface block** that repeats the procedure's declarations:

```fortran
interface
    subroutine greet(name)
        character(len=*), intent(in) :: name
    end subroutine greet
end interface
```

The block contains only the argument declarations, not the body. The compiler then checks every call against it. The block must match the real procedure, though, and nothing verifies that the two stay in agreement. This is the main reason to prefer modules for your own code: the interface is generated for you and can never be out of date.

The compiler flag `-Wimplicit-interface` (Lesson 3.3) warns whenever a procedure is called without an explicit interface.

### Example

This program has a module of numerical tools, called with optional and keyword arguments, and then an external subroutine called through an interface block:

```fortran
module precision
    use, intrinsic :: iso_fortran_env, only: real64
    implicit none
    integer, parameter :: dp = real64
end module precision

module numerics
    use precision, only: dp
    implicit none
    private
    public :: mean, scale_array, clamp
contains
    function mean(x) result(m)
        real(dp), intent(in) :: x(:)
        real(dp) :: m
        m = sum(x) / size(x)
    end function mean

    subroutine scale_array(x, factor, offset)
        real(dp), intent(inout) :: x(:)
        real(dp), intent(in) :: factor
        real(dp), intent(in), optional :: offset
        x = factor * x
        if (present(offset)) x = x + offset
    end subroutine scale_array

    function clamp(x, lo, hi) result(y)
        real(dp), intent(in) :: x, lo, hi
        real(dp) :: y
        y = min(max(x, lo), hi)
    end function clamp
end module numerics

program interface_demo
    use precision, only: dp
    use numerics
    implicit none
    real(dp) :: v(4)

    interface
        subroutine greet(name)
            character(len=*), intent(in) :: name
        end subroutine greet
    end interface

    v = [1.0_dp, 2.0_dp, 3.0_dp, 4.0_dp]
    print *, "mean(v)          :", mean(v)
    call scale_array(v, 2.0_dp)
    print *, "scaled by 2      :", v
    call scale_array(v, 0.5_dp, offset=10.0_dp)
    print *, "x0.5 + 10        :", v
    print *, "clamp(15, 0, 10) :", clamp(15.0_dp, 0.0_dp, 10.0_dp)
    call greet("Ada")
end program interface_demo

subroutine greet(name)
    character(len=*), intent(in) :: name
    print '(2A)', "Hello from an external procedure, ", trim(name)
end subroutine greet
```

Output:

```
 mean(v)          :   2.5000000000000000     
 scaled by 2      :   2.0000000000000000        4.0000000000000000        6.0000000000000000        8.0000000000000000     
 x0.5 + 10        :   11.000000000000000        12.000000000000000        13.000000000000000        14.000000000000000     
 clamp(15, 0, 10) :   10.000000000000000     
Hello from an external procedure, Ada
```

The module procedures needed no interface block, since the module supplies one automatically, while the external `greet` needed one written by hand. When you write new code, put procedures in modules and let the compiler do this work for you.

---

[Previous](./[15]-Functions-and-Subroutines.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[17]-Recursion-and-Pure-Procedures.md)
