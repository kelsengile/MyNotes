[Previous](./[10]-Loops.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[12]-Array-Operations.md)

*Arrays & Strings*

# Lesson 11 - Arrays Fundamentals

So far each variable has held a single value. Real programs usually work with *many* values at once: a hundred temperature readings, a grid of pixels, a table of measurements. An **array** stores a whole collection of values of the same type under one name. Arrays are at the heart of Fortran, and the language was designed around them. This lesson covers how to declare arrays, fill them with values, find individual elements, and cut out pieces called slices.

---

## 11.1 Declaring One- and Multi-Dimensional Arrays

An array is a numbered row (or grid, or block) of boxes. Every box has the same type, and each one is reached by its **index**, a position number:

```
   v      ┌─────┬─────┬─────┬─────┬─────┐
          │ 2.5 │ 1.0 │ 4.0 │ 3.5 │ 0.5 │       a one-dimensional array
          └─────┴─────┴─────┴─────┴─────┘         (a "vector")
 index:      1     2     3     4     5
```

### Declaration

Give the array a size in parentheses after its name, or use the `dimension` attribute (Lesson 5):

```fortran
real :: v(5)                      ! five reals
real, dimension(5) :: w           ! the same thing using dimension
integer :: counts(100)            ! 100 integers
real :: m(3, 4)                   ! a table with 3 rows and 4 columns
real, dimension(3, 4) :: a, b     ! two arrays with the same shape
```

Any type can be used: `integer`, `real`, `complex`, `logical`, or `character`. Sizes can be written as numbers or as named constants (Lesson 5.4). Named constants are preferred, because the size is then written in one place only:

```fortran
integer, parameter :: n = 100
real :: signal(n), filtered(n)
```

### The vocabulary of arrays

| Term | Meaning | For `real :: m(3, 4)` |
|------|---------|-----------------------|
| **Element** | One value inside the array | `m(2, 3)` |
| **Rank** | Number of dimensions | 2 |
| **Extent** | Number of elements along one dimension | 3 along the first, 4 along the second |
| **Shape** | The list of extents | `[3, 4]` |
| **Size** | Total number of elements | 12 |
| **Conformable** | Two arrays with the same shape (or one is a scalar) | needed for `a + b` |

A Fortran array can have a rank up to 15 (the standard before 2008 allowed 7). Rank 1 and 2 are by far the most common.

### Custom bounds

By default the first index is 1. You may choose another **lower bound** by writing `low:high`:

```fortran
real :: t(0:4)                    ! indices 0, 1, 2, 3, 4  (size 5)
integer :: y(-2:2)                ! indices -2, -1, 0, 1, 2
integer :: pop(1990:1994)         ! indexed directly by year
```

This is a feature C and Python do not have. It is handy when the problem has a natural numbering, such as years or distances starting from zero.

### Asking an array about itself

Intrinsic functions report the properties of an array:

| Function | Returns |
|----------|---------|
| `size(a)` | Total number of elements |
| `size(a, dim)` | Extent along dimension `dim` |
| `shape(a)` | An integer array holding all extents |
| `rank(a)` | The number of dimensions (Fortran 2018) |
| `lbound(a)`, `ubound(a)` | Lower and upper bounds (arrays); add `dim` for a single dimension |

### Example

```fortran
program declare_demo
    implicit none
    integer, parameter :: n = 5
    real :: v(n)
    real, dimension(3, 4) :: m
    integer :: pop(1990:1994)
    logical :: flags(3) = .false.

    print *, "v:   size =", size(v), " rank =", rank(v)
    print *, "m:   size =", size(m), " rank =", rank(m)
    print *, "m:   shape =", shape(m)
    print *, "m:   rows =", size(m, 1), " columns =", size(m, 2)
    print *, "pop: lbound =", lbound(pop, 1), " ubound =", ubound(pop, 1)
    print *, "pop: size =", size(pop)
    print *, "flags =", flags
end program declare_demo
```

