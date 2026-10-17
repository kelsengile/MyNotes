[Previous](./[11]-Arrays-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[13]-Allocatable-Arrays.md)

*Arrays & Strings*

# Lesson 12 - Whole-Array Operations & Intrinsics (`sum`, `matmul`, `where`)

In most languages, adding two lists of numbers needs a loop. In Fortran you simply write `c = a + b`. Whole-array operations make numerical code short, readable, and easy for the compiler to speed up. This lesson shows how arithmetic works on entire arrays, the built-in functions that summarize them, the matrix routines `matmul` and `transpose`, and how to apply operations to only the elements you choose.

---

## 12.1 Element-wise Arithmetic

Every arithmetic operator from Lesson 7 can be used on whole arrays. The operation is applied **element by element**, and the result is an array of the same shape:

```fortran
c = a + b          ! c(i) = a(i) + b(i) for every i
c = a * b          ! element-by-element product, NOT a matrix product
c = a ** 2         ! square every element
c = 2.0 * a + 1.0  ! a scalar is combined with every element
```

The rules:

- Arrays that are combined must be **conformable**: they must have the same shape (Lesson 11.1). A scalar is conformable with anything, and is treated as if it were copied into every element.
- The left side of the assignment must have the same shape as the right side (or the right side is a scalar).
- Sections (Lesson 11.4) work in the same way, as long as their shapes match.
- `*` always means element-wise multiplication. Matrix multiplication is done with `matmul` (12.3).

### Intrinsic functions on arrays

Most mathematical intrinsics are **elemental**: given an array, they return an array of the same shape, applying the function to every element. This includes `sqrt`, `exp`, `log`, `sin`, `cos`, `abs`, `mod`, `min`, `max`, `real`, `int`, and many more:

```fortran
y = sqrt(x)              ! square root of every element
z = max(a, b)            ! larger of each pair of elements
```

### Comparisons produce logical arrays

A relational operator (Lesson 7.2) used on an array gives a `logical` array, one answer per element. Such arrays are called **masks** and are used throughout 12.2 and 12.4:

```fortran
big = a > 2.5            ! a logical array
```

### Example

```fortran
program elementwise_demo
    implicit none
    real :: a(4), b(4), c(4)

    a = [1.0, 2.0, 3.0, 4.0]
    b = [10.0, 20.0, 30.0, 40.0]

    c = a + b
    print '(A, 4F7.2)', "a + b       :", c
    c = a * b
    print '(A, 4F7.2)', "a * b       :", c
    c = b / a
    print '(A, 4F7.2)', "b / a       :", c
    c = a**2
    print '(A, 4F7.2)', "a**2        :", c
    c = 2.0 * a + 1.0
    print '(A, 4F7.2)', "2*a + 1     :", c
    c = sqrt(a)
    print '(A, 4F7.2)', "sqrt(a)     :", c
    c = max(a, 2.5)
    print '(A, 4F7.2)', "max(a, 2.5) :", c

    a(2:3) = a(2:3) * 10.0
    print '(A, 4F7.2)', "a(2:3) * 10 :", a
    print *, "b > 25      :", b > 25.0
end program elementwise_demo
```

Output:

```
a + b       :  11.00  22.00  33.00  44.00
a * b       :  10.00  40.00  90.00 160.00
b / a       :  10.00  10.00  10.00  10.00
a**2        :   1.00   4.00   9.00  16.00
2*a + 1     :   3.00   5.00   7.00   9.00
sqrt(a)     :   1.00   1.41   1.73   2.00
max(a, 2.5) :   2.50   2.50   3.00   4.00
a(2:3) * 10 :   1.00  20.00  30.00   4.00
 b > 25      : F F T T
```

If two arrays are **not** conformable, for example a size-3 array added to a size-4 array, the compiler reports an error when it can see the sizes, and otherwise the program stops (or misbehaves) at run time.

> **Tip:** whole-array statements are not only shorter than loops. The compiler can see that every element is independent, so it is free to vectorize them for speed.

---

