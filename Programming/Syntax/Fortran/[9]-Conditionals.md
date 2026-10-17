[Previous](./[8]-Input-and-Output.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[10]-Loops.md)

*Core Syntax*

# Lesson 9 - Conditionals: `if`, `else if`, `select case`

Programs become interesting when they can make decisions. A **conditional** runs some statements only when a condition is true. This lesson covers the two decision tools in Fortran, the `if` construct and `select case`, and previews the array-based `where`.

---

## 9.1 The `if` Construct

The basic form tests a `logical` expression (Lesson 7) and runs a block of statements only if it is `.true.`:

```fortran
if (condition) then
    ! statements that run when condition is true
end if
```

The condition **must** be in parentheses, and `then` is required for the block form.

### Single-line `if`

For a single statement, you can drop `then` and `end if`:

```fortran
if (condition) statement
```

### Example

```fortran
program if_demo
    implicit none
    integer :: temperature

    temperature = 35
    if (temperature > 30) then
        print *, "It is hot."
    end if

    if (temperature < 0) print *, "Freezing!"

    if (temperature > 30) then
        print *, "Stay hydrated."
    else
        print *, "Enjoy the weather."
    end if
end program if_demo
```

Output:

```
 It is hot.
 Stay hydrated.
```

The first `if` is true, so it prints. The second is false, so nothing is printed. The third uses `else`, which provides a block that runs when the condition is **false**.

### Rules to remember

- Only one `if` block of a given chain runs. Once a condition succeeds, the rest are skipped.
- A single-line `if` can hold only **one** statement and cannot contain `else`.
- Indent the block by four spaces. The compiler does not need it, but readers do.
- There is no short-circuit guarantee for `.and.` and `.or.` (see Lesson 7.3), so guard array accesses with a nested `if`.

---

## 9.2 `else if` and `else`

To choose between several alternatives, chain conditions with `else if`. The conditions are tested from top to bottom, and the **first** true one wins. An optional final `else` handles everything left over.

```fortran
if (condition1) then
    ! ...
else if (condition2) then
    ! ...
else
    ! ...
end if
```

Note the spelling: `else if` may also be written `elseif`, and `end if` may be written `endif`. Both spellings are accepted. This course uses the two-word forms.

### Example: grading

```fortran
program grade_demo
    implicit none
    integer :: score
    character(len=1) :: grade

    score = 83
    if (score >= 90) then
        grade = "A"
    else if (score >= 80) then
        grade = "B"
    else if (score >= 70) then
        grade = "C"
    else if (score >= 60) then
        grade = "D"
    else
        grade = "F"
    end if
    print *, "Score", score, "gives grade ", grade
end program grade_demo
```

Output:

```
 Score          83 gives grade B
```

Because the conditions are checked in order, `score >= 80` only needs to be written as such: by that point the program already knows `score` is below 90.

### Nested `if` and named constructs

An `if` block can sit inside another. For long or deeply nested code you may give a construct a **name**, which must appear at the beginning and on `end if` (and optionally on `else` branches):

```fortran
program nested_demo
    implicit none
    integer :: age
    logical :: has_ticket

    age = 16
    has_ticket = .true.

    check: if (has_ticket) then
        if (age >= 18) then
            print *, "Welcome to the show."
        else if (age >= 12) then
            print *, "Entry allowed with a guardian."
        else
            print *, "Too young."
        end if
    else check
        print *, "Please buy a ticket first."
    end if check
end program nested_demo
```

Output:

```
 Entry allowed with a guardian.
```

Names help the reader match each `end if` to its `if` in long programs.

### Common mistakes

| Mistake | Problem |
|---------|---------|
| `if (x = 5) then` | A single `=` is assignment. Use `==` to compare. |
| `if (x > 0) then print *, x` | A block `if` needs the statement on a new line. Without `then`, use the single-line form. |
| `if (0 < x < 10)` | Comparisons cannot chain. Write `x > 0 .and. x < 10`. |
| Comparing reals with `==` | Rounding errors; use a tolerance (Lesson 6). |