Output:

```
 v:   size =           5  rank =           1
 m:   size =          12  rank =           2
 m:   shape =           3           4
 m:   rows =           3  columns =           4
 pop: lbound =        1990  ubound =        1994
 pop: size =           5
 flags = F F F
```

Notice that `flags` was given a starting value of `.false.` for all three elements in the declaration, using a single scalar. A scalar given to an array is copied into every element.

> **Warning:** declared arrays that are not given values contain whatever was left in memory, just like ordinary variables (Lesson 5.3). Always fill an array before reading it.

---

## 11.2 Array Constructors and Initialization

An **array constructor** builds an array value from a list. It is written with square brackets (Fortran 2003 and later):

```fortran
v = [1.0, 2.5, 4.0, 3.5, 0.5]
```

Older code writes the same thing as `(/ 1.0, 2.5, 4.0, 3.5, 0.5 /)`. Both are accepted, and this course uses brackets.

### Implied-do loops

Writing out many values is tedious. Inside a constructor you can put an **implied do**, the compact loop from Lesson 10.1:

```fortran
squares = [(i**2, i = 1, 5)]         ! 1 4 9 16 25
countdown = [(i, i = 10, 1, -3)]     ! 10 7 4 1
```

### Giving the constructor a type

All the elements in a constructor must have the same type and kind. Mixing them, as in `[1, 2.5]`, is an error. Put a **type specification** and a double colon at the start to convert every element to one type. The same idea builds an empty (zero-size) array, and it lets strings of different lengths be combined:

```fortran
x = [real :: 1, 2.5, 3]                       ! 1.0 2.5 3.0
names = [character(len=5) :: "Ana", "Ben", "Carla"]
none = [integer :: ]                          ! zero elements
```

### Filling a whole array at once

A scalar assigned to an array fills every element. An array assigned to an array of the same shape copies element by element:

```fortran
v = 0.0          ! every element becomes 0.0
w = v            ! copy
```

### Multi-dimensional arrays with `reshape`

A constructor always produces a one-dimensional array. To make a matrix from it, use `reshape(source, shape)`. By default the values fill the new array **column by column** (see 11.3):

```fortran
m = reshape([1, 2, 3, 4, 5, 6], [2, 3])
```

This is the matrix

```
 1  3  5
 2  4  6
```

Add `order=[2, 1]` to fill row by row instead, which is often the way you would write a matrix on paper.

### Constant arrays

A named constant (`parameter`) can be an array too. This is a good home for fixed tables:

```fortran
real, parameter :: weights(3) = [0.2, 0.3, 0.5]
```

### Example

```fortran
program constructor_demo
    implicit none
    integer :: i, k
    integer :: squares(5), down(4), m(2, 3), r(2, 3)
    real :: x(3)
    real, parameter :: weights(3) = [0.2, 0.3, 0.5]
    character(len=5) :: names(3)

    squares = [(i**2, i = 1, 5)]
    down = [(k, k = 10, 1, -3)]
    x = [real :: 1, 2.5, 3]
    names = [character(len=5) :: "Ana", "Ben", "Carla"]

    print *, "squares:", squares
    print *, "down:   ", down
    print *, "x:      ", x
    print *, "weights:", weights
    print '(3A)', names(1), names(2), names(3)

    m = reshape([1, 2, 3, 4, 5, 6], [2, 3])
    r = reshape([1, 2, 3, 4, 5, 6], [2, 3], order=[2, 1])

    print *, "m (default order, column by column):"
    do i = 1, 2
        print '(3I4)', m(i, :)
    end do
    print *, "r (order=[2,1], row by row):"
    do i = 1, 2
        print '(3I4)', r(i, :)
    end do
end program constructor_demo
```

Output:

```
 squares:           1           4           9          16          25
 down:             10           7           4           1
 x:         1.00000000       2.50000000       3.00000000    
 weights:  0.200000003      0.300000012      0.500000000    
Ana  Ben  Carla
 m (default order, column by column):
   1   3   5
   2   4   6
 r (order=[2,1], row by row):
   1   2   3
   4   5   6
```