## 12.2 `sum`, `product`, `maxval`, `minval`

A **reduction** function takes an array and boils it down to a single value, or to a smaller array. These are the ones you will use most:

| Function | Result |
|----------|--------|
| `sum(a)` | Sum of all the elements |
| `product(a)` | Product of all the elements |
| `maxval(a)` | Largest element |
| `minval(a)` | Smallest element |
| `maxloc(a)` | **Position** of the largest element (returned as an array) |
| `minloc(a)` | **Position** of the smallest element |
| `count(mask)` | How many elements of a logical array are true |
| `any(mask)` | True if at least one element of the mask is true |
| `all(mask)` | True if every element of the mask is true |
| `norm2(a)` | Euclidean length, square root of the sum of squares (Fortran 2008) |
| `size(a)` | Number of elements (Lesson 11.1) |

### Reducing along one dimension

For a matrix, the optional argument `dim` says which dimension to collapse. `sum(m, dim=1)` adds **down the columns** (the first index disappears), giving one value per column, and `sum(m, dim=2)` adds **across the rows**, giving one value per row:

```
        dim=2  →
       ┌───────────┐
 dim=1 │ 1   2   3 │      sum(m, dim=1) = [ 5  7  9 ]   (one per column)
   ↓   │ 4   5   6 │      sum(m, dim=2) = [ 6 15 ]      (one per row)
       └───────────┘
```

### Reducing only some elements: `mask`

The reductions `sum`, `product`, `maxval`, `minval`, `count` (and others) accept a `mask=` argument, a logical array of the same shape. Only the elements where the mask is true are used:

```fortran
total_positive = sum(x, mask = x > 0.0)
```

### Finding positions

`maxloc` and `minloc` return an **array** holding the index of the element in each dimension. For a vector the result has one element, so ask for a plain integer by adding `dim=1`:

```fortran
i = maxloc(v, dim=1)       ! index of the largest element of a vector
p = maxloc(m)              ! p(1) = row, p(2) = column
```

If the largest value appears more than once, the first one found is reported.

### Example

```fortran
program reduction_demo
    use, intrinsic :: iso_fortran_env, only: dp => real64
    implicit none
    integer :: m(2, 3), v(6)
    real(dp) :: x(5), mean, sd

    m = reshape([1, 4, 2, 5, 3, 6], [2, 3])
    print *, "m = [1 2 3; 4 5 6]"
    print *, "sum(m)          :", sum(m)
    print *, "product(m)      :", product(m)
    print *, "sum(m, dim=1)   :", sum(m, dim=1)
    print *, "sum(m, dim=2)   :", sum(m, dim=2)
    print *, "maxval, minval  :", maxval(m), minval(m)
    print *, "maxloc(m)       :", maxloc(m)

    v = [3, -1, 4, -1, 5, -9]
    print *, "v               :", v
    print *, "maxloc(v, dim=1):", maxloc(v, dim=1)
    print *, "minloc(v, dim=1):", minloc(v, dim=1)
    print *, "count(v < 0)    :", count(v < 0)
    print *, "any(v > 4)      :", any(v > 4)
    print *, "all(v > 0)      :", all(v > 0)
    print *, "sum of positives:", sum(v, mask = v > 0)

    x = [2.0_dp, 4.0_dp, 4.0_dp, 4.0_dp, 5.0_dp]
    mean = sum(x) / size(x)
    sd = sqrt(sum((x - mean)**2) / size(x))
    print *, "mean            :", mean
    print *, "std. deviation  :", sd
    print *, "norm2(x)        :", norm2(x)
end program reduction_demo
```

Output:

```
 m = [1 2 3; 4 5 6]
 sum(m)          :          21
 product(m)      :         720
 sum(m, dim=1)   :           5           7           9
 sum(m, dim=2)   :           6          15
 maxval, minval  :           6           1
 maxloc(m)       :           2           3
 v               :           3          -1           4          -1           5          -9
 maxloc(v, dim=1):           5
 minloc(v, dim=1):           6
 count(v < 0)    :           3
 any(v > 4)      : T
 all(v > 0)      : F
 sum of positives:          12
 mean            :   3.7999999999999998     
 std. deviation  :  0.97979589711327120     
 norm2(x)        :   8.7749643873921226     
```

