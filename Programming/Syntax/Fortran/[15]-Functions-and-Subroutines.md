[Previous](./[14]-Strings.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[16]-Modules.md)

*Procedures & Modules*

# Lesson 15 - Functions & Subroutines

A program that does everything in one long block quickly becomes impossible to read. **Procedures** let you name a piece of work, write it once, and use it as often as you like. Fortran has two kinds: **subroutines**, which perform an action, and **functions**, which calculate and return a value. This lesson shows how to write and call both, how to declare what each argument is allowed to do with `intent`, and how to make arguments optional or pass them by name.

---

## 15.1 Declaring and Calling Subroutines

A **subroutine** is a named block of code that is run with the `call` statement:

```fortran
subroutine name(arg1, arg2)
    ! declarations of the arguments and any local variables
    ! executable statements
end subroutine name
```

The names in the parentheses are the **dummy arguments** (also called formal parameters). They stand for whatever you give the subroutine when you call it. The values or variables you actually write in the `call` are the **actual arguments**:

```fortran
call swap(a, b)        ! a and b are the actual arguments
```

### Where procedures live

For now, place your procedures **inside the main program**, after the `contains` statement (Lesson 4.1). Procedures placed there are called **internal procedures**. Lesson 16 shows how to put them in modules, which is the better choice for larger programs.

```fortran
program name
    implicit none
    ! declarations
    ! statements, including calls
contains
    subroutine one(...)
    end subroutine one
end program name
```

Each procedure has its own declarations, and its own `implicit none` is not needed when it sits inside a program that already has one, because the setting is inherited.

### Arguments are shared, not copied

Fortran normally passes arguments **by reference**: the procedure does not receive a copy of the value, but works on the caller's variable itself. So if the subroutine changes a dummy argument, the actual argument changes too. This is why `swap(a, b)` can really exchange two variables. It is also why the `intent` attribute (15.3) matters.

### Arrays as arguments

The most convenient way to receive an array is as an **assumed-shape** dummy argument, written with a colon: `real :: v(:)`. The procedure then learns the size and bounds from the array it is given, and works for arrays of any length and also for slices (Lesson 11.4):

```fortran
subroutine normalize(v)
    real, intent(inout) :: v(:)
    v = v / norm2(v)           ! size is known from v itself: size(v)
end subroutine normalize
```

Assumed-shape arguments need an **explicit interface**, which internal procedures and module procedures always have (see Lesson 16.4).

Strings follow the same idea with `character(len=*)`: the dummy takes the length of whatever is passed (Lesson 14.4).

### Other useful details

- `return` leaves the procedure early. It is rarely needed, because reaching `end subroutine` returns automatically.
- A subroutine may have **no arguments**: `call say_hello()`.
- A dummy argument with the `value` attribute receives a **copy**, so changes inside do not reach the caller. It is mostly used when calling C code (Lesson 40).

### Example

```fortran
program subroutine_demo
    implicit none
    real :: a, b, v(4), w(4)

    a = 3.0
    b = 7.0
    print *, "before swap:", a, b
    call swap(a, b)
    print *, "after swap :", a, b

    call print_table(4)

    v = [3.0, 0.0, 4.0, 0.0]
    call normalize(v)
    print *, "normalized v:", v

    w = [6.0, 2.0, 8.0, 5.0]
    call normalize(w(1:3:2))       ! only elements 1 and 3 of w
    print *, "after slice  :", w
contains
    subroutine swap(x, y)
        real, intent(inout) :: x, y
        real :: tmp
        tmp = x
        x = y
        y = tmp
    end subroutine swap

    subroutine print_table(n)
        integer, intent(in) :: n
        integer :: i
        print '(A)', "  n  n**2"
        do i = 1, n
            print '(I3, I5)', i, i**2
        end do
    end subroutine print_table

    subroutine normalize(w)
        real, intent(inout) :: w(:)
        w = w / norm2(w)
    end subroutine normalize
end program subroutine_demo
```

Output:

```
 before swap:   3.00000000       7.00000000    
 after swap :   7.00000000       3.00000000    
  n  n**2
  1    1
  2    4
  3    9
  4   16
 normalized v:  0.600000024       0.00000000      0.800000012       0.00000000    
 after slice  :  0.600000024       2.00000000      0.800000012       5.00000000    
```

The second call to `normalize` received only the two elements `w(1)` and `w(3)` (6 and 8) as an array of size 2, and scaled them to 0.6 and 0.8. Because the arguments are shared, the procedure updated those elements of `w` directly, and the other two elements were not touched.

---

## 15.2 Functions and Return Values

A **function** calculates one result and hands it back. Instead of `call`, you use it inside an expression, just as you use `sqrt(x)`:

```fortran
y = 2.0 * celsius_to_f(t) + 1.0
```

### Declaring a function

The recommended form names the return variable with `result(...)`:

```fortran
function name(arg1, arg2) result(res)
    ! declarations, including the type of res
    res = ...                      ! the last value assigned to res is returned
end function name
```

