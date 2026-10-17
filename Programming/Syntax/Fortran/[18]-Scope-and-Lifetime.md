[Previous](./[17]-Recursion-and-Pure-Procedures.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[19]-Derived-Types.md)

*Procedures & Modules*

# Lesson 18 - Scope, `save`, and Variable Lifetime

Two questions come up about every variable: *where* in the program can its name be used, and *when* does it exist and keep its value? The first is called **scope**, the second **lifetime**. Getting them wrong causes some of the most puzzling bugs, such as a counter that unexpectedly keeps counting between calls, or a variable that is modified by a procedure that never mentioned it. This lesson explains both ideas, the `save` attribute, the surprising effect of initializing a local variable, and the `block` construct for keeping variables as local as possible.

---

## 18.1 What Is Scope?

The **scope** of a name is the part of the program where that name can be used. Fortran divides a program into **scoping units**, and a variable declared in one unit is, by default, **local** to it: other units cannot see it.

The main scoping units are:

| Scoping unit | Where it appears |
|--------------|------------------|
| Main program | `program ... end program` |
| Module | `module ... end module` |
| Procedure | Each `subroutine` or `function`, whether external, internal, or in a module |
| Block | A `block ... end block` construct (18.5) |
| Interface body | The declarations inside an `interface` block (Lesson 16.4) |

A consequence of local scope is that **two procedures can use the same names independently**. A variable `i` or `total` in one subroutine has nothing to do with a variable of the same name in another:

```fortran
subroutine first()
    integer :: i            ! this i belongs to first
    ...
end subroutine first

subroutine second()
    integer :: i            ! a different i, unrelated to the one in first
    ...
end subroutine second
```

This is what makes it safe to write procedures separately and combine them later.

### How a name can reach a unit

A procedure can use a name in exactly four ways:

| Mechanism | Meaning |
|-----------|---------|
| **Local declaration** | The name is declared in the procedure itself |
| **Argument association** | The name is a dummy argument, standing for what the caller passed (Lesson 15) |
| **Use association** | The name was imported from a module with `use` (Lesson 16) |
| **Host association** | The name belongs to the surrounding program, module, or procedure (18.2) |

---

## 18.2 Local Variables and Host Association

An **internal procedure** (one placed after `contains` in a program, module, or another procedure) lives inside a **host**. It automatically sees every variable and procedure of its host. This is called **host association**, and it works without any argument or `use` statement.

```fortran
program host
    implicit none
    integer :: x
    ...
contains
    subroutine inner()
        x = x + 1          ! no declaration here: this is the host's x
    end subroutine inner
end program host
```

Host association is convenient, but it is also a way to change a variable far from where it was declared. A reader looking at `inner` cannot tell where `x` comes from without checking the host.

### Shadowing

If an internal procedure declares a variable with the **same name** as one in its host, the local one **hides** (shadows) the host's. Inside the procedure, the name means the new variable, and the host's variable is untouched:

```fortran
subroutine shadows()
    integer :: x           ! a new, local x: the host's x is hidden here
    x = 999                ! does not affect the host
end subroutine shadows
```

### The classic accident

Combined with implicit typing (Lesson 4.3), host association is a trap: forget to declare a local counter such as `i`, and a procedure might silently use and change a host variable of the same name. Two habits prevent it: always write `implicit none` (which is inherited by internal procedures), and **declare every variable you use in each procedure**, even loop counters.

### Example

```fortran
program scope_demo
    implicit none
    integer :: x, y

    x = 10
    y = 1
    print *, "start            : x, y =", x, y
    call uses_host()
    print *, "after uses_host  : x, y =", x, y
    call shadows()
    print *, "after shadows    : x, y =", x, y
contains
    subroutine uses_host()
        x = x + 1                  ! host association: changes the host's x
    end subroutine uses_host

    subroutine shadows()
        integer :: x               ! hides the host's x
        x = 999
        y = y + 1                  ! no local y, so this is the host's y
        print *, "inside shadows   : local x =", x
    end subroutine shadows
end program scope_demo
```

Output:

```
 start            : x, y =          10           1
 after uses_host  : x, y =          11           1
 inside shadows   : local x =         999
 after shadows    : x, y =          11           2
```

