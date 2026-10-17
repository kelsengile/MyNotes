[Previous](./[12]-Array-Operations.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[14]-Strings.md)

*Arrays & Strings*

# Lesson 13 - Allocatable Arrays & Dynamic Memory (`allocate`, `deallocate`)

The arrays in Lesson 11 had their size fixed in the source code. But often you do not know how big an array must be until the program is running: it depends on a file, on user input, or on an earlier calculation. **Allocatable arrays** solve this. You declare the array without a size and then request memory for it when the size is known. This lesson shows how, how to handle failures, how Fortran can resize arrays automatically, and how allocatables differ from pointers.

---

## 13.1 Why Dynamic Arrays?

A fixed-size array forces an unpleasant choice:

```fortran
real :: data(1000)       ! what if there are 1001 values? what if only 10?
```

If you guess too small, the program fails. If you guess too large, you waste memory, and very large fixed arrays can even crash the program because they do not fit in the limited area of memory used for local variables (the *stack*).

An **allocatable array** postpones the decision. You declare the **rank** (number of dimensions) with the `allocatable` attribute and a colon `:` for each dimension, and leave the extents blank:

```fortran
real, allocatable :: v(:)          ! a vector, size not yet known
real, allocatable :: m(:,:)        ! a matrix, sizes not yet known
integer, allocatable :: grid(:,:,:)
```

Such an array is said to have **deferred shape**. Only the rank is fixed in the declaration. The extents are chosen later by `allocate`.

### Allocation status

An allocatable array is always in one of two states:

| State | Meaning |
|-------|---------|
| **Not allocated** | It has no memory yet. Using its elements is an error. This is its state at the start. |
| **Allocated** | It has memory and a shape. It can be used like any other array. |

The logical function `allocated(a)` tells you which state an array is in:

```fortran
if (.not. allocated(v)) allocate(v(100))
```

---

## 13.2 `allocate` and `deallocate`

### Allocating

`allocate` reserves the memory and sets the shape. The extents can be any integer expressions, so they may come from variables:

```fortran
allocate(v(n))               ! indices 1..n
allocate(m(rows, cols))      ! a rows x cols matrix
allocate(t(0:n))             ! custom bounds, as in Lesson 11.1
allocate(a(10), b(20))       ! several arrays at once
```

Newly allocated arrays hold **undefined values**, not zeros. Assign something before you read them:

```fortran
allocate(v(n))
v = 0.0
```

### Copying the shape and values: `source` and `mold`

Two options copy from an existing array (Fortran 2003):

```fortran
allocate(b, source=a)        ! b gets a's shape AND a copy of its values
allocate(c, mold=a)          ! c gets a's shape only (values undefined)
```

### Releasing memory

`deallocate` returns the memory. Afterwards the array is back in the "not allocated" state and can be allocated again with a different size:

```fortran
deallocate(v)
```

Two rules prevent common errors:

- Allocating an array that is **already allocated** is an error. Check with `allocated`, or deallocate first.
- Deallocating an array that is **not allocated** is also an error.

The good news: an allocatable array that is a **local variable** of a procedure (Lesson 15) is deallocated **automatically** when the procedure ends. The same is true for those in the main program when it ends. You do not have to call `deallocate` for every array, and memory leaks of the kind found in C are not possible with allocatables. Explicit `deallocate` is useful when you want to free a large array early.

### Handling failure: `stat` and `errmsg`

Memory is finite. If an `allocate` fails and you did nothing to handle it, the program stops immediately. Adding `stat=` lets you take control instead: `stat` is set to 0 on success and to a non-zero value on failure. The optional `errmsg=` receives a text description (the wording depends on the compiler):

```fortran
allocate(v(n), stat=ierr, errmsg=msg)
if (ierr /= 0) then
    print *, "Could not allocate: ", trim(msg)
    error stop 1
end if
```

`error stop` (Fortran 2008) ends the program and reports a failure to the operating system. Lesson 26 covers error handling in depth. The same `stat=` and `errmsg=` options work with `deallocate`.

