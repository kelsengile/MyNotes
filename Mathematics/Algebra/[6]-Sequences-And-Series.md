[Previous](./[5]-Matrices-And-Vectors.md) | [Table of Contents](./[0]-Introduction-to-Algebra.md) | [Next](./[7]-Exponents-And-Logarithms.md)

# Lesson 6 - Sequences And Series

## 6.1 Arithmetic and Geometric Sequences

A **sequence** is an ordered list of numbers, called **terms**, usually following a pattern. Terms are often written as `a₁, a₂, a₃, ...` or referenced generally as `aₙ` (the "n-th term").

**Arithmetic Sequences**

An arithmetic sequence has a **constant difference**, `d`, between consecutive terms:

```
2, 5, 8, 11, 14, ...      (d = 3)
```

The formula for the `n`-th term:

```
aₙ = a₁ + (n - 1)d
```

**Example:** find the 10th term of `2, 5, 8, 11, ...` (`a₁ = 2, d = 3`):

```
a₁₀ = 2 + (10 - 1)(3) = 2 + 27 = 29
```

**Geometric Sequences**

A geometric sequence has a **constant ratio**, `r`, between consecutive terms (each term is multiplied by the same value to get the next):

```
3, 6, 12, 24, 48, ...     (r = 2)
```

The formula for the `n`-th term:

```
aₙ = a₁ * r^(n-1)
```

**Example:** find the 6th term of `3, 6, 12, 24, ...` (`a₁ = 3, r = 2`):

```
a₆ = 3 * 2^(6-1) = 3 * 32 = 96
```

**Telling them apart quickly:** if you *add* the same amount each time, it's arithmetic. If you *multiply* by the same amount each time, it's geometric.

---

## 6.2 Summation Notation

Rather than writing out `a₁ + a₂ + a₃ + ... + aₙ`, mathematicians use **sigma notation** (`Σ`, the Greek capital letter sigma) as shorthand for "sum of."

```
  n
  Σ  aᵢ   =   a₁ + a₂ + a₃ + ... + aₙ
 i=1
```

The letter under `Σ` (here, `i`) is the **index**, starting at the number below `Σ` and ending at the number above it.

**Example 1**

```
  5
  Σ  i   =  1 + 2 + 3 + 4 + 5  =  15
 i=1
```

**Example 2 — summing a formula**

```
  4
  Σ  (2i + 1)  =  (2*1+1) + (2*2+1) + (2*3+1) + (2*4+1)
 i=1
             =  3 + 5 + 7 + 9  =  24
```

**Example 3 — summing an arithmetic sequence**

The sum of the first `n` terms of an arithmetic sequence has a shortcut formula so you don't have to add every term one by one:

```
Sₙ = n(a₁ + aₙ) / 2
```

For `2, 5, 8, 11, 14` (5 terms, `a₁=2`, `a₅=14`):
```
S₅ = 5(2 + 14) / 2 = 5(16) / 2 = 40
```

This directly mirrors how you'd write a summation as a loop in code:

```python
total = 0
for i in range(1, 6):
    total += 2 * i + 1
```

---

## 6.3 Series and Convergence (Intro)

A **series** is the sum of the terms of a sequence. A **finite series** adds a fixed number of terms; an **infinite series** adds terms forever:

```
1 + 1/2 + 1/4 + 1/8 + 1/16 + ...     (infinite geometric series, r = 1/2)
```

Surprisingly, some infinite series add up to a *finite* number — this is called **converging**. Others grow without bound — this is called **diverging**.

**Infinite Geometric Series Formula**

If `|r| < 1` (the ratio's absolute value is less than 1), the infinite sum converges to:

```
S = a₁ / (1 - r)
```

**Example:** sum `1 + 1/2 + 1/4 + 1/8 + ...` (`a₁ = 1, r = 1/2`):

```
S = 1 / (1 - 1/2) = 1 / (1/2) = 2
```

Intuitively: each new term fills in half of what's left of the "gap" to 2, so the sum gets closer and closer to 2 without ever exceeding it.

**When it diverges:** if `|r| ≥ 1` (like `r = 2` in `3, 6, 12, 24, ...`), the terms don't shrink, so the sum grows without bound — the series diverges, and the formula above doesn't apply.

This is only an introduction — full convergence tests (ratio test, comparison test, etc.) are typically covered in a calculus course, but recognizing convergent vs. divergent behavior is the essential takeaway here.

---

## 6.4 Why This Matters for Recurrence Relations

A **recurrence relation** defines each term of a sequence using one or more previous terms — this is precisely how recursive algorithms and data structures are analyzed in computer science.

**Example — the Fibonacci sequence**

```
F(0) = 0,  F(1) = 1
F(n) = F(n-1) + F(n-2)   for n ≥ 2
```
This produces: `0, 1, 1, 2, 3, 5, 8, 13, 21, ...`

This maps directly onto recursive code:

```python
def fib(n):
    if n <= 1:
        return n
    return fib(n - 1) + fib(n - 2)
```

**Why sequences and series matter for analyzing algorithms**

- **Loop cost analysis:** if a loop's work follows a pattern like `1 + 2 + 3 + ... + n`, that's an arithmetic series, and the shortcut formula from 6.2 (`n(n+1)/2`) is exactly how you'd derive that a nested loop over `n` elements does `O(n²)` work.
- **Recursive algorithm cost:** the number of operations in recursive algorithms (like the naive Fibonacci above) can often be expressed as a recurrence relation, and solving that recurrence tells you the algorithm's time complexity.
- **Geometric growth/decay:** algorithms that repeatedly halve their input (like binary search) or double their storage (like a dynamic array's resizing) behave like geometric sequences — which is exactly why binary search runs in `O(log n)` and doubling-array resizing gives amortized `O(1)` insertion.

Sequences and series aren't just abstract math — they're the language used to describe and prove how efficient (or inefficient) an algorithm actually is.

---

[Previous](./[5]-Matrices-And-Vectors.md) | [Table of Contents](./[0]-Introduction-to-Algebra.md) | [Next](./[7]-Exponents-And-Logarithms.md)