Inside the function, `res` is an ordinary variable. Whatever it holds when the function ends is the value returned. Declare its type like any other variable:

```fortran
function celsius_to_f(c) result(f)
    real, intent(in) :: c
    real :: f
    f = 1.8 * c + 32.0
end function celsius_to_f
```

You may also see the older style, where the function name itself is the result variable and the type is written in front: `real function celsius_to_f(c)`. It works, but `result(...)` is clearer and is required for recursion (Lesson 17).

### Rules and good habits

- Make every argument of a function `intent(in)` (15.3). A function that changes its arguments has hidden side effects and surprises the caller. When you need to produce several values or modify arguments, use a subroutine.
- A function can return an **array** or a **string**, not just a scalar. An array result may be sized from an argument, as in `real :: s(size(x))`.
- The result must be assigned before the function ends. Reading it without assigning is a bug.
- The function's type is the type of its result. When the function is used, the caller sees it as a value of that type: `celsius_to_f(100.0)` is a `real`.
- Logical functions make conditions read nicely: `if (is_even(n)) then`.

### Example

```fortran
program function_demo
    use, intrinsic :: iso_fortran_env, only: dp => real64
    implicit none
    real(dp) :: data(5)

    data = [2.0_dp, 4.0_dp, 6.0_dp, 8.0_dp, 10.0_dp]

    print *, "100 C in F      :", celsius_to_f(100.0_dp)
    print *, "distance        :", distance(0.0_dp, 0.0_dp, 3.0_dp, 4.0_dp)
    print *, "mean of data    :", mean(data)
    print *, "running sum     :", running_sum(data)
    print *, "7 even?         :", is_even(7), "   10 even?:", is_even(10)
    print *, greeting("Ada")
contains
    function celsius_to_f(c) result(f)
        real(dp), intent(in) :: c
        real(dp) :: f
        f = 1.8_dp * c + 32.0_dp
    end function celsius_to_f

    function distance(x1, y1, x2, y2) result(d)
        real(dp), intent(in) :: x1, y1, x2, y2
        real(dp) :: d
        d = sqrt((x2 - x1)**2 + (y2 - y1)**2)
    end function distance

    function mean(x) result(m)
        real(dp), intent(in) :: x(:)
        real(dp) :: m
        m = sum(x) / size(x)
    end function mean

    function running_sum(x) result(s)
        real(dp), intent(in) :: x(:)
        real(dp) :: s(size(x))
        integer :: i
        s(1) = x(1)
        do i = 2, size(x)
            s(i) = s(i - 1) + x(i)
        end do
    end function running_sum

    function is_even(n) result(r)
        integer, intent(in) :: n
        logical :: r
        r = mod(n, 2) == 0
    end function is_even

    function greeting(name) result(msg)
        character(len=*), intent(in) :: name
        character(len=:), allocatable :: msg
        msg = "Hello, " // trim(name) // "!"
    end function greeting
end program function_demo
```

Output:

```
 100 C in F      :   212.00000000000000     
 distance        :   5.0000000000000000     
 mean of data    :   6.0000000000000000     
 running sum     :   2.0000000000000000        6.0000000000000000        12.000000000000000        20.000000000000000        30.000000000000000     
 7 even?         : F    10 even?: T
 Hello, Ada!
```

---

## 15.3 `intent(in)`, `intent(out)`, `intent(inout)`

Because arguments are shared with the caller, a procedure should say what it intends to do with each one. This is done with the `intent` attribute in the declaration of each dummy argument:

| Attribute | Meaning | Typical use |
|-----------|---------|-------------|
| `intent(in)` | The procedure only **reads** the argument. Changing it is an error. | Inputs |
| `intent(out)` | The procedure only **sets** the argument. Its value on entry is undefined, and the procedure is expected to give it a value. | Results of a subroutine |
| `intent(inout)` | The procedure **reads and changes** the argument. | Values updated in place |

### Why bother?

1. **The compiler enforces it.** If you try to assign to an `intent(in)` argument, you get an error at compile time, instead of a wrong answer at run time.
2. **It documents the code.** Reading the first lines of a procedure tells you its inputs and outputs.
3. **It enables optimization.** The compiler knows an `intent(in)` array cannot change underneath it.
4. **It prevents mistakes with constants.** A literal, such as `3.0`, can only be passed where the argument is `intent(in)`. Passing it to an `intent(out)` or `intent(inout)` argument is an error, since the procedure would be trying to change a constant.

Some further details:

- With `intent(out)`, the incoming value must not be used. For an allocatable argument, an `intent(out)` automatically **deallocates** it on entry.
- Declare `intent` for **every** dummy argument. A few exceptions exist (such as pointers and procedure arguments), but the habit is a good one.

### What the compiler says

Suppose a procedure tries to change a read-only argument:

```fortran
subroutine bad(x)
    real, intent(in) :: x
    x = x + 1.0            ! error!
end subroutine bad
```

