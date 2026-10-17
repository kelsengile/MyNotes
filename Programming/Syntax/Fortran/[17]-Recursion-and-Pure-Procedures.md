[Previous](./[16]-Modules.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[18]-Scope-and-Lifetime.md)

*Procedures & Modules*

# Lesson 17 - Recursion & Pure/Elemental Procedures

Now that you can write and organize procedures, this lesson adds three more tools. **Recursion** lets a procedure call itself, which is the natural way to express many mathematical definitions. **`pure`** and **`elemental`** procedures promise that they have no side effects, which makes them safe to run in parallel and lets one scalar function work on whole arrays. Finally you will see how to hand arrays and even other procedures to a procedure, which is how general-purpose numerical routines are written.

---

## 17.1 `recursive` Procedures

A procedure is **recursive** when it calls itself. This is useful whenever a problem can be reduced to a smaller copy of the same problem. The factorial is the classic case:

```
n! = n * (n-1)!        and        0! = 1! = 1
```

Every recursive procedure needs two parts:

1. A **base case**, where the answer is known directly and no further call is made. Without it the procedure would call itself forever.
2. A **recursive case**, which calls the procedure again on a *smaller* problem, moving it toward the base case.

### The `recursive` keyword

Fortran requires you to say so when a procedure may call itself, by adding `recursive` in front. For a function, you must also use `result(...)`, because inside the function the name refers to the function itself and cannot also hold the answer:

```fortran
recursive function factorial(n) result(f)
    integer, intent(in) :: n
    integer(int64) :: f
    if (n <= 1) then
        f = 1_int64                      ! base case
    else
        f = n * factorial(n - 1)         ! recursive case
    end if
end function factorial
```

(The newest standard, Fortran 2018, relaxed this rule, but older compilers still require the keyword, so always write `recursive` to keep your code portable.)

### How it works

Each call gets its own fresh copy of the local variables and arguments, kept in a region of memory called the **call stack**. The calls pile up on the way down to the base case, and then return one by one on the way back up:

```
factorial(4)
  = 4 * factorial(3)
        = 3 * factorial(2)
              = 2 * factorial(1)
                    = 1            <- base case
```

### Costs and limits

- Each call uses stack memory. Very deep recursion, such as a depth of millions, can exhaust the stack and crash the program. For simple counting problems a loop is safer and faster.
- Some recursive definitions are **very wasteful**. The naive Fibonacci function recomputes the same values again and again (see the example below), so its work grows exponentially. When that happens, use a loop or store the intermediate results.
- Beware of integer overflow. With default 32-bit integers, `13!` is already too large. The example uses 64-bit integers (Lesson 6), which last until `20!`.

Recursion shines for problems that are naturally nested or split into parts: trees, divide-and-conquer algorithms, the Towers of Hanoi puzzle, and mathematical definitions such as Euclid's algorithm for the greatest common divisor.

### Example

```fortran
program recursion_demo
    use, intrinsic :: iso_fortran_env, only: int64
    implicit none
    integer :: i, calls

    do i = 0, 20, 5
        print '(A, I2, A, I0)', "factorial(", i, ") = ", factorial(i)
    end do

    print *, "gcd(48, 18)       :", gcd(48, 18)
    print *, "digit_sum(12345)  :", digit_sum(12345)

    calls = 0
    print *, "fib(20) recursive :", fib(20), " using", calls, "calls"
    print *, "fib(20) with loop :", fib_loop(20)

    print *, "Towers of Hanoi with 3 disks:"
    call hanoi(3, "A", "C", "B")
contains
    recursive function factorial(n) result(f)
        integer, intent(in) :: n
        integer(int64) :: f
        if (n <= 1) then
            f = 1_int64
        else
            f = n * factorial(n - 1)
        end if
    end function factorial

    recursive function gcd(a, b) result(g)        ! Euclid's algorithm
        integer, intent(in) :: a, b
        integer :: g
        if (b == 0) then
            g = a
        else
            g = gcd(b, mod(a, b))
        end if
    end function gcd

    recursive function digit_sum(n) result(s)
        integer, intent(in) :: n
        integer :: s
        if (n < 10) then
            s = n
        else
            s = mod(n, 10) + digit_sum(n / 10)
        end if
    end function digit_sum

    recursive function fib(n) result(f)
        integer, intent(in) :: n
        integer :: f
        calls = calls + 1
        if (n < 2) then
            f = n
        else
            f = fib(n - 1) + fib(n - 2)
        end if
    end function fib

    function fib_loop(n) result(f)
        integer, intent(in) :: n
        integer :: f, a, b, t, i
        a = 0
        b = 1
        do i = 1, n
            t = a + b
            a = b
            b = t
        end do
        f = a
    end function fib_loop

    recursive subroutine hanoi(n, src, dst, spare)
        integer, intent(in) :: n
        character(len=*), intent(in) :: src, dst, spare
        if (n == 0) return                        ! base case: nothing to move
        call hanoi(n - 1, src, spare, dst)
        print '(A, I0, 4A)', "  move disk ", n, " from ", src, " to ", dst
        call hanoi(n - 1, spare, dst, src)
    end subroutine hanoi
end program recursion_demo
```