### Example

```fortran
program allocate_demo
    use, intrinsic :: iso_fortran_env, only: int64
    implicit none
    integer :: n, i, ierr
    real, allocatable :: v(:), m(:, :), t(:), w(:), big(:)
    character(len=100) :: msg

    print *, "allocated(v) at start:", allocated(v)

    n = 5                          ! could just as well be read from the keyboard
    allocate(v(n))
    v = [(real(i)**2, i = 1, n)]
    print *, "allocated(v) now     :", allocated(v)
    print *, "size(v) =", size(v), "  v =", v

    allocate(m(2, n))
    m = 0.0
    print *, "shape(m) =", shape(m)

    allocate(t(0:3))
    t = 1.0
    print *, "t bounds:", lbound(t, 1), ubound(t, 1)

    allocate(w, source=v)
    print *, "copy w   :", w

    deallocate(v)
    print *, "allocated(v) after deallocate:", allocated(v)

    allocate(big(2_int64**50), stat=ierr, errmsg=msg)
    if (ierr /= 0) then
        print *, "Huge allocation failed (as expected), stat /= 0:", ierr /= 0
    end if
end program allocate_demo
```

Output:

```
 allocated(v) at start: F
 allocated(v) now     : T
 size(v) =           5   v =   1.00000000       4.00000000       9.00000000       16.0000000       25.0000000    
 shape(m) =           2           5
 t bounds:           0           3
 copy w   :   1.00000000       4.00000000       9.00000000       16.0000000       25.0000000    
 allocated(v) after deallocate: F
 Huge allocation failed (as expected), stat /= 0: T
```

The last request asks for about eight petabytes, so it fails. Because the program used `stat=`, it can report this and carry on instead of crashing.

---

## 13.3 Automatic Reallocation

Since Fortran 2003, assigning a whole array to an allocatable array **reallocates it automatically** if the shapes differ (or allocates it if it was not allocated at all). You do not need to call `allocate` first:

```fortran
real, allocatable :: a(:)

a = [1.0, 2.0, 3.0]         ! a was unallocated: it is now allocated with size 3
a = [a, 4.0, 5.0]           ! grows to size 5 (a new element appended)
a = a(2:4)                  ! shrinks to size 3
```

This makes allocatable arrays behave a little like the lists of Python. All the elements of the right-hand side are computed **before** the old array is replaced, so using `a` on both sides, as in `a = [a, 4.0, 5.0]`, is safe.

### When it does *not* happen

Automatic reallocation applies only when you assign to the **whole array**. If the left side is written as a section, such as `a(:) = ...`, no reallocation happens and the shapes must already match:

```fortran
a = b          ! whole array: reallocates a to b's shape if needed
a(:) = b       ! section: a must already have b's shape
```

Writing `a(:) = b` is therefore a way to say "I expect the shapes to match, and I want an error if they do not". Compilers can disable automatic reallocation with `-fno-realloc-lhs`, but modern code assumes it is on (it is the default in `gfortran`).

### Growing an array efficiently: `move_alloc`

Appending one element at a time with `a = [a, x]` copies the entire array on every step, which becomes slow for big arrays. The standard remedy is to grow in big jumps, for example by doubling the **capacity** each time it fills up. The intrinsic `move_alloc(from, to)` helps here: it transfers the memory of `from` to `to` **without copying**, leaving `from` deallocated.

```fortran
allocate(tmp(new_capacity))
tmp(1:count) = list(1:count)       ! copy the used part once
call move_alloc(tmp, list)         ! list now owns the new memory; tmp is empty
```

### Example