Look at how the mean and standard deviation are written. `(x - mean)**2` creates a whole temporary array, `sum` adds it up, and no loop is needed. This is typical Fortran.

---

## 12.3 `matmul`, `dot_product`, `transpose`

Linear algebra is built in. Three intrinsics cover the basics:

| Function | Result |
|----------|--------|
| `matmul(a, b)` | Matrix product (matrix x matrix, matrix x vector, or vector x matrix) |
| `dot_product(u, v)` | Sum of `u(i) * v(i)` for two vectors of equal length |
| `transpose(a)` | The matrix with rows and columns exchanged |

### Matrix multiplication

For `c = matmul(a, b)`, the number of **columns** of `a` must equal the number of **rows** of `b`. If `a` is n x k and `b` is k x p, then `c` is n x p, with

```
c(i, j) = a(i, 1)*b(1, j) + a(i, 2)*b(2, j) + ... + a(i, k)*b(k, j)
```

Do not confuse it with `a * b`, which multiplies matching elements and requires identical shapes.

### Writing matrices the way you read them

Because `reshape` fills column by column, a matrix typed in this way looks transposed. Add `order=[2, 1]` to write the values row by row, just as they appear on paper:

```fortran
a = reshape([1.0, 2.0, &
             3.0, 4.0], [2, 2], order=[2, 1])      ! a = [1 2; 3 4]
```

### Example

```fortran
program matrix_demo
    implicit none
    real :: a(2, 2), b(2, 2), x(2), y(2), c(2, 2), t(2, 3), m(3, 2)
    integer :: i

    a = reshape([1.0, 2.0, &
                 3.0, 4.0], [2, 2], order=[2, 1])
    b = reshape([0.0, 1.0, &
                 1.0, 0.0], [2, 2], order=[2, 1])
    x = [1.0, 1.0]

    print *, "a:"
    do i = 1, 2
        print '(2F6.1)', a(i, :)
    end do

    c = matmul(a, b)
    print *, "matmul(a, b):"
    do i = 1, 2
        print '(2F6.1)', c(i, :)
    end do

    c = a * b
    print *, "a * b (element-wise):"
    do i = 1, 2
        print '(2F6.1)', c(i, :)
    end do

    y = matmul(a, x)
    print *, "matmul(a, x)       :", y
    print *, "dot_product(x, y)  :", dot_product(x, y)

    t = reshape([1.0, 2.0, 3.0, &
                 4.0, 5.0, 6.0], [2, 3], order=[2, 1])
    m = transpose(t)
    print *, "t is", shape(t), "; transpose(t) is", shape(m)
    print *, "t * transpose(t) via matmul:"
    c = matmul(t, m)
    do i = 1, 2
        print '(2F6.1)', c(i, :)
    end do
end program matrix_demo
```

Output:

```
 a:
   1.0   2.0
   3.0   4.0
 matmul(a, b):
   2.0   1.0
   4.0   3.0
 a * b (element-wise):
   0.0   2.0
   3.0   0.0
 matmul(a, x)       :   3.00000000       7.00000000    
 dot_product(x, y)  :   10.0000000    
 t is           2           3 ; transpose(t) is           3           2
 t * transpose(t) via matmul:
  14.0  32.0
  32.0  77.0
```

Here `b` swaps the two columns of whatever matrix it multiplies from the right, so `matmul(a, b)` swaps the columns of `a`, while `a * b` just masks out some entries.

> **Performance note:** `matmul` is perfectly good for small and medium matrices. For large ones, specialized libraries (BLAS and LAPACK, Lesson 31) are much faster, and `gfortran` can be told to use them with `-fexternal-blas`.

---

## 12.4 `where`, `forall`, and Masking

