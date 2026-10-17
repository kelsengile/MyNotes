[Previous](./[9]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[11]-Arrays-Fundamentals.md)

*Core Syntax*

# Lesson 10 - Loops: `do`, `do while`, `exit`, `cycle`

A **loop** repeats a block of statements. Computers are good at repetition, and nearly every useful program contains loops. Fortran has one loop keyword, `do`, which comes in several forms. This lesson covers all of them, including the loop controls `exit` and `cycle`, nested and named loops, and the parallel-friendly `do concurrent`.

---

## 10.1 Counted `do` Loops

Use a counted loop when you know in advance how many times to repeat. The form is:

```fortran
do variable = start, stop [, step]
    ! body
end do
```

The **loop variable** (an integer) starts at `start` and increases by `step` (default 1) after each pass, and the loop ends once it would go beyond `stop`.

```fortran
program do_demo
    implicit none
    integer :: i, total

    do i = 1, 5
        print *, "i =", i
    end do

    do i = 10, 1, -3
        print *, "countdown:", i
    end do

    total = 0
    do i = 1, 100
        total = total + i
    end do
    print *, "Sum 1..100 =", total

    do i = 5, 1
        print *, "never printed"
    end do
    print *, "after loop, i =", i
    print *, (i*i, i = 1, 5)
end program do_demo
```

Output:

```
 i =           1
 i =           2
 i =           3
 i =           4
 i =           5
 countdown:          10
 countdown:           7
 countdown:           4
 countdown:           1
 Sum 1..100 =        5050
 after loop, i =           5
           1           4           9          16          25
```

(`gfortran -Wall` warns that the `do i = 5, 1` loop will run zero times, which is exactly the point of that example.)

### How the loop works

- The number of passes is calculated **once**, before the loop starts: `max(0, (stop - start + step) / step)`. Changing `stop` inside the loop has no effect on how many times it runs.
- A loop whose start is already past the stop runs **zero times** (as `do i = 5, 1` above).
- A **negative step** counts down: `do i = 10, 1, -3` gives 10, 7, 4, 1.
- The step cannot be zero.
- **Never assign to the loop variable inside the body.** The compiler rejects it.
- After a loop finishes normally, the loop variable holds the first value beyond the stop (for `do i = 1, 5` it is 6). Do not rely on this. If you need the value, copy it into another variable.
- Real loop variables (`do x = 0.0, 1.0, 0.1`) were removed from the standard because rounding errors make the pass count unreliable. Use an integer counter and compute `x = 0.1 * i`.

### Implied-do loops

Inside an `print`, `read`, or array constructor you can write a compact loop in parentheses, called an **implied do**:

```fortran
print *, (i*i, i = 1, 5)        ! prints 1 4 9 16 25
```

You will use this with arrays in Lesson 11.

---

## 10.2 `do while`

When you do not know in advance how many passes you need, use `do while`. The loop repeats **as long as** a condition is true. The condition is checked **before** each pass, so the body may run zero times:

```fortran
do while (condition)
    ! body
end do
```

The classic example is the Collatz sequence: from any starting number, halve it if it is even, otherwise triple it and add one, and repeat until you reach 1.

```fortran
program do_while_demo
    implicit none
    integer :: n, steps

    n = 27
    steps = 0
    do while (n /= 1)
        if (mod(n, 2) == 0) then
            n = n / 2
        else
            n = 3 * n + 1
        end if
        steps = steps + 1
    end do
    print *, "Reached 1 after", steps, "steps"
end program do_while_demo
```

Output:

```
 Reached 1 after         111 steps
```

**Warning:** make sure something inside the loop eventually makes the condition false. Otherwise you have an **infinite loop** and the program never ends (press `Ctrl+C` to stop it).

---

## 10.3 `exit` and `cycle`

Two statements give finer control inside any `do` loop:

| Statement | Effect |
|-----------|--------|
| `exit` | Leave the loop completely and continue after `end do` |
| `cycle` | Skip the rest of this pass and jump to the next one |

### The infinite `do`

A `do` with **no** control at all repeats forever, until you leave it with `exit`. This is the most flexible loop, handy when the stopping test sits in the middle of the body:

```fortran
do
    ! ...
    if (done) exit
    ! ...
end do
```

### Example

```fortran
program exit_cycle_demo
    implicit none
    integer :: i, n

    do i = 1, 10
        if (mod(i, 2) == 0) cycle
        if (i > 7) exit
        print *, "odd number:", i
    end do
    print *, "stopped with i =", i

    n = 0
    do
        n = n + 1
        if (n**2 > 50) exit
    end do
    print *, "first n with n**2 > 50:", n
end program exit_cycle_demo
```

Output:

```
 odd number:           1
 odd number:           3
 odd number:           5
 odd number:           7
 stopped with i =           9
 first n with n**2 > 50:           8
```