The compiler stops with a message such as:

```
Error: Dummy argument 'x' with INTENT(IN) in variable definition context (assignment) at (1)
```

### Example

```fortran
program intent_demo
    implicit none
    real :: data(6), average, spread, total

    data = [4.0, 8.0, 15.0, 16.0, 23.0, 42.0]
    call stats(data, average, spread)
    print *, "average =", average
    print *, "spread  =", spread

    total = 10.0
    call add_to(total, 5.0)
    call add_to(total, 2.5)
    print *, "total   =", total
contains
    subroutine stats(x, mean, range_width)
        real, intent(in)  :: x(:)          ! only read
        real, intent(out) :: mean          ! results: given a value here
        real, intent(out) :: range_width
        mean = sum(x) / size(x)
        range_width = maxval(x) - minval(x)
    end subroutine stats

    subroutine add_to(running_total, amount)
        real, intent(inout) :: running_total   ! read and updated
        real, intent(in)    :: amount
        running_total = running_total + amount
    end subroutine add_to
end program intent_demo
```

Output:

```
 average =   18.0000000    
 spread  =   38.0000000    
 total   =   17.5000000    
```

In `stats`, the intents tell the story: `x` goes in, `mean` and `range_width` come out. In `add_to`, `running_total` is both an input and an output.

---

## 15.4 Optional and Keyword Arguments

### Optional arguments

An argument marked `optional` may be left out of the call. Inside the procedure, test whether it was supplied with the function `present()` before using it:

```fortran
subroutine report(label, value, unit)
    character(len=*), intent(in) :: label
    real, intent(in) :: value
    character(len=*), intent(in), optional :: unit
    if (present(unit)) then
        ! unit was given
    end if
end subroutine
```

Never read an optional argument that is not present. If you need a default value, copy the argument into an ordinary local variable, choosing the default when it is absent:

```fortran
n = 2                                    ! the default
if (present(power)) n = power            ! override if supplied
```

> **Caution:** do not write `if (present(x) .and. x > 0)`. Fortran does not guarantee short-circuit evaluation (Lesson 7.3), so `x` might be examined even when it is absent. Use a nested `if` instead.

### Keyword arguments

In a call you can name the dummy argument you are giving, so the order no longer matters and the call reads like a sentence:

```fortran
call report("Length", 12.3456, unit="m")
call report(value=0.5, label="Ratio", decimals=3)
```

The rules:

- Once you use a keyword in a call, all the arguments **after it** must also use keywords. Positional arguments, if any, come first.
- Optional arguments are normally given by keyword, since you often skip some of them.
- The keyword is the **dummy argument name** used in the procedure, so choose descriptive names.
- Keywords require an **explicit interface**. Internal and module procedures supply one automatically (Lesson 16.4).

Intrinsic procedures use the same mechanism. For example `reshape(x, shape=[2, 3], order=[2, 1])` (Lesson 11.2) and `sum(a, dim=1, mask=a > 0)` (Lesson 12.2) are calls with keywords.

### Example

```fortran
program optional_demo
    implicit none

    call report("Length", 12.3456)
    call report("Length", 12.3456, "m")
    call report("Length", 12.3456, decimals=1)
    call report(value=0.5, label="Ratio", decimals=3, unit="%")

    print *, "1.0 vs 1.05, default tol :", approx_equal(1.0, 1.05)
    print *, "1.0 vs 1.05, tol = 0.1   :", approx_equal(1.0, 1.05, tol=0.1)
contains
    subroutine report(label, value, unit, decimals)
        character(len=*), intent(in) :: label
        real, intent(in) :: value
        character(len=*), intent(in), optional :: unit
        integer, intent(in), optional :: decimals
        integer :: d
        character(len=20) :: fmt, buf
        character(len=:), allocatable :: tail

        d = 2                                   ! default number of decimals
        if (present(decimals)) d = decimals
        tail = ""
        if (present(unit)) tail = " " // unit

        write (fmt, '("(F10.", I0, ")")') d     ! builds a format such as (F10.2)
        write (buf, fmt) value
        print '(A)', label // ": " // trim(adjustl(buf)) // tail
    end subroutine report

    function approx_equal(a, b, tol) result(same)
        real, intent(in) :: a, b
        real, intent(in), optional :: tol
        logical :: same
        real :: t
        t = 1.0e-6                              ! default tolerance
        if (present(tol)) t = tol
        same = abs(a - b) <= t
    end function approx_equal
end program optional_demo
```

Output:

```
Length: 12.35
Length: 12.35 m
Length: 12.3
Ratio: 0.500 %
 1.0 vs 1.05, default tol : F
 1.0 vs 1.05, tol = 0.1   : T
```

The last call to `report` gives its arguments in a different order than the declaration, which is only possible because every argument is named. The next lesson shows how to collect procedures like these in a module so that they can be shared between files.

---

[Previous](./[14]-Strings.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[16]-Modules.md)