Output:

```
factorial( 0) = 1
factorial( 5) = 120
factorial(10) = 3628800
factorial(15) = 1307674368000
factorial(20) = 2432902008176640000
 gcd(48, 18)       :           6
 digit_sum(12345)  :          15
 fib(20) recursive :        6765  using       21891 calls
 fib(20) with loop :        6765
 Towers of Hanoi with 3 disks:
  move disk 1 from A to C
  move disk 2 from A to B
  move disk 1 from C to B
  move disk 3 from A to C
  move disk 1 from B to A
  move disk 2 from B to C
  move disk 1 from A to C
```

Computing `fib(20)` the naive way takes 21,891 calls to produce a number that a 20-step loop finds with ease. A subroutine can be recursive too, as `hanoi` shows: to move `n` disks it moves `n-1` out of the way, moves the biggest disk, then moves the `n-1` back on top.

---

## 17.2 `pure` and `elemental`

### `pure` procedures

A **pure** procedure is one that has **no side effects**: its only effect is to compute its result from its arguments. Declare it by adding `pure` in front of `function` or `subroutine`:

```fortran
pure function square(x) result(y)
    real, intent(in) :: x
    real :: y
    y = x * x
end function square
```

The compiler enforces the promise. Inside a pure procedure you may **not**:

- change a variable that belongs to somebody else (a module variable, a host variable, or an argument that is `intent(in)`). All dummy arguments of a pure function must be declared `intent(in)`;
- do input or output (`print`, `read`, or `write` to an external unit);
- use `stop`, or give a local variable the `save` attribute (Lesson 18);
- call any procedure that is not itself pure.

A violation is a compile error, for example:

```
Error: PRINT statement at (1) not allowed within PURE procedure
```

Why accept these limits? Because pure procedures are **safe and predictable**:

- The same arguments always give the same result.
- They can be used in places that need that guarantee, in particular inside `do concurrent` loops (Lesson 10.5), where the compiler may run iterations in parallel or in any order.
- The compiler can optimize more aggressively, since it knows nothing else is affected.
- They are easier to test and to reason about.

### `elemental` procedures

An **elemental** procedure is written for **scalar** arguments but can be called with arrays. When it is called with arrays, it is applied to each element, and the result has the same shape as the arrays. This is exactly how the intrinsic functions `sqrt`, `sin`, and `abs` behave (Lesson 12.1).

```fortran
elemental function to_celsius(f) result(c)
    real, intent(in) :: f
    real :: c
    c = (f - 32.0) * 5.0 / 9.0
end function to_celsius
```

Now `to_celsius(212.0)` gives a scalar and `to_celsius(temps)` gives an array of results, with no loop and no second version of the function. The rules:

- Every dummy argument and the result must be **scalars**.
- An elemental procedure is automatically `pure`, and so follows all the rules above.
- If several arguments are arrays, they must all have the same shape. Scalars may be mixed in freely, and are used with every element.
- It works with any rank: vectors, matrices, and beyond.

### Allowing side effects: `impure elemental`

Sometimes you want element-wise behavior with side effects, such as printing each element. Fortran 2008 allows this if you say so explicitly with `impure elemental`. The elements are then processed in order. Such a procedure loses the safety guarantees of `pure`, and cannot be used in a `do concurrent` loop.

### Example