The row printing uses `m(i, :)`, a slice that is explained in 11.4.

---

## 11.3 Indexing, Bounds, and Column-Major Order

### Reaching one element

Write the index (or indices) in parentheses. With the default lower bound, the first element is number **1**, not 0:

```fortran
v(1) = 10.0         ! first element
v(5) = 50.0         ! last element of a size-5 array
m(2, 3) = 7.0       ! row 2, column 3
```

For a matrix the convention is `m(row, column)`.

### Staying inside the bounds

Using an index outside the declared range, such as `v(6)` for a size-5 array, is an error. The compiler normally does **not** check this for you, so the program may silently read or overwrite other memory and misbehave in strange ways. This is one of the most dangerous bugs in Fortran (see Lesson 3.3).

During development, compile with `-fcheck=bounds` (included in `-fcheck=all`) and the program stops with a clear message the moment a bad index is used.

When you loop over an array, take the limits from the array itself instead of retyping a number:

```fortran
do i = 1, size(v)             ! works for any size
    v(i) = 2.0 * i
end do
do i = lbound(y, 1), ubound(y, 1)    ! works for custom bounds
    y(i) = i
end do
```

### Column-major order

Memory is a long one-dimensional strip, so a matrix has to be laid out along it somehow. Fortran stores arrays **column by column** ("column-major order"), with the **first** index changing fastest. C and Python do the opposite (row-major).

The 2 x 3 matrix `m` is stored in memory as:

```
 matrix view            memory order
 m(1,1) m(1,2) m(1,3)   m(1,1)  m(2,1)  m(1,2)  m(2,2)  m(1,3)  m(2,3)
 m(2,1) m(2,2) m(2,3)   └─ column 1 ─┘  └─ column 2 ─┘  └─ column 3 ─┘
```

Why does it matter?

- Whole-array operations and `print *, m` always walk the array in memory order, which is column by column.
- Computers fetch memory in chunks, so touching neighbouring memory cells one after another is much faster than jumping around. **In nested loops, make the first index the inner loop** so you move through memory in order:

```fortran
do j = 1, ncols          ! outer loop: columns
    do i = 1, nrows      ! inner loop: rows (first index changes fastest)
        a(i, j) = ...
    end do
end do
```

Swapping the two loops still gives the right answer, but for large arrays it can be several times slower. This is the opposite of what a C programmer would write.

### Example

```fortran
program indexing_demo
    implicit none
    integer :: i, j
    integer :: m(2, 3)
    integer :: y(-2:2)

    do j = 1, 3
        do i = 1, 2
            m(i, j) = 10 * i + j
        end do
    end do

    print *, "m(2,3) =", m(2, 3)
    print *, "matrix:"
    do i = 1, 2
        print '(3I4)', m(i, :)
    end do
    print *, "memory order:", m

    do i = lbound(y, 1), ubound(y, 1)
        y(i) = i * i
    end do
    print *, "y(-2:2) =", y
    print *, "y(0) =", y(0)
end program indexing_demo
```

Output:

```
 m(2,3) =          23
 matrix:
  11  12  13
  21  22  23
 memory order:          11          21          12          22          13          23
 y(-2:2) =           4           1           0           1           4
 y(0) =           0
```

The element `m(i, j)` holds `10*i + j`, so the first digit is the row and the second is the column. Printing the whole array shows the values in memory order: both rows of column 1 first (`11`, `21`), then column 2 (`12`, `22`), and so on.

---

## 11.4 Array Slicing

Often you want a **part** of an array: the first three elements, every second element, one row of a matrix. Fortran lets you name such a part directly. It is called an **array section** (or slice) and is written with a colon:

```
array(start : stop : step)
```

Any of the three parts may be left out. A missing `start` means the lower bound, a missing `stop` means the upper bound, and a missing `step` means 1.

