[Previous](./[3]-Polynomials.md) | [Table of Contents](./[0]-Introduction-to-Algebra.md) | [Next](./[5]-Matrices-And-Vectors.md)

# Lesson 4 - Systems Of Equations

## 4.1 Solving by Substitution and Elimination

A **system of equations** is a set of two or more equations that share the same variables, where you're looking for values that satisfy *all* the equations at once.

```
2x + y = 11
x - y  = 1
```

Geometrically, each linear equation is a line; the solution to the system is the point where the lines intersect.

**Method 1 — Substitution**

Solve one equation for one variable, then substitute that expression into the other equation.

```
x - y = 1   →   x = y + 1        (solve for x)

2x + y = 11
2(y + 1) + y = 11    (substitute)
2y + 2 + y   = 11
3y + 2       = 11
3y           = 9
y            = 3

x = y + 1 = 3 + 1 = 4
```
Solution: `(x, y) = (4, 3)`.

Check both original equations: `2(4) + 3 = 11` ✅ and `4 - 3 = 1` ✅.

**Method 2 — Elimination**

Add or subtract the equations (after scaling, if needed) so that one variable cancels out.

```
2x + y = 11
x - y  = 1
-----------  (add the equations directly — the y terms cancel)
3x     = 12
 x     = 4
```
Substitute back into either original equation:
```
x - y = 1  →  4 - y = 1  →  y = 3
```
Same solution: `(4, 3)`.

**When to use which:** substitution is usually cleaner when one equation is already solved (or easily solved) for a variable; elimination is usually cleaner when the coefficients line up nicely for cancelling.

**Three possible outcomes** for a system of two linear equations:

| Outcome | Geometric meaning | Example |
|---|---|---|
| One solution | Lines cross at exactly one point | Most systems |
| No solution | Lines are parallel, never meet | `x + y = 1` and `x + y = 5` |
| Infinite solutions | Lines are actually the same line | `x + y = 1` and `2x + 2y = 2` |

---

## 4.2 Systems in Matrix Form

Every linear system can be rewritten compactly using **matrices** — rectangular grids of numbers. This isn't just notation; it's the form computers actually use to solve large systems efficiently.

Take the system:

```
2x + y = 11
x - y  = 1
```

We separate it into three pieces: the **coefficient matrix**, the **variable vector**, and the **constant vector**:

```
| 2   1 |   | x |   | 11 |
| 1  -1 | * | y | = |  1 |
```

This is written compactly as `A * x = b`, where:
- `A` is the coefficient matrix `[[2, 1], [1, -1]]`
- `x` is the variable vector `[x, y]`
- `b` is the constant vector `[11, 1]`

Matrix multiplication (covered fully in the next lesson) recovers the original equations exactly: multiplying row 1 of `A` by the column `[x, y]` gives `2x + 1y`, which must equal the first entry of `b`, `11` — that's our first equation.

**Why represent it this way?** Because it lets you solve *any* size system — 2 variables or 2,000 — using the same general algorithms (like Gaussian elimination), which is exactly what libraries like NumPy's `linalg.solve()` do under the hood. It also generalizes cleanly to code:

```python
import numpy as np

A = np.array([[2, 1], [1, -1]])
b = np.array([11, 1])
x = np.linalg.solve(A, b)   # => [4., 3.]
```

**Why this matters in CS:** systems of equations underlie linear regression (fitting a line to data), computer graphics transformations, circuit analysis, and optimization — anywhere multiple constraints need to be satisfied simultaneously.

---

[Previous](./[3]-Polynomials.md) | [Table of Contents](./[0]-Introduction-to-Algebra.md) | [Next](./[5]-Matrices-And-Vectors.md)
