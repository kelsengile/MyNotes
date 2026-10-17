[Previous](./[5]-Variables-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[7]-Operators-and-Expressions.md)

*Core Syntax*

# Lesson 6 - Numeric Precision & Kinds (`kind`, `iso_fortran_env`)

Computers store numbers with a limited number of digits, and the choice of how many matters enormously in scientific work. Fortran lets you pick the precision with a feature called **kinds**. This lesson explains how, shows the pitfalls of floating-point arithmetic, and covers converting between types.

---

## 6.1 Single vs Double Precision

A `real` number is stored in a fixed number of bits, so only a limited number of decimal digits can be represented exactly.

| Name | Bits | Approx. decimal digits | Approx. range | Typical use |
|------|------|------------------------|---------------|-------------|
| Single precision (default `real`) | 32 | 6 to 7 | up to about 3.4 x 10^38 | Graphics, machine learning, saving memory |
| Double precision | 64 | 15 to 16 | up to about 1.8 x 10^308 | **Most scientific computing** |
| Quadruple precision | 128 | 33 | up to about 1.2 x 10^4932 | Special cases; slow and not on every platform |

The difference is easy to see with 1/3:

```
single:   0.333333343              (correct for about 7 digits)
double:   0.33333333333333331      (correct for about 16 digits)
```

In a long calculation with millions of steps, tiny single-precision errors pile up and can ruin the result. **Use double precision unless you have a specific reason not to.**

Integers have similar limits:

| Integer size | Bits | Largest value |
|--------------|------|---------------|
| Default `integer` | 32 | 2,147,483,647 |
| 64-bit integer | 64 | 9,223,372,036,854,775,807 |

---

## 6.2 The `kind` System

Every intrinsic type has a **kind parameter**, a small integer that selects the storage size and precision. You write it in parentheses after the type:

```fortran
real(kind=8) :: x     ! full form
real(8) :: y          ! short form, same meaning
integer(kind=8) :: n
```

Here comes the catch: **kind numbers are not portable.** With `gfortran`, `real(8)` is double precision because kind numbers equal the number of bytes. Other compilers number their kinds differently (some use 1, 2, 3...), so `real(8)` might fail to compile or mean something else. Never hard-code a kind number in real code.

Instead, use a portable method. There are two good ones (6.3). Either way, you give the kind a name once (conventionally `dp` for double precision) and use it everywhere:

```fortran
real(dp) :: x, y, z
```

### Literals need a kind too

A literal such as `0.1` is a default (single-precision) real. To make a literal of a particular kind, add an underscore and the kind name:

```fortran
x = 0.1_dp         ! double precision
n = 42_int64       ! 64-bit integer
```

Many older programs use `d` as the exponent letter, as in `1.0d0` or `2.5d-3`, which always means double precision. It works but is less flexible than the `_dp` suffix.

### Inquiring about kinds

Intrinsic functions report the properties of a type:

| Function | Returns |
|----------|---------|
| `kind(x)` | The kind number of `x` |
| `precision(x)` | Number of decimal digits of precision |
| `range(x)` | Decimal exponent range |
| `epsilon(x)` | The smallest value that, added to 1.0, gives a result different from 1.0 |
| `huge(x)` | The largest representable value |
| `tiny(x)` | The smallest positive normal real value |

---

## 6.3 `selected_real_kind` and `real64`

### Method 1: `iso_fortran_env` (recommended)

The intrinsic module `iso_fortran_env` (Fortran 2008) gives ready-made named kinds. Bring them in with `use`:

```fortran
program kinds_demo
    use, intrinsic :: iso_fortran_env, only: int32, int64, real32, real64
    implicit none
    integer(int32) :: small
    integer(int64) :: big
    real(real32) :: x
    real(real64) :: y

    small = huge(small)
    big = huge(big)
    x = 1.0_real32 / 3.0_real32
    y = 1.0_real64 / 3.0_real64

    print *, "int32 largest:", small
    print *, "int64 largest:", big
    print *, "single 1/3   :", x
    print *, "double 1/3   :", y
    print *, "digits (single, double):", precision(x), precision(y)
    print *, "epsilon single:", epsilon(x)
    print *, "epsilon double:", epsilon(y)
    print *, "largest double:", huge(y)
end program kinds_demo
```

Output (from `gfortran` 13; the exact layout of numbers can vary between compilers):