```fortran
program pure_demo
    use, intrinsic :: iso_fortran_env, only: dp => real64
    implicit none
    integer :: i
    real(dp) :: f(4), c(4)

    f = [32.0_dp, 50.0_dp, 77.0_dp, 212.0_dp]

    print *, "scalar call  :", to_celsius(100.0_dp)
    print *, "array call   :", to_celsius(f)
    print *, "clamped      :", clamp(f, 40.0_dp, 100.0_dp)

    do concurrent (i = 1:4)
        c(i) = to_celsius(f(i))
    end do
    print *, "do concurrent:", c

    print *, "poly(2)      :", horner([1.0_dp, 2.0_dp, 3.0_dp], 2.0_dp)
    call show([10, 20, 30])
contains
    elemental function to_celsius(t) result(r)
        real(dp), intent(in) :: t
        real(dp) :: r
        r = (t - 32.0_dp) * 5.0_dp / 9.0_dp
    end function to_celsius

    elemental function clamp(x, lo, hi) result(y)
        real(dp), intent(in) :: x, lo, hi
        real(dp) :: y
        y = min(max(x, lo), hi)
    end function clamp

    pure function horner(coef, x) result(y)       ! coef(1) + coef(2)*x + coef(3)*x**2 ...
        real(dp), intent(in) :: coef(:), x
        real(dp) :: y
        integer :: k
        y = coef(size(coef))
        do k = size(coef) - 1, 1, -1
            y = y * x + coef(k)
        end do
    end function horner

    impure elemental subroutine show(n)
        integer, intent(in) :: n
        print '(A, I0)', "  element: ", n
    end subroutine show
end program pure_demo
```

Output:

```
 scalar call  :   37.777777777777779     
 array call   :   0.0000000000000000        10.000000000000000        25.000000000000000        100.00000000000000     
 clamped      :   40.000000000000000        50.000000000000000        77.000000000000000        100.00000000000000     
 do concurrent:   0.0000000000000000        10.000000000000000        25.000000000000000        100.00000000000000     
 poly(2)      :   17.000000000000000     
  element: 10
  element: 20
  element: 30
```

`clamp` mixes arrays and scalars: the array `f` is combined with the scalars `40` and `100` for every element. `horner` is pure but not elemental, because it takes an array argument and returns one number. It evaluates `1 + 2x + 3x**2` at `x = 2` using a scheme that needs no powers. Purity is what allows both functions to be called from the `do concurrent` loop and similar places.

---

## 17.3 Passing Arrays and Procedures as Arguments

### Passing arrays

There are three ways to declare an array dummy argument:

| Kind | Declaration | The procedure learns the size from... |
|------|-------------|----------------------------------------|
| **Assumed-shape** | `real :: x(:)` | The array that was passed (`size(x)`) |
| **Explicit-shape** | `real :: x(n)` | A separate argument `n` |
| **Assumed-size** (legacy) | `real :: x(*)` | Nowhere. The size is unknown and unchecked. |

Use **assumed-shape** for new code. It is the most convenient and safest, it works on slices (Lesson 11.4), and the procedure can ask for the size and bounds with `size`, `shape`, `lbound`, and `ubound`. Its one requirement is an explicit interface (Lesson 16.4), which module procedures provide.

Explicit-shape arguments appear in older code and when a procedure needs the dimensions as separate numbers. Assumed-size arrays are found only in legacy code (Lesson 42), and should be avoided.

Other array features covered earlier carry over:

- **Array results.** A function may return an array whose size depends on its arguments, as in `real :: r(size(a, 1))`.
- **Allocatable dummies.** A dummy declared `real, allocatable, intent(out) :: x(:)` lets the procedure **create** the array: it calls `allocate` inside, and the caller's array is allocated when it returns (Lesson 13). With `intent(out)`, an array that was allocated on entry is deallocated first.

### Passing procedures

A procedure can be an **argument** to another procedure. This lets you write general routines, such as a numerical integrator, that work for any function the caller supplies. The receiving procedure needs to know what *kind* of procedure it will be given, so you describe its interface once with an **abstract interface** (a template that is not tied to any particular procedure), and then declare the dummy argument with `procedure(...)`:

```fortran
abstract interface
    function func(x) result(y)
        import :: dp                 ! make the module's dp visible in here
        real(dp), intent(in) :: x
        real(dp) :: y
    end function func
end interface

function trapezoid(f, a, b, n) result(area)
    procedure(func) :: f            ! f is any function matching func
    ...
```

Two details to note:

- Inside an interface block, names from the surrounding module are **not** visible automatically, which is why the `import` statement is needed to use `dp`.
- Any function with the same argument and result types as `func` can be passed as an actual argument. The compiler checks that it matches.