```fortran
program realloc_demo
    implicit none
    integer :: i, count, capacity
    real, allocatable :: a(:)
    integer, allocatable :: list(:), tmp(:)

    a = [1.0, 2.0, 3.0]
    print *, "size", size(a), ":", a
    a = [a, 4.0, 5.0]
    print *, "size", size(a), ":", a
    a = a(2:4)
    print *, "size", size(a), ":", a

    ! growing a list by doubling its capacity
    capacity = 2
    allocate(list(capacity))
    count = 0
    do i = 1, 10
        if (count == capacity) then
            capacity = 2 * capacity
            allocate(tmp(capacity))
            tmp(1:count) = list(1:count)
            call move_alloc(tmp, list)
            print *, "capacity grown to", capacity
        end if
        count = count + 1
        list(count) = i * i
    end do
    print *, "final capacity", capacity, " used", count
    print *, "list:", list(1:count)
end program realloc_demo
```

Output:

```
 size           3 :   1.00000000       2.00000000       3.00000000    
 size           5 :   1.00000000       2.00000000       3.00000000       4.00000000       5.00000000    
 size           3 :   2.00000000       3.00000000       4.00000000    
 capacity grown to           4
 capacity grown to           8
 capacity grown to          16
 final capacity          16  used          10
 list:           1           4           9          16          25          36          49          64          81         100
```

Only three growth steps were needed to store ten values, instead of ten separate copies.

---

## 13.4 Pointers vs Allocatables

Fortran also has **pointers**, and beginners often wonder which one to use. A pointer does not hold data itself. It **refers to** (is *associated with*) data that lives elsewhere. A pointer has the `pointer` attribute, and anything it is to refer to must have the `target` attribute:

```fortran
real, target  :: a(6)
real, pointer :: p(:)

p => a(2:4)         ! "points to" elements 2 to 4 of a (note the =>)
```

The symbol `=>` creates the association. After it, `p` is another name for that part of `a`, so changing `p` changes `a`. Useful pointer tools:

| Tool | Meaning |
|------|---------|
| `p => target` | Associate the pointer with a target |
| `associated(p)` | True if the pointer currently refers to something |
| `nullify(p)` | Make the pointer refer to nothing |
| `allocate(p(n))` | Create new, unnamed memory and point to it |
| `deallocate(p)` | Free that memory (done by hand, always) |

### Example

```fortran
program pointer_demo
    implicit none
    integer :: i
    real, target :: a(6)
    real, pointer :: p(:), q

    a = [(real(i), i = 1, 6)]

    p => a(2:4)
    print *, "associated(p):", associated(p), "  p =", p
    p = p * 10.0
    print *, "a after p = p*10:", a

    q => a(1)
    q = 100.0
    print *, "a after q = 100 :", a

    nullify(p)
    print *, "associated(p) after nullify:", associated(p)

    allocate(p(3))
    p = 1.0
    print *, "p now owns new memory:", p
    deallocate(p)
end program pointer_demo
```

Output:

```
 associated(p): T   p =   2.00000000       3.00000000       4.00000000    
 a after p = p*10:   1.00000000       20.0000000       30.0000000       40.0000000       5.00000000       6.00000000    
 a after q = 100 :   100.000000       20.0000000       30.0000000       40.0000000       5.00000000       6.00000000    
 associated(p) after nullify: F
 p now owns new memory:   1.00000000       1.00000000       1.00000000    
```

### Which one should you choose?

| | `allocatable` | `pointer` |
|---|---------------|-----------|
| Owns its memory | Yes, exclusively | No: may share with other names |
| Freed automatically | Yes, at the end of its scope | **No**: you must `deallocate`, or memory leaks |
| Can become a "dangling" reference | Never | Yes, if its target is freed or goes out of scope |
| Optimization | The compiler knows nothing else can alias it | The compiler must assume other names may touch the same data |
| Can be passed around as an alias for a slice | No | Yes |
| Needed for linked lists and trees | No | Yes (Lesson 21) |

**Rule of thumb: use `allocatable` unless you specifically need a pointer's abilities.** Allocatables are safer, faster, and automatically cleaned up. Reach for pointers when you need an alias to part of an array, or to build linked data structures such as lists and trees, which Lesson 21 covers.

---

[Previous](./[12]-Array-Operations.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[14]-Strings.md)