Sometimes an operation should affect only some elements, such as replacing negative readings, or taking the square root only of positive values. A **mask** (a logical array of the same shape) selects which elements to touch.

### The `where` statement and construct

The one-line form applies one assignment to the elements where the mask is true:

```fortran
where (a < 0.0) a = 0.0
```

The block form (previewed in Lesson 9.4) can have `elsewhere` branches, including extra masks:

```fortran
where (a > 0.0)
    b = sqrt(a)
elsewhere (a < 0.0)
    b = -1.0
elsewhere
    b = 0.0
end where
```

Rules for `where`:

- The mask and every assigned array must have the same shape.
- Only array assignments are allowed inside the block, not `print`, `if`, or `do`.
- The test is done once, **before** any assignment, so changing the array inside the block does not alter which elements were chosen.
- Elements outside the mask are left unchanged. That is also why `sqrt(a)` above is safe: it is never evaluated for the negative elements.

### `merge`: choosing between two values

`merge(tsource, fsource, mask)` returns `tsource` where the mask is true and `fsource` where it is false. It is the array version of an inline `if`, and it is also useful for scalars:

```fortran
c = merge(a, b, a > b)        ! element-wise larger value
```

### `pack` and `unpack`

`pack(a, mask)` collects the elements where the mask is true into a new one-dimensional array. It is the easy way to extract all the values that meet some condition:

```fortran
positives = pack(v, v > 0)
```

`unpack(vector, mask, field)` does the reverse: it puts the elements of `vector` back into the true positions of the mask, with `field` supplying the other elements.

### `forall`

Older Fortran code often uses `forall` to assign to the elements of an array in any order:

```fortran
forall (i = 1:n) a(i, i) = 1.0      ! set the diagonal
```

Since Fortran 2018 `forall` is **obsolescent** (it is still legal, but marked for eventual removal), because `do concurrent` (Lesson 10.5) does the job better and more flexibly. New code should use:

```fortran
do concurrent (i = 1:n)
    a(i, i) = 1.0
end do
```

### Example

```fortran
program mask_demo
    implicit none
    integer, parameter :: n = 6
    integer :: i
    real :: a(n), b(n), c(n), d(n), eye(3, 3)
    real, allocatable :: positives(:)

    a = [3.0, -1.5, 0.0, 8.0, -4.0, 2.5]

    b = a
    where (b < 0.0) b = 0.0
    print '(A, 6F6.1)', "negatives to 0 :", b

    where (a > 0.0)
        c = sqrt(a)
    elsewhere (a < 0.0)
        c = -1.0
    elsewhere
        c = 0.0
    end where
    print '(A, 6F6.2)', "sqrt / -1 / 0  :", c

    d = merge(a, 0.0, a > 1.0)
    print '(A, 6F6.1)', "merge(a,0,a>1) :", d

    positives = pack(a, a > 0.0)
    print *, "pack(a, a>0)   :", positives
    print *, "count positives:", count(a > 0.0)

    eye = 0.0
    do concurrent (i = 1:3)
        eye(i, i) = 1.0
    end do
    print *, "identity:"
    do i = 1, 3
        print '(3F5.1)', eye(i, :)
    end do
end program mask_demo
```

Output:

```
negatives to 0 :   3.0   0.0   0.0   8.0   0.0   2.5
sqrt / -1 / 0  :  1.73 -1.00  0.00  2.83 -1.00  1.58
merge(a,0,a>1) :   3.0   0.0   0.0   8.0   0.0   2.5
 pack(a, a>0)   :   3.00000000       8.00000000       2.50000000    
 count positives:           3
 identity:
  1.0  0.0  0.0
  0.0  1.0  0.0
  0.0  0.0  1.0
```

(`positives` is declared `allocatable` and sized automatically by the assignment. Lesson 13 explains how that works.)

Masks, reductions, and elemental functions combine naturally. For example, `sum(a, mask = a > 0.0) / count(a > 0.0)` gives the average of just the positive elements, with no loop at all.

---

[Previous](./[11]-Arrays-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[13]-Allocatable-Arrays.md)
