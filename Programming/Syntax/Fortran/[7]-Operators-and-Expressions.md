[Previous](./[6]-Kinds-and-Precision.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[8]-Input-and-Output.md)

*Core Syntax*

# Lesson 7 - Operators & Expressions

An **expression** combines values, variables, and **operators** to produce a new value, such as `2 * radius + 1` or `age >= 18`. This lesson covers the arithmetic, relational, and logical operators in Fortran, and the precedence rules that decide which part of an expression is evaluated first.

---

## 7.1 Arithmetic Operators and `**`

Fortran has five arithmetic operators:

| Operator | Meaning | Example | Result |
|----------|---------|---------|--------|
| `+` | Addition (or unary plus) | `17 + 5` | `22` |
| `-` | Subtraction (or unary minus) | `17 - 5` | `12` |
| `*` | Multiplication | `17 * 5` | `85` |
| `/` | Division | `17 / 5` | `3` (integers!) |
| `**` | Exponentiation (power) | `17 ** 2` | `289` |

Note that there is no `^` operator: powers are written `x**2`.

Two related intrinsic functions work alongside them:

| Function | Meaning |
|----------|---------|
| `mod(a, b)` | Remainder after dividing; result has the **sign of `a`** |
| `modulo(a, b)` | Modulo; result has the **sign of `b`** |

### Example

```fortran
program arithmetic_demo
    implicit none
    integer :: a, b

    a = 17
    b = 5
    print *, "a + b       =", a + b
    print *, "a - b       =", a - b
    print *, "a * b       =", a * b
    print *, "a / b       =", a / b
    print *, "a ** 2      =", a ** 2
    print *, "17.0 / 5    =", 17.0 / b
    print *, "mod(17, 5)  =", mod(a, b)
    print *, "mod(-17, 5) =", mod(-a, b)
    print *, "modulo(-17,5)=", modulo(-a, b)
    print *, "2**3**2     =", 2**3**2
    print *, "-2**2       =", -2**2
    print *, "(-2)**2     =", (-2)**2
    print *, "2.0**(-1)   =", 2.0**(-1)
    print *, "9.0**0.5    =", 9.0**0.5
end program arithmetic_demo
```

Output:

```
 a + b       =          22
 a - b       =          12
 a * b       =          85
 a / b       =           3
 a ** 2      =         289
 17.0 / 5    =   3.40000010    
 mod(17, 5)  =           2
 mod(-17, 5) =          -2
 modulo(-17,5)=           3
 2**3**2     =         512
 -2**2       =          -4
 (-2)**2     =           4
 2.0**(-1)   =  0.500000000    
 9.0**0.5    =   3.00000000    
```

### Integer division

When **both** operands are integers, the division result is an integer and the fractional part is **thrown away** (truncated toward zero): `17 / 5` is `3`, and `-17 / 5` is `-3`. If at least one operand is real, the result is real. This is a very frequent source of bugs:

```fortran
average = (a + b) / 2        ! integer division if a and b are integers
average = (a + b) / 2.0      ! correct: 2.0 is real
```

### Mixed-type arithmetic

When an operation mixes an integer and a real, the integer is converted to real first and the result is real. With different kinds, the result takes the kind with more precision (Lesson 6).

### Powers

- `x**2` with an integer exponent is calculated by repeated multiplication, which is fast and accurate. Prefer `x**2` over `x**2.0`.
- `x**0.5` with a real exponent uses a general (slower) power calculation. For square roots, `sqrt(x)` is clearer.
- `**` is **right-associative**: `2**3**2` means `2**(3**2)`, which is `2**9 = 512`, not `(2**3)**2 = 64`.
- A negative integer to a negative power gives an integer result that is usually not what you wanted (`2**(-1)` is `0` because it is integer arithmetic). Use a real base, `2.0**(-1)`.

### Operators on text

Strings have one operator of their own, concatenation `//`, which joins two strings: `"ab" // "cd"` gives `"abcd"`. It is described in Lesson 14.

---

## 7.2 Relational Operators

A **relational operator** compares two values and produces a `logical` result (`.true.` or `.false.`).

| Operator | Older form | Meaning |
|----------|------------|---------|
| `==` | `.eq.` | Equal to |
| `/=` | `.ne.` | Not equal to |
| `<` | `.lt.` | Less than |
| `<=` | `.le.` | Less than or equal to |
| `>` | `.gt.` | Greater than |
| `>=` | `.ge.` | Greater than or equal to |

Both forms are identical in meaning. The symbol forms (`==`, `/=`, ...) arrived in Fortran 90 and are standard today, but you will see the dotted forms in older code.

Notice that "not equal" is written `/=`, **not** `!=` as in C or Python. (`!` starts a comment in Fortran.)

```fortran
program relational_demo
    implicit none
    integer :: x, y

    x = 4
    y = 9
    print *, "x == y:", x == y
    print *, "x /= y:", x /= y
    print *, "x <  y:", x < y
    print *, "x <= y:", x <= y
    print *, "x >  y:", x > y
    print *, "x >= y:", x >= y
    print *, "'apple' < 'banana':", "apple" < "banana"
    print *, "'abc' == 'abc  '  :", "abc" == "abc  "
end program relational_demo
```

