[Previous](./[7]-Operators-and-Expressions.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[9]-Conditionals.md)

*Core Syntax*

# Lesson 8 - Input & Output (`print`, `read`, `write`, format specifiers)

A program is only useful if it can show results and accept data. This lesson covers writing to the screen and reading from the keyboard, first the easy way (list-directed I/O) and then with full control over layout using **format specifiers**. Reading and writing files uses the same statements and is covered in Lesson 24.

---

## 8.1 List-Directed I/O

**List-directed** I/O means you give a list of items and let the compiler choose the layout. It is marked by `*` where a format would go.

### `print`

```fortran
program list_directed_io
    implicit none
    integer :: n
    real :: x
    character(len=10) :: word
    logical :: flag

    n = 42
    x = 3.14159
    word = "Fortran"
    flag = .true.

    print *, n
    print *, n, x
    print *, "Name: ", word, " flag: ", flag
    write (*, *) "Same output using write"
    print *
    print *, "after a blank line"
end program list_directed_io
```

Output:

```
          42
          42   3.14159012    
 Name: Fortran    flag:  T
 Same output using write
 
 after a blank line
```

What to notice:

- `print *, list` prints each item separated by spaces, followed by a new line. Each `print` starts a new line.
- The first column is a blank space (see Lesson 1).
- Numbers are padded to a standard width, and a default `real` shows more digits than you typed (`3.14159012`), because the real is stored in binary (Lesson 6).
- Character variables keep their padding. `word` has length 10, so `"Fortran"` is followed by three blank spaces.
- A `print *` with nothing after the comma prints a blank line.

### `write`

`write` is the general-purpose output statement. Its form is `write (unit, format) list`:

```fortran
write (*, *) "hello"           ! unit * = screen, format * = list-directed
write (*, '(A)') "hello"       ! screen, with an explicit format
```

- The **unit** says *where* to send the data. `*` means the standard output (normally the screen). A file unit is used in Lesson 24.
- The **format** says *how* to lay it out. `*` means list-directed.

`print *, x` is shorthand for `write (*, *) x`.

### Named standard units

The module `iso_fortran_env` provides named constants for the standard streams:

```fortran
use, intrinsic :: iso_fortran_env, only: input_unit, output_unit, error_unit
write (error_unit, '(A)') "Warning: something went wrong"
```

Using `error_unit` sends messages to the *standard error* stream, which is kept separate from normal output and is the proper place for error messages.

---

## 8.2 Formatted Output (`I`, `F`, `E`, `A`)

To control exactly how output looks, supply a **format**: a list of **edit descriptors** in parentheses. The format can be a string in the statement or a named character constant:

```fortran
print '(F8.3)', x                         ! format as a string
write (*, '(I5, 2X, F8.3)') n, x          ! format in write
character(len=*), parameter :: fmt = '(I5, 2X, F8.3)'
write (*, fmt) n, x                       ! named format
```

### Core edit descriptors

| Descriptor | Used for | Meaning |
|------------|----------|---------|
| `Iw` | integer | Field width `w` characters |
| `Iw.m` | integer | Width `w`, at least `m` digits (padded with zeros) |
| `I0` | integer | Minimum width needed, no padding |
| `Fw.d` | real | Width `w`, `d` digits after the decimal point |
| `F0.d` | real | Minimum width, `d` decimals |
| `Ew.d` | real | Scientific notation, `d` digits in the mantissa |
| `ESw.d` | real | Scientific notation with one digit before the point (like `1.2346E+05`) |
| `A` | character | The string at its own length |
| `Aw` | character | Width `w` (right-justified, truncated if too long) |
| `Lw` | logical | `T` or `F` right-justified in width `w` |
| `nX` | spacing | Skip `n` spaces |
| `/` | layout | Start a new line |

### Examples

```fortran
program formatted_output
    implicit none
    integer :: n
    real :: x, big
    character(len=8) :: name

    n = 42
    x = 3.14159265
    big = 123456.789
    name = "Ada"

    print '(I5)', n
    print '(I0)', n
    print '(I5.3)', n
    print '(F8.3)', x
    print '(F0.2)', x
    print '(E12.4)', big
    print '(ES12.4)', big
    print '(A)', name
    print '(A)', trim(name)
    print '(A10)', trim(name)
    print '(A,I3,A,F6.2)', "n=", n, " x=", x
    print '(I3)', 123456
    print '(L3)', .true.
    print '(2I4,3X,F6.1)', 7, 8, 9.5
end program formatted_output
```

Output:

```
   42
42
  042
   3.142
3.14
  0.1235E+06
  1.2346E+05
Ada     
Ada
       Ada
n= 42 x=  3.14
***
  T
   7   8      9.5
```

Line by line:

| Statement | Result | Explanation |
|-----------|--------|-------------|
| `'(I5)'` | `   42` | Integer right-justified in 5 columns |
| `'(I0)'` | `42` | Just as wide as needed |
| `'(I5.3)'` | `  042` | At least 3 digits |
| `'(F8.3)'` | `   3.142` | 8 columns, 3 decimals (rounded) |
| `'(F0.2)'` | `3.14` | As compact as possible |
| `'(E12.4)'` | `  0.1235E+06` | Scientific form, `0.` mantissa |
| `'(ES12.4)'` | `  1.2346E+05` | Scientific form, `1.` to `9.` mantissa |
| `'(A)'` with `name` | `Ada     ` | Prints all 8 characters, including trailing blanks |
| `trim(name)` | `Ada` | `trim` removes the trailing blanks |
| `'(A10)'` | `       Ada` | Right-justified in 10 columns |
| `'(A,I3,A,F6.2)'` | `n= 42 x=  3.14` | Mixed text and numbers |
| `'(I3)'` for 123456 | `***` | **Too wide for the field: the compiler fills it with asterisks** |