```
 int32 largest:  2147483647
 int64 largest:  9223372036854775807
 single 1/3   :  0.333333343    
 double 1/3   :  0.33333333333333331     
 digits (single, double):           6          15
 epsilon single:   1.19209290E-07
 epsilon double:   2.2204460492503131E-016
 largest double:   1.7976931348623157E+308
```

The names available in `iso_fortran_env` are `int8`, `int16`, `int32`, `int64`, `real32`, `real64`, `real128`. The number is the size in **bits**, so the name says exactly what you get. (`real128` is not available on every platform.)

You can rename a kind as you import it, which is a popular idiom:

```fortran
use, intrinsic :: iso_fortran_env, only: dp => real64, i8 => int64
```

After that, `real(dp)` and `integer(i8)` work throughout the program.

### Method 2: `selected_real_kind` and `selected_int_kind`

These intrinsic functions ask for a **requirement**, and the compiler returns a suitable kind number:

```fortran
integer, parameter :: dp = selected_real_kind(p=15, r=307)
integer, parameter :: i8 = selected_int_kind(18)
```

- `selected_real_kind(p, r)` returns a kind with at least `p` decimal digits of precision and exponent range of at least `r`. For double precision: 15 digits, range 307.
- `selected_int_kind(r)` returns an integer kind that can hold every value from -10^r to 10^r. `selected_int_kind(18)` gives a 64-bit integer.
- If no kind satisfies the request, the function returns a negative number, which causes a compile error when you use it. That is helpful, because you find out immediately.

Complete example:

```fortran
program selected_demo
    implicit none
    integer, parameter :: dp = selected_real_kind(15, 307)
    integer, parameter :: i8 = selected_int_kind(18)
    real(dp) :: x
    integer(i8) :: n

    x = 2.0_dp
    n = 10_i8**15
    print *, "kind numbers: dp =", dp, " i8 =", i8
    print *, "sqrt(2) =", sqrt(x)
    print *, "10^15   =", n
end program selected_demo
```

Output:

```
 kind numbers: dp =           8  i8 =           8
 sqrt(2) =   1.4142135623730951     
 10^15   =     1000000000000000
```

Both methods are portable. `iso_fortran_env` is shorter and clearer; `selected_real_kind` works with the older standards (Fortran 90/95).

### Sharing one kind across files

In a bigger project, define the kinds once in a module and `use` that module everywhere:

```fortran
module precision
    use, intrinsic :: iso_fortran_env, only: real64, int64
    implicit none
    integer, parameter :: dp = real64
    integer, parameter :: i8 = int64
end module precision
```

Modules are covered in Lesson 16.

---

## 6.4 Floating-Point Pitfalls

Real numbers are stored in binary. Many decimal fractions, such as 0.1, have no exact binary form, just as 1/3 has no exact decimal form. Every `real` is therefore a very close approximation. The program below shows the main consequences:

```fortran
program pitfalls
    use, intrinsic :: iso_fortran_env, only: dp => real64
    implicit none
    real(dp) :: a, b, tol, lit_bad, lit_good, total
    integer :: i

    a = 0.1_dp + 0.2_dp
    b = 0.3_dp
    print *, "a == b ?      ", a == b
    print *, "a - b =       ", a - b

    tol = 1.0e-12_dp
    print *, "close enough? ", abs(a - b) < tol

    lit_bad  = 0.1
    lit_good = 0.1_dp
    print *, "0.1 (single literal):", lit_bad
    print *, "0.1_dp              :", lit_good

    total = 0.0_dp
    do i = 1, 10
        total = total + 0.1_dp
    end do
    print *, "ten times 0.1 =", total
    print *, "equals 1.0 ?  ", total == 1.0_dp
    print *, "1/3 in integers:", 1/3
end program pitfalls
```

Output:

```
 a == b ?       F
 a - b =          5.5511151231257827E-017
 close enough?  T
 0.1 (single literal):  0.10000000149011612     
 0.1_dp              :  0.10000000000000001     
 ten times 0.1 =  0.99999999999999989     
 equals 1.0 ?   F
 1/3 in integers:           0
```

(`gfortran` also prints a warning for the last line, because it can see the integer division gives 0.)

### The lessons