Output:

```
 x == y: F
 x /= y: T
 x <  y: T
 x <= y: T
 x >  y: F
 x >= y: F
 'apple' < 'banana': T
 'abc' == 'abc  '  : T
```

Things to remember:

- Strings are compared alphabetically (by character code). When two strings of different lengths are compared, the shorter one is padded with blanks, so `"abc" == "abc  "` is true.
- Relational operators work on `integer`, `real`, and `character` values. For `complex` numbers only `==` and `/=` are allowed, because there is no natural ordering.
- Do not use `==` to compare real numbers that came from calculations (see Lesson 6.4).
- A relational operator is not allowed to chain: `0 < x < 10` is an error. Write `0 < x .and. x < 10`.

---

## 7.3 Logical Operators

Logical operators combine or flip `logical` values.

| Operator | Meaning | True when... |
|----------|---------|--------------|
| `.not. a` | NOT | `a` is false |
| `a .and. b` | AND | both `a` and `b` are true |
| `a .or. b` | OR | at least one is true |
| `a .eqv. b` | Equivalent | `a` and `b` have the same value |
| `a .neqv. b` | Not equivalent (exclusive OR) | `a` and `b` have different values |

The **truth table**:

| `a` | `b` | `a .and. b` | `a .or. b` | `a .eqv. b` | `a .neqv. b` |
|-----|-----|-------------|------------|-------------|---------------|
| T | T | T | T | T | F |
| T | F | F | T | F | T |
| F | T | F | T | F | T |
| F | F | F | F | T | F |

```fortran
program logical_demo
    implicit none
    logical :: p, q
    integer :: age

    p = .true.
    q = .false.
    print *, "p .and. q  :", p .and. q
    print *, "p .or. q   :", p .or. q
    print *, ".not. p    :", .not. p
    print *, "p .eqv. q  :", p .eqv. q
    print *, "p .neqv. q :", p .neqv. q

    age = 25
    print *, "adult and under 65:", age >= 18 .and. age < 65
    print *, ".not. p .or. q    :", .not. p .or. q
    print *, "2 + 3*4**2/8      :", 2 + 3 * 4**2 / 8
    print *, "'ab' // 'cd'      : ", "ab" // "cd"
end program logical_demo
```

Output:

```
 p .and. q  : F
 p .or. q   : T
 .not. p    : F
 p .eqv. q  : F
 p .neqv. q : T
 adult and under 65: T
 .not. p .or. q    : F
 2 + 3*4**2/8      :           8
 'ab' // 'cd'      : abcd
```

### No guaranteed short-circuiting

In some languages, `a and b` stops evaluating if `a` is false. **Fortran does not promise this.** The compiler may evaluate both sides, or in any order. So this is unsafe:

```fortran
if (i <= n .and. values(i) > 0.0) then    ! may read values(n+1)!
```

If `i` is greater than `n`, `values(i)` could still be evaluated and read outside the array. Use nested conditions instead:

```fortran
if (i <= n) then
    if (values(i) > 0.0) then
        ...
    end if
end if
```

---

## 7.4 Operator Precedence

When an expression has several operators, Fortran evaluates them in order of **precedence** (highest first). Operators with equal precedence are evaluated left to right, **except** `**`, which goes right to left.

| Priority | Operator(s) | Kind |
|----------|-------------|------|
| 1 (highest) | user-defined unary operators | Lesson 22 |
| 2 | `**` | power (right to left) |
| 3 | `*`  `/` | multiply, divide |
| 4 | unary `+`  `-`, binary `+`  `-` | sign, add, subtract |
| 5 | `//` | string concatenation |
| 6 | `==` `/=` `<` `<=` `>` `>=` | relational |
| 7 | `.not.` | logical NOT |
| 8 | `.and.` | logical AND |
| 9 | `.or.` | logical OR |
| 10 | `.eqv.`  `.neqv.` | logical equivalence |
| 11 (lowest) | user-defined binary operators | Lesson 22 |

Reading the table gives these results:

- `2 + 3 * 4**2 / 8`: first `4**2 = 16`, then `3 * 16 = 48`, then `48 / 8 = 6`, then `2 + 6 = 8`.
- `-2**2` is `-(2**2) = -4`, because `**` binds tighter than the unary minus.
- `.not. p .or. q` is `(.not. p) .or. q`, not `.not. (p .or. q)`.
- `a .or. b .and. c` is `a .or. (b .and. c)`, because `.and.` has higher priority than `.or.`.
- `x > 1 .and. x < 5` works as expected because the relational operators rank above `.and.`.

### Use parentheses

Parentheses always win. Use them freely to make the order obvious, even when they are not required:

```fortran
result = (a + b) / (c - d)
ok = (age >= 18) .and. (age < 65)
```

Fortran also respects parentheses mathematically. The compiler may rearrange an expression like `a + b + c` for speed, but it will never move things across parentheses in a way that changes the stated grouping.

Avoid writing two operators in a row without parentheses, such as `a * -b`. Standard Fortran forbids it, and some compilers accept it as an extension. Write `a * (-b)`.

---

[Previous](./[6]-Kinds-and-Precision.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[8]-Input-and-Output.md)