### When the field is too narrow

If a number needs more columns than `w`, the output is a row of `*` characters. That is Fortran's way of saying "it did not fit". Increase `w` or use `I0` / `F0.d`.

### Text inside a format

String literals can be placed directly inside the format:

```fortran
print '("t(", I1, ") = ", F7.3)', i, t(i)
```

---

## 8.3 Reading from the Keyboard

The `read` statement mirrors `write`: `read (unit, format) list`. `read (*, *)` reads from the keyboard with list-directed formatting.

```fortran
program keyboard_input
    implicit none
    integer :: n
    real :: x
    character(len=20) :: name

    write (*, '(A)', advance='no') "Enter your name: "
    read (*, '(A)') name
    write (*, '(A)', advance='no') "Enter an integer and a real: "
    read (*, *) n, x
    print '(3A)', "Hello, ", trim(name), "!"
    print '(A,I0,A,F0.2)', "n = ", n, ", x = ", x
    print '(A,F0.2)', "n * x = ", n * x
end program keyboard_input
```

Sample session (the user types `Maria Santos`, then `7 2.5`):

```
Enter your name: Maria Santos
Enter an integer and a real: 7 2.5
Hello, Maria Santos!
n = 7, x = 2.50
n * x = 17.50
```

### Points to understand

- **Prompts.** Normally each `write` ends the line. Adding `advance='no'` keeps the cursor on the same line so the user types right after your prompt.
- **Reading text.** For names with spaces, use `read (*, '(A)') name`. With list-directed input `read (*, *) name`, text stops at the first blank or comma, so you would only receive `Maria`.
- **Reading several values.** One `read` can fill many variables. Values in the input may be separated by spaces, commas, or new lines.
- **Each `read` consumes a whole line** by default, even if you ask for fewer values than the line contains. The remaining values on that line are discarded.
- **Type matching.** If you type `abc` where an integer is expected, the program crashes with a runtime error. Lesson 26 shows how to check for bad input with `iostat`.
- **List-directed special cases.** A slash `/` ends the input early, leaving the remaining variables unchanged, and a repeat count like `3*0.5` supplies the value 0.5 three times.

---

## 8.4 Format Strings and Repeat Counts

### Repeat counts

A number in front of a descriptor repeats it, so `3F8.2` is the same as `F8.2, F8.2, F8.2`. You can also repeat a group in parentheses: `2(I3, F6.1)`.

### Format reversion

If the list has more items than the format has descriptors, the format is **reused** from the start, beginning a new line each time. If the format has more descriptors than items, output stops at the first descriptor that has no item.

### Unlimited repeat

Writing `*( ... )` repeats the group as often as needed (Fortran 2008). This is the easy way to print an entire array on one line:

```fortran
program format_repeat
    implicit none
    integer :: i, v(5)
    real :: t(3)
    character(len=*), parameter :: fmt = '(A,I4,F8.2)'

    v = [10, 20, 30, 40, 50]
    t = [1.5, 22.25, 333.125]

    print '(5I4)', v
    print '(I4)', v
    print '(*(I4))', v
    print '(*(I0,1X))', v
    print '(3F8.2)', t
    print fmt, "row", 1, t(1)
    print '(A,/,A)', "line one", "line two"
    do i = 1, 3
        print '("t(",I1,") = ",F7.3)', i, t(i)
    end do
    print '(F5.1)', 12345.678
end program format_repeat
```

Output:

```
  10  20  30  40  50
  10
  20
  30
  40
  50
  10  20  30  40  50
10 20 30 40 50
    1.50   22.25  333.12
row   1    1.50
line one
line two
t(1) =   1.500
t(2) =  22.250
t(3) = 333.125
*****
```

Reading the output:

- `'(5I4)'` fits five integers on one line.
- `'(I4)'` has one descriptor for five items, so the format restarts for each item and each lands on its own line.
- `'(*(I4))'` and `'(*(I0,1X))'` print the whole array on one line, the second with single spaces between values.
- `/` inside a format starts a new line.
- `F5.1` cannot hold `12345.7` (it needs 7 columns), so it prints `*****`.
- Rounding of exact halves (333.125 becomes `333.12`) follows the compiler's rounding rules and may differ between compilers.

### The old `format` statement

In older code you will see formats written as labelled statements:

```fortran
      write (*, 100) n, x
  100 format (I5, F8.3)
```

It does the same job. Modern code keeps the format next to the statement as a string, which is easier to read.

### Quick reference

| Goal | Format |
|------|--------|
| Integer, any width | `I0` |
| Real with 2 decimals | `F0.2` or `F8.2` |
| Scientific notation | `ES12.4` |
| A table row | `(I5, 3F10.4)` |
| Whole array on one line | `(*(F8.3))` |
| Text then value | `(A, F0.3)` |
| Blank line | `print *` |

---

[Previous](./[7]-Operators-and-Expressions.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[9]-Conditionals.md)