`uses_host` changed the program's `x`. In `shadows`, the assignment `x = 999` went to the local variable, so the program's `x` stayed at 11, but `y` had no local twin and was modified.

### Module variables

Variables declared in a module are reached by **use association**. Every unit that uses the module shares the very same variable (Lesson 16.2). Like host association, this makes it possible to change a variable from distant places, so keep module variables few, and mark them `private` or `protected` unless they truly need to be public.

> **Guideline:** pass information through **arguments** whenever you can. Procedures that depend only on their arguments are easier to understand, to test, and to reuse.

---

## 18.3 Variable Lifetime

The **lifetime** of a variable is the time during the run when it actually exists in memory. Variables fall into three groups.

### Automatic variables

Ordinary local variables of a procedure are **automatic**: they are created each time the procedure is entered and **destroyed when it returns**. Two consequences:

- They **do not remember** anything between calls. The next call starts with fresh variables.
- A new automatic variable has an **undefined** value, usually leftover memory. It is not zero (Lesson 5.3).

Each call has its own copies, which is also what makes recursion work (Lesson 17.1).

A local array whose size depends on an argument is called an **automatic array**. It is created on entry with the requested size and vanishes on exit:

```fortran
function sum_squares(n) result(s)
    integer, intent(in) :: n
    integer :: work(n)               ! automatic array: size comes from the argument n
    ...
```

Automatic arrays usually live on the **stack**, a limited area of memory. A very large one can overflow the stack and crash the program. For big arrays, use an `allocatable` array (Lesson 13) instead.

### Static variables

A **static** variable exists for the **entire run** of the program and keeps its value. The following are static:

- variables of the main program,
- variables declared in a module,
- local variables with the `save` attribute (18.4), including those that are initialized in their declaration (18.5).

### Allocatable variables

An allocatable array exists from its `allocate` (or its automatic allocation, Lesson 13.3) until it is deallocated. A *local* allocatable that is still allocated when its procedure ends is **deallocated automatically**, unless it has `save`.

### Summary

| Variable | Created | Destroyed | Remembers value between calls? |
|----------|---------|-----------|-------------------------------|
| Local, no `save` | On entry to the procedure | On exit | No |
| Local with `save` | At program start | At program end | Yes |
| Local, initialized in its declaration | At program start | At program end | **Yes** (implicit `save`) |
| Main program or module variable | At program start | At program end | Yes |
| Local `allocatable` | At `allocate` | At `deallocate`, or on exit | No |
| Automatic array | On entry | On exit | No |

---

## 18.4 The `save` Attribute

Add `save` to a local variable to give it a lifetime that matches the whole program, so that it **keeps its value from one call to the next**:

```fortran
integer, save :: calls = 0
```

The initial value is set **once**, before the program starts, and not at every call. The most common uses are:

- **Counters**, such as the number of times a procedure has been called.
- **First-call flags**, which run some setup code only once.
- **Cached results**, computed on the first call and then reused.

```fortran
logical, save :: first_call = .true.
if (first_call) then
    ! one-time setup
    first_call = .false.
end if
```

### Rules and cautions

- A bare `save` statement on its own, with no list, saves **every** variable in the unit. This is a habit from old Fortran, and you should avoid it. Write `save` on exactly the variables that need it.
- Variables of the main program and of a module behave as if they have `save` already, so it is not needed there.
- A `pure` procedure cannot have saved local variables, because they would be a side effect (Lesson 17.2).
- A procedure with saved variables has a hidden **memory**: calling it twice with the same arguments can give different results. This makes it harder to test and to understand. It also makes it unsafe to use from several threads at once (Lesson 36), and all recursive calls share one copy.

Use `save` sparingly. If a procedure needs to keep information, it is often clearer to keep it in a variable owned by the caller and pass it as an `intent(inout)` argument.

### Example

