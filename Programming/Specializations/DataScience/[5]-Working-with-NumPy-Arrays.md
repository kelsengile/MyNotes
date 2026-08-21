*Core Programming for Data Science*

# Lesson 5 - Working with NumPy Arrays

[Previous](./[4]-Python-Fundamentals-for-Data-Science.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[6]-Data-Manipulation-with-Pandas.md)

---

## 5.1 What is NumPy?

**NumPy** ("Numerical Python") provides the `ndarray`, a fast, memory-efficient array type that underlies almost every other data science library in Python (Pandas, scikit-learn, TensorFlow all build on it). Unlike Python lists, NumPy arrays store elements of a single type in contiguous memory, which makes math operations dramatically faster.

```python
import numpy as np

arr = np.array([1, 2, 3, 4, 5])
print(arr.shape)   # (5,)
print(arr.dtype)   # int64
```

---

## 5.2 Creating and Indexing Arrays

```python
zeros = np.zeros((3, 3))          # 3x3 array of zeros
ones = np.ones((2, 4))            # 2x4 array of ones
seq = np.arange(0, 10, 2)         # [0, 2, 4, 6, 8]
grid = np.linspace(0, 1, 5)       # 5 evenly spaced points from 0 to 1

matrix = np.array([[1, 2, 3], [4, 5, 6]])
print(matrix[0, 1])     # 2 — row 0, column 1
print(matrix[:, 0])     # [1, 4] — entire first column
print(matrix[1, :])     # [4, 5, 6] — entire second row
```

---

## 5.3 Vectorized Operations

The biggest advantage of NumPy is **vectorization** — applying operations to whole arrays at once instead of writing explicit loops:

```python
a = np.array([1, 2, 3])
b = np.array([10, 20, 30])

print(a + b)          # [11, 22, 33]
print(a * 2)           # [2, 4, 6]
print(a > 1)            # [False, True, True] — boolean mask
print(a[a > 1])          # [2, 3] — filter using the mask

print(np.mean(b))        # 20.0
print(np.sum(b))         # 60
print(np.std(b))         # standard deviation
```

Vectorized code is not just shorter — it typically runs 10-100x faster than an equivalent Python `for` loop because NumPy executes the operation in optimized, compiled code.

---

## 5.4 Broadcasting

**Broadcasting** lets NumPy apply operations between arrays of different shapes, as long as their shapes are compatible:

```python
matrix = np.array([[1, 2, 3], [4, 5, 6]])
row_means = matrix.mean(axis=1)   # mean of each row: [2. , 5.]
col_means = matrix.mean(axis=0)   # mean of each column: [2.5, 3.5, 4.5]

# Subtract the column means from every row
centered = matrix - col_means
```

Understanding broadcasting rules (`axis=0` for columns, `axis=1` for rows) is essential — it comes up constantly when working with Pandas DataFrames too.

---

[Previous](./[4]-Python-Fundamentals-for-Data-Science.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[6]-Data-Manipulation-with-Pandas.md)
