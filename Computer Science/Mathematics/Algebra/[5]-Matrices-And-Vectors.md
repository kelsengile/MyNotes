[Previous](./[4]-Systems-Of-Equations.md) | [Table of Contents](./[0]-Introduction-to-Algebra.md) | [Next](./[6]-Sequences-And-Series.md)

# Lesson 5 - Matrices And Vectors

## 5.1 Matrix Operations

A **matrix** is a rectangular grid of numbers arranged in rows and columns. A matrix with `m` rows and `n` columns is called an `m x n` matrix.

```
    | 1  2  3 |
A = | 4  5  6 |      (a 2x3 matrix — 2 rows, 3 columns)
```

**Addition/Subtraction:** matrices must be the same size; add or subtract corresponding entries.

```
| 1  2 |   | 5  6 |   | 6   8 |
| 3  4 | + | 7  8 | = | 10  12 |
```

**Scalar Multiplication:** multiply every entry by the scalar.

```
    | 1  2 |   | 2  4 |
2 * | 3  4 | = | 6  8 |
```

**Matrix Multiplication:** this is the operation that trips people up most, because it's *not* element-by-element. To multiply `A * B`, the number of columns in `A` must equal the number of rows in `B`. Each entry in the result is the **dot product** of a row from `A` and a column from `B`.

```
| 1  2 |   | 5  6 |   | (1*5+2*7)  (1*6+2*8) |   | 19  22 |
| 3  4 | * | 7  8 | = | (3*5+4*7)  (3*6+4*8) | = | 43  50 |
```

Walking through the top-left entry: take row 1 of `A` → `[1, 2]`, and column 1 of `B` → `[5, 7]`, multiply pairwise and sum: `1*5 + 2*7 = 19`.

**Important:** matrix multiplication is generally **not commutative** — `A * B ≠ B * A` in most cases, unlike regular number multiplication.

**The Identity Matrix:** the matrix equivalent of the number `1` — multiplying any matrix by it leaves the matrix unchanged. For a 2x2 matrix:

```
    | 1  0 |
I = | 0  1 |
```

---

## 5.2 Vectors and Vector Spaces

A **vector** is an ordered list of numbers, often thought of as a point in space or an arrow with both direction and magnitude. A vector with `n` entries lives in `n`-dimensional space:

```
v = [3, 4]        (a 2D vector — think of it as "3 right, 4 up")
```

**Magnitude (length)** of a vector, using the Pythagorean theorem:

```
|v| = √(3^2 + 4^2) = √(9 + 16) = √25 = 5
```

**Vector addition:** add corresponding entries — this is the same rule as matrix addition, since a vector is just a matrix with one row or column.

```
[1, 2] + [3, -1] = [4, 1]
```

**Scalar multiplication:** scales the vector's length (and flips its direction if negative).

```
3 * [1, 2] = [3, 6]
```

**Dot product:** multiply corresponding entries and sum the results — the same operation used inside matrix multiplication above. It produces a single number (a scalar), often used to measure how much two vectors point in the same direction.

```
[1, 2, 3] · [4, 5, 6] = (1*4) + (2*5) + (3*6) = 4 + 10 + 18 = 32
```

**Vector space (conceptually):** a set of vectors that is closed under addition and scalar multiplication — meaning if you add any two vectors in the set, or scale any vector in the set, the result is still in the set. You don't need the full formal definition to work with algebra day-to-day, but it's the concept that everything above (lines, planes, and higher-dimensional analogues) generalizes into.

---

## 5.3 Why Linear Algebra Matters in CS

Matrices and vectors are the backbone of much of modern computing:

- **Computer graphics:** 3D positions are vectors; rotating, scaling, and moving objects on screen is done by multiplying those vectors by transformation matrices — every frame of a video game or animated film relies on this.
- **Machine learning:** a dataset is typically stored as a matrix (rows = examples, columns = features); a neural network layer is, at its core, a matrix multiplication followed by a small nonlinear adjustment.
- **Search & recommendation:** techniques like PageRank (how Google originally ranked web pages) and collaborative filtering (how streaming services recommend content) are built on vector and matrix operations.
- **Image processing:** an image is a matrix of pixel values; filters (blur, sharpen, edge detection) are applied using matrix operations.
- **Solving large systems efficiently:** as shown in the previous lesson, real-world systems can have thousands of variables — the matrix form is what makes solving them computationally tractable.

**Example — a tiny "neural network" step**

A single layer's output is often computed as `output = W * x + b`, where `W` is a weight matrix, `x` is the input vector, and `b` is a bias vector:

```python
import numpy as np

W = np.array([[0.5, -0.2], [0.1, 0.8]])
x = np.array([1.0, 2.0])
b = np.array([0.1, -0.1])

output = W @ x + b   # matrix-vector multiplication, then add bias
```

This single line is, mathematically, exactly the matrix and vector operations covered in this lesson — which is why a solid grasp of linear algebra makes concepts like machine learning far less mysterious.

---

[Previous](./[4]-Systems-Of-Equations.md) | [Table of Contents](./[0]-Introduction-to-Algebra.md) | [Next](./[6]-Sequences-And-Series.md)