---

## 9.3 `select case`

When you compare **one value** against several fixed alternatives, `select case` is cleaner than a long `else if` chain.

```fortran
select case (expression)
case (value1)
    ! ...
case (value2, value3)
    ! ...
case (low:high)
    ! ...
case default
    ! ...
end select
```

The **selector** (the expression in parentheses) must be an `integer`, `character`, or `logical` value. Real numbers are **not** allowed.

Each `case` lists what it matches:

| Case selector | Matches |
|---------------|---------|
| `case (3)` | exactly 3 |
| `case (2, 3, 4)` | any of 2, 3, or 4 |
| `case (6:7)` | the range 6 to 7 inclusive |
| `case (:-1)` | everything up to and including -1 |
| `case (10:)` | 10 and above |
| `case default` | anything not matched elsewhere |

### Example

```fortran
program select_demo
    implicit none
    integer :: day, n
    character(len=1) :: ch

    day = 6
    select case (day)
    case (1)
        print *, "Monday"
    case (2, 3, 4)
        print *, "Midweek"
    case (5)
        print *, "Friday"
    case (6:7)
        print *, "Weekend"
    case default
        print *, "Not a valid day"
    end select

    ch = "e"
    select case (ch)
    case ("a", "e", "i", "o", "u")
        print *, ch, " is a vowel"
    case ("b":"d", "f":"h", "j":"n", "p":"t", "v":"z")
        print *, ch, " is a consonant"
    case default
        print *, ch, " is not a lowercase letter"
    end select

    n = 15
    select case (n)
    case (:-1)
        print *, "negative"
    case (0)
        print *, "zero"
    case (1:9)
        print *, "small"
    case (10:)
        print *, "large"
    end select
end program select_demo
```

Output:

```
 Weekend
 e is a vowel
 large
```

### Differences from other languages

- **No fall-through.** Only the one matching `case` block runs, then control jumps to after `end select`. There is no `break` statement, and you do not need one.
- **Cases must not overlap.** If a value could match two `case` selectors, the compiler reports an error (for example listing `"a"` in one case and the range `"a":"z"` in another).
- **`case default` is optional**, but recommended, so unexpected values are handled instead of silently ignored.
- A `select case` can be named, like `if`, for clarity in long code.

### Choosing between the two

| Use `if` / `else if` when... | Use `select case` when... |
|------------------------------|---------------------------|
| Testing ranges of real numbers | Testing one integer, character, or logical value |
| The conditions involve different variables | All branches test the same value |
| Conditions are complex (`.and.`, `.or.`) | The alternatives are a fixed list of values or ranges |

---

## 9.4 Logical `where` Preview

An `if` makes **one** decision. When you have an **array** and want to decide separately for every element, Fortran offers the `where` construct. This is only a preview. Lesson 12 covers it fully, after you have learned arrays in Lesson 11.

```fortran
program where_preview
    implicit none
    real :: v(6), w(6)

    v = [3.0, -1.5, 0.0, 8.0, -4.0, 2.5]
    w = v
    where (w < 0.0)
        w = 0.0
    end where
    print '(6F6.1)', v
    print '(6F6.1)', w

    where (v > 0.0)
        w = sqrt(v)
    elsewhere
        w = -1.0
    end where
    print '(6F6.2)', w
end program where_preview
```

Output:

```
   3.0  -1.5   0.0   8.0  -4.0   2.5
   3.0   0.0   0.0   8.0   0.0   2.5
  1.73 -1.00 -1.00  2.83 -1.00  1.58
```

- `where (w < 0.0)` examines every element of `w`. The assignment inside is applied only to those where the condition is true. Here, negative values are replaced by zero.
- `elsewhere` handles the elements where the condition is false.
- No loop is needed: the test and the assignment apply to the whole array at once.

Think of it as an `if` that runs once per element.

---

[Previous](./[8]-Input-and-Output.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[10]-Loops.md)