The first loop skips even numbers with `cycle`, prints odd ones, and leaves when `i` reaches 9 (which is why it prints `9` afterward: `exit` leaves the loop variable at its current value). The second is an infinite loop that stops itself.

---

## 10.4 Named and Nested Loops

A loop can contain another loop. This is called **nesting**. The inner loop runs completely for every pass of the outer loop. Nested loops are the natural way to visit a table or a matrix row by row.

By default `exit` and `cycle` act only on the **innermost** loop. To control an outer loop, give the loops **names**:

```fortran
outer: do i = 1, 4
    inner: do j = 1, 4
        if (j > i) cycle outer     ! jump to the next i
        if (i * j == 9) exit outer ! leave both loops
    end do inner
end do outer
```

A name is a label followed by a colon before `do`. The same name must appear after `end do`. Then `exit outer` or `cycle outer` refers to that loop.

```fortran
program named_loops
    implicit none
    integer :: i, j

    outer: do i = 1, 4
        inner: do j = 1, 4
            if (j > i) cycle outer
            if (i * j == 9) exit outer
            write (*, '(I0,"x",I0,"=",I0,2X)', advance='no') i, j, i*j
        end do inner
    end do outer
    print *
    print *, "finished at i =", i, " j =", j
end program named_loops
```

Output:

```
1x1=1  2x1=2  2x2=4  3x1=3  3x2=6  
 finished at i =           3  j =           3
```

Trace through it: for `i = 1`, the inner loop prints `1x1=1`, then at `j = 2` the test `j > i` is true so `cycle outer` jumps to `i = 2`. This continues until `i = 3` and `j = 3`, where `i * j == 9` and `exit outer` leaves both loops at once.

### Loop-nesting tips

- Use a different loop variable for each level (`i`, `j`, `k`).
- A common convention: the *outer* loop is over rows and the *inner* loop is over columns. Fortran stores arrays by columns, so for arrays the **inner loop should run over the first index** for speed (Lesson 11 explains why).
- Deep nesting is hard to read. Move inner work into a procedure (Lesson 15).

---

## 10.5 `do concurrent`

Fortran 2008 added `do concurrent`, a loop in which you **promise** the compiler that the iterations are independent of each other and can run in any order, or all at once.

```fortran
do concurrent (i = 1:n)
    a(i) = b(i)**2
end do
```

Note the different syntax: the range is written `i = 1:n` inside parentheses, with a colon instead of a comma. An optional **mask** after the range selects which iterations run:

```fortran
do concurrent (i = 1:n, b(i) > 4.0)
    a(i) = 0.0
end do
```

```fortran
program concurrent_demo
    implicit none
    integer, parameter :: n = 8
    integer :: i
    real :: a(n), b(n)

    b = [(real(i), i = 1, n)]
    do concurrent (i = 1:n)
        a(i) = b(i)**2
    end do
    print '(8F6.1)', a

    do concurrent (i = 1:n, b(i) > 4.0)
        a(i) = 0.0
    end do
    print '(8F6.1)', a
end program concurrent_demo
```

Output:

```
   1.0   4.0   9.0  16.0  25.0  36.0  49.0  64.0
   1.0   4.0   9.0  16.0   0.0   0.0   0.0   0.0
```

### Rules for a valid `do concurrent`

- Each iteration must not depend on results from another iteration. A loop like `a(i) = a(i-1) + 1` is **not** allowed, because it needs the previous pass to be finished first.
- No `exit` or `cycle` that leaves the loop is allowed, and you cannot `return` from inside.
- The compiler does not check your promise. If it is broken, the results are undefined.
- Fortran 2018 lets you state which variables are private to each iteration with `local(...)`, and shared with `shared(...)`; `default(none)` forces you to be explicit.

### Why use it?

Because the iterations are independent, the compiler is free to **vectorize** the loop (use SIMD instructions), run it across several CPU cores, or even offload it to a GPU. With the right compiler flags (for example `-ftree-parallelize-loops=4` in `gfortran`, or `-fopenmp` with some other compilers), the same source can take advantage of parallel hardware. Lessons 36 and 39 revisit this.

For everyday sequential work, the plain counted `do` loop is just as good.

---

### Summary of loop forms

| Form | Use when |
|------|----------|
| `do i = a, b [, step]` | The number of passes is known |
| `do while (cond)` | Repeat while a condition holds; may run zero times |
| `do` ... `if (cond) exit` | The test belongs in the middle or end of the body |
| `outer: do` ... `exit outer` | Leaving or skipping outer loops from within inner ones |
| `do concurrent (i = 1:n)` | The passes are independent and may run in parallel |

---

[Previous](./[9]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[11]-Arrays-Fundamentals.md)