1. **Never compare real numbers with `==`.** Small rounding errors make mathematically equal values differ. Test whether they are close enough instead:
   ```fortran
   if (abs(a - b) < tol) then    ! tol chosen to suit the problem
   ```
   A relative tolerance such as `abs(a - b) <= tol * max(abs(a), abs(b))` is better when the values can be very large or very small.
2. **A literal without a kind is single precision**, even when assigned to a double-precision variable. In `lit_bad = 0.1` the compiler first creates a single-precision 0.1 (with its error at the 8th digit) and *then* widens it. Always write `0.1_dp`. This is one of the most common precision bugs in Fortran.
3. **Errors accumulate.** Adding 0.1 ten times does not give exactly 1.0.
4. **Integer division discards the fraction.** `1/3` is `0`, not 0.333. If either operand is real, the result is real.
5. **Integers overflow.** Going past `huge(n)` does not raise an error by default. The result is wrong, and what happens is processor-dependent. Use a 64-bit integer, or compile with `-fcheck=all` during development, to catch problems.
6. **Subtracting nearly equal numbers loses digits** (called *cancellation*), so rearrange formulas when you can.

Two special floating-point values exist as well: `Infinity` (for example from `1.0/0.0` with traps off) and `NaN` ("not a number", for example `sqrt(-1.0)`). Once a `NaN` enters a calculation, it contaminates everything it touches. The flag `-ffpe-trap=invalid,zero,overflow` stops the program at the first one.

For background reading, see the IEEE 754 standard that defines floating-point behavior on nearly all modern hardware.

---

## 6.5 Type Conversion

Fortran converts types automatically in mixed expressions, and you can also convert on purpose with intrinsic functions.

### Automatic (implicit) conversion

When an operation mixes types, the "lower" type is promoted to the "higher" one. The order is `integer` → `real` → `complex`, and a smaller kind is promoted to a larger kind:

```fortran
x = 2 * 3.5        ! 2 becomes 2.0, result is real 7.0
```

When you **assign** to a variable of a different type, the value is converted to that variable's type. Assigning a real to an integer variable chops off the fraction, and `-Wconversion` can warn you about it. Prefer explicit conversion so your intent is visible.

### Explicit conversion functions

| Function | Result | Behavior |
|----------|--------|----------|
| `real(i, dp)` | real of kind `dp` | Converts an integer (or other type). **Always give the kind.** |
| `dble(x)` | double precision real | Older shorthand for `real(x, kind=8)` |
| `int(x)` | integer | **Truncates** toward zero |
| `nint(x)` | integer | **Rounds** to the nearest integer |
| `floor(x)` | integer | Largest integer not greater than `x` |
| `ceiling(x)` | integer | Smallest integer not less than `x` |
| `cmplx(a, b, kind=dp)` | complex | Builds a complex number from two reals |

A common trap: `real(i)` with no kind returns a *default* (single) real, silently discarding precision for large `i`. Write `real(i, dp)`.

```fortran
program conversion_demo
    use, intrinsic :: iso_fortran_env, only: dp => real64
    implicit none
    integer :: i, n
    real(dp) :: x

    i = 7
    n = 2
    x = 7.8_dp

    print *, "i/n                  =", i / n
    print *, "real(i,dp)/n         =", real(i, dp) / n
    print *, "int(7.8), nint(7.8)  =", int(x), nint(x)
    print *, "floor(7.8), ceil(7.8)=", floor(x), ceiling(x)
    print *, "int(-7.8), nint(-7.8)=", int(-x), nint(-x)
    print *, "floor(-7.8), ceil    =", floor(-x), ceiling(-x)
    print *, "cmplx(1,2)           =", cmplx(1.0_dp, 2.0_dp, kind=dp)
end program conversion_demo
```

Output:

```
 i/n                  =           3
 real(i,dp)/n         =   3.5000000000000000     
 int(7.8), nint(7.8)  =           7           8
 floor(7.8), ceil(7.8)=           7           8
 int(-7.8), nint(-7.8)=          -7          -8
 floor(-7.8), ceil    =          -8          -7
 cmplx(1,2)           =               (1.0000000000000000,2.0000000000000000)
```

Notice how the negative numbers show the differences: `int` moves toward zero (-7), `floor` moves down (-8), and `nint` rounds to the closest (-8).

---

[Previous](./[5]-Variables-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[7]-Operators-and-Expressions.md)