| Section | Meaning |
|---------|---------|
| `a(2:4)` | Elements 2, 3, 4 |
| `a(:3)` | From the start up to element 3 |
| `a(4:)` | From element 4 to the end |
| `a(:)` or `a` | The whole array |
| `a(1::2)` | Every second element: 1, 3, 5, ... |
| `a(5:1:-1)` | Backwards from 5 to 1 |
| `m(2, :)` | All of row 2 (a rank-1 section) |
| `m(:, 3)` | All of column 3 |
| `m(1:2, 2:3)` | A 2 x 2 block in the corner |

A scalar index (`m(2, :)`) removes that dimension from the result, so the row of a matrix is an ordinary vector.

### Using sections

A section can appear anywhere an array can: in an expression, on the left side of an assignment, or as an argument to a procedure.

```fortran
a(2:4) = 0.0                  ! set three elements to zero
b = a(5:1:-1)                 ! reversed copy
total = sum(m(:, 2))          ! sum of column 2
m(1, :) = m(2, :)             ! copy row 2 into row 1
```

Both sides of an assignment must have the same shape, or the right side must be a scalar.

### Overlapping sections are safe

The whole right-hand side of an assignment is evaluated **before** anything is stored. So a statement such as `a(2:5) = a(1:4)` shifts the values along correctly, with no element being overwritten too early.

### Vector subscripts

An **integer array** can be used as an index to pick out elements in any order, including repeats:

```fortran
a([1, 3, 5])         ! elements 1, 3 and 5
a([5, 5, 1])         ! element 5 twice, then element 1
```

Such a selection may be read freely, but it cannot be assigned to if an index repeats.

### Example

```fortran
program slicing_demo
    implicit none
    integer :: i
    integer :: a(8), m(3, 4)

    a = [(10 * i, i = 1, 8)]
    m = reshape([(i, i = 1, 12)], [3, 4])

    print *, "a          :", a
    print *, "a(2:4)     :", a(2:4)
    print *, "a(:3)      :", a(:3)
    print *, "a(6:)      :", a(6:)
    print *, "a(1::2)    :", a(1::2)
    print *, "a(8:1:-3)  :", a(8:1:-3)
    print *, "a([1,3,5]) :", a([1, 3, 5])

    a(2:5) = a(1:4)
    print *, "after a(2:5)=a(1:4):", a

    print *, "m:"
    do i = 1, 3
        print '(4I4)', m(i, :)
    end do
    print *, "row 2      :", m(2, :)
    print *, "column 3   :", m(:, 3)
    print *, "m(1:2,2:3) :", m(1:2, 2:3)
    print *, "sum of column 4:", sum(m(:, 4))

    m(:, 1) = 0
    print *, "column 1 zeroed:", m(:, 1)
end program slicing_demo
```

Output:

```
 a          :          10          20          30          40          50          60          70          80
 a(2:4)     :          20          30          40
 a(:3)      :          10          20          30
 a(6:)      :          60          70          80
 a(1::2)    :          10          30          50          70
 a(8:1:-3)  :          80          50          20
 a([1,3,5]) :          10          30          50
 after a(2:5)=a(1:4):          10          10          20          30          40          60          70          80
 m:
   1   4   7  10
   2   5   8  11
   3   6   9  12
 row 2      :           2           5           8          11
 column 3   :           7           8           9
 m(1:2,2:3) :           4           5           7           8
 sum of column 4:          33
 column 1 zeroed:           0           0           0
```

Look at `m(1:2, 2:3)`: it is a 2 x 2 block, and printing it shows its own elements in memory order (column by column), not the order of the original matrix.

Sections do not copy data. They simply point to part of the original array, which is why assigning to `m(:, 1)` changes `m` itself. Lesson 12 shows how to calculate with whole arrays and sections at once.

---

[Previous](./[10]-Loops.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[12]-Array-Operations.md)