The trapezoid rule approximates an integral by adding up thin trapezoids:

```
area  ≈  h * [ f(a)/2 + f(a+h) + f(a+2h) + ... + f(b-h) + f(b)/2 ],     h = (b - a) / n
```

### Example

```fortran
module calculus
    use, intrinsic :: iso_fortran_env, only: dp => real64
    implicit none
    private
    public :: func, trapezoid, apply_twice

    abstract interface
        function func(x) result(y)
            import :: dp
            real(dp), intent(in) :: x
            real(dp) :: y
        end function func
    end interface
contains
    function trapezoid(f, a, b, n) result(area)
        procedure(func) :: f
        real(dp), intent(in) :: a, b
        integer, intent(in) :: n
        real(dp) :: area, h
        integer :: i

        h = (b - a) / n
        area = 0.5_dp * (f(a) + f(b))
        do i = 1, n - 1
            area = area + f(a + i * h)
        end do
        area = area * h
    end function trapezoid

    function apply_twice(f, x) result(y)
        procedure(func) :: f
        real(dp), intent(in) :: x
        real(dp) :: y
        y = f(f(x))
    end function apply_twice
end module calculus

module curves
    use, intrinsic :: iso_fortran_env, only: dp => real64
    implicit none
    private
    public :: square, wave
contains
    function square(x) result(y)
        real(dp), intent(in) :: x
        real(dp) :: y
        y = x * x
    end function square

    function wave(x) result(y)
        real(dp), intent(in) :: x
        real(dp) :: y
        y = sin(x)
    end function wave
end module curves

program procedure_args
    use, intrinsic :: iso_fortran_env, only: dp => real64
    use calculus
    use curves
    implicit none
    real(dp), parameter :: pi = 3.141592653589793_dp
    real :: v(5) = [1.0, 2.0, 3.0, 4.0, 5.0], m(2, 3)
    real, allocatable :: r(:)

    print *, "integral of x**2 on [0,1]  :", trapezoid(square, 0.0_dp, 1.0_dp, 1000)
    print *, "integral of sin on [0,pi]  :", trapezoid(wave, 0.0_dp, pi, 1000)
    print *, "square applied twice to 3  :", apply_twice(square, 3.0_dp)

    m = reshape([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], [2, 3])
    print *, "explicit-shape sum         :", sum_explicit(5, v)
    print *, "assumed-shape on v(2:4)    :", sum_assumed(v(2:4))
    print *, "row sums of matrix         :", row_sums(m)
    call make_range(4, r)
    print *, "array created by procedure :", r
contains
    function sum_explicit(n, x) result(s)
        integer, intent(in) :: n
        real, intent(in) :: x(n)
        real :: s
        s = sum(x)
    end function sum_explicit

    function sum_assumed(x) result(s)
        real, intent(in) :: x(:)
        real :: s
        s = sum(x)
    end function sum_assumed

    function row_sums(a) result(r)
        real, intent(in) :: a(:, :)
        real :: r(size(a, 1))
        r = sum(a, dim=2)
    end function row_sums

    subroutine make_range(n, x)
        integer, intent(in) :: n
        real, allocatable, intent(out) :: x(:)
        integer :: k
        allocate(x(n))
        x = [(real(k), k = 1, n)]
    end subroutine make_range
end program procedure_args
```

Output:

```
 integral of x**2 on [0,1]  :  0.33333349999999995     
 integral of sin on [0,pi]  :   1.9999983550656624     
 square applied twice to 3  :   81.000000000000000     
 explicit-shape sum         :   15.0000000    
 assumed-shape on v(2:4)    :   9.00000000    
 row sums of matrix         :   9.00000000       12.0000000    
 array created by procedure :   1.00000000       2.00000000       3.00000000       4.00000000    
```

The functions `square` and `wave` live in a module, so they have explicit interfaces and can be passed safely. (With `gfortran`, passing an *internal* procedure as an argument can make the linker warn about an executable stack, so module procedures are the better choice for this.) The integrator `trapezoid` knows nothing about squares or sines. It simply calls whatever function it was handed, and because the same code serves every function, numerical libraries are built this way. Lesson 30 uses the idea for root finding and ODE solvers.

---

[Previous](./[16]-Modules.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[18]-Scope-and-Lifetime.md)