```fortran
program save_demo
    implicit none
    integer :: k

    do k = 1, 3
        call greet()
    end do
    print *, "sum of squares 1..4:", sum_squares(4)
contains
    subroutine greet()
        logical, save :: first_call = .true.
        integer, save :: calls = 0

        calls = calls + 1
        if (first_call) then
            print *, "(one-time setup done)"
            first_call = .false.
        end if
        print '(A, I0)', "hello, call number ", calls
    end subroutine greet

    function sum_squares(n) result(s)
        integer, intent(in) :: n
        integer :: work(n)                ! automatic array
        integer :: s, i
        work = [(i * i, i = 1, n)]
        s = sum(work)
    end function sum_squares
end program save_demo
```

Output:

```
 (one-time setup done)
hello, call number 1
hello, call number 2
hello, call number 3
 sum of squares 1..4:          30
```

The setup message appeared only once, because `first_call` kept its new value `.false.` between calls. `work` exists only while `sum_squares` is running.

---

## 18.5 Initialization, Implicit `save`, and `block`

### The implicit `save` trap

Lesson 5.3 warned that an initial value in a declaration is set **once**. For a local variable of a procedure, this has an important side effect: **the variable automatically becomes `save`**. The initialization does not run at each call. It is not like `integer :: n = 0` in C.

Compare these two ways of starting a counter at zero:

```fortran
integer :: total = 0        ! set once; total is implicitly saved
integer :: total             ! then, in the executable part:
total = 0                    ! reset at every call
```

If you want a fresh value at every call, assign it in the executable part of the procedure.

### Example

```fortran
program init_trap
    implicit none
    integer :: k

    print '(2A12)', "reset", "initialized"
    do k = 1, 3
        call counters()
    end do
contains
    subroutine counters()
        integer :: reset
        integer :: initialized = 0       ! implicitly saved!

        reset = 0                        ! runs at every call
        reset = reset + 1
        initialized = initialized + 1
        print '(2I12)', reset, initialized
    end subroutine counters
end program init_trap
```

Output:

```
       reset initialized
           1           1
           1           2
           1           3
```

`reset` shows 1 every time, as most beginners expect. The initialized variable counts upward, because its `= 0` was applied only once, at the start of the program. Named constants declared with `parameter` are not affected, since they never change.

### The `block` construct

Fortran 2008 added the `block` construct, which creates a small scoping unit of its own **in the middle of the executable code**. Variables declared inside a block exist only there:

```fortran
block
    integer :: i                 ! declarations come first in the block
    real :: tmp
    ! statements
end block
```

Blocks are useful for temporary variables that belong to one step of the work, so you do not need to declare them at the top of the program. Everything declared inside disappears at `end block`, and the names are free to be used again afterwards. Local variables of a block are automatic, so they are fresh each time the block is entered.

A block can be **named**, and then `exit` followed by the block name leaves it early, which is a tidy alternative to a deeply nested `if`:

```fortran
check: block
    ...
    if (problem) exit check
    ...
end block check
```

### Example

```fortran
program block_demo
    implicit none
    integer :: n

    n = 5
    block
        integer :: i, sq                 ! exist only inside this block
        do i = 1, n
            sq = i * i
        end do
        print *, "last square computed:", sq
    end block
    ! i and sq cannot be used here: they are gone

    check: block
        real :: ratio
        ratio = 1.0 / 4.0
        if (ratio < 0.5) exit check      ! leave the block early
        print *, "this line is skipped"
    end block check

    print *, "done"
end program block_demo
```

Output:

```
 last square computed:          25
 done
```

### Good habits for scope and lifetime

| Habit | Why |
|-------|-----|
| Declare variables in the smallest scope that works | Fewer places can go wrong |
| Always use `implicit none`, and declare every local variable | Prevents accidental host association and typos |
| Pass data through `intent`-labelled arguments | The flow of data is visible in the code |
| Set values in the executable part, not in declarations | Avoids the implicit `save` surprise |
| Use `save` only when the procedure truly needs memory, and comment it | Hidden state is hard to debug |
| Keep module variables `private` or `protected` when possible | Limits who can change shared data |
| Use `allocatable` for large arrays | Avoids stack overflow, and is cleaned up automatically |

With procedures, modules, and variable lifetime in hand, you are ready for the next step: grouping related variables into your own **derived types** in Lesson 19.

---

[Previous](./[17]-Recursion-and-Pure-Procedures.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[19]-Derived-Types.md)
