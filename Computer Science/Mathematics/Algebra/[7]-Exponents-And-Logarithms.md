[Previous](./[6]-Sequences-And-Series.md) | [Table of Contents](./[0]-Introduction-to-Algebra.md)

# Lesson 7 - Exponents And Logarithms

## 7.1 Exponent Rules

An **exponent** tells you how many times to multiply a base by itself: `x^n` means `x` multiplied by itself `n` times.

```
2^4 = 2 * 2 * 2 * 2 = 16
```

**Core rules** (assume `x, y ≠ 0` where relevant):

| Rule | Formula | Example |
|---|---|---|
| Product of powers | `x^a * x^b = x^(a+b)` | `2^3 * 2^2 = 2^5 = 32` |
| Quotient of powers | `x^a / x^b = x^(a-b)` | `2^5 / 2^2 = 2^3 = 8` |
| Power of a power | `(x^a)^b = x^(a*b)` | `(2^2)^3 = 2^6 = 64` |
| Power of a product | `(xy)^a = x^a * y^a` | `(2*3)^2 = 2^2 * 3^2 = 36` |
| Zero exponent | `x^0 = 1` | `5^0 = 1` |
| Negative exponent | `x^(-a) = 1 / x^a` | `2^(-3) = 1/8` |
| Fractional exponent | `x^(1/n) = ⁿ√x` | `8^(1/3) = ∛8 = 2` |

**Worked example combining rules**

```
(2x^3)^2 * x^(-1)
= 2^2 * (x^3)^2 * x^(-1)      (power of a product)
= 4 * x^6 * x^(-1)            (power of a power)
= 4 * x^(6 + (-1))            (product of powers)
= 4x^5
```

**Why this matters in CS:** exponents describe how quickly things grow — `O(2ⁿ)` exponential algorithms, doubling a hash table's capacity, or the number of possible values representable in `n` bits (`2ⁿ`) are all direct applications.

---

## 7.2 Logarithms and Their Properties

A **logarithm** answers the question: "What power do I need to raise the base to, to get this number?" It's the inverse operation of exponentiation.

```
log_b(x) = y     means the same thing as     b^y = x
```

**Example**

```
log₂(8) = 3      because     2^3 = 8
log₁₀(1000) = 3  because     10^3 = 1000
```

**Common bases have shorthand:**
- `log(x)` usually means `log₁₀(x)` (base 10)
- `ln(x)` means `logₑ(x)` — the **natural log**, base `e` (≈ 2.71828)
- `log₂(x)` — base 2 — comes up constantly in computer science

**Logarithm properties** (mirror the exponent rules above, since they're inverses of each other):

| Rule | Formula | Example |
|---|---|---|
| Product rule | `log_b(xy) = log_b(x) + log_b(y)` | `log₂(4*8) = log₂4 + log₂8 = 2+3 = 5` |
| Quotient rule | `log_b(x/y) = log_b(x) - log_b(y)` | `log₂(16/4) = 4-2 = 2` |
| Power rule | `log_b(x^n) = n * log_b(x)` | `log₂(8^2) = 2*log₂8 = 2*3 = 6` |
| Change of base | `log_b(x) = log_k(x) / log_k(b)` | `log₂(10) = log(10)/log(2) ≈ 3.32` |

**Worked example — solving an equation with an unknown exponent**

```
2^x = 32
log₂(2^x) = log₂(32)     (take log base 2 of both sides)
x = log₂(32) = 5          (since 2^5 = 32)
```

---

## 7.3 Why Logarithms Matter in Complexity Analysis

Logarithms show up throughout computer science any time a problem's size is repeatedly **cut down by a constant factor** (usually halved).

**Binary search**

Each comparison eliminates half of the remaining search space. Starting with `n` elements, after `k` steps you have `n / 2^k` elements left. Searching stops when only 1 element remains:

```
n / 2^k = 1
n = 2^k
k = log₂(n)
```

That's exactly why binary search runs in `O(log n)` time — the number of steps needed is the base-2 logarithm of the input size. For a billion elements, that's only about 30 steps, versus up to a billion for a linear scan.

**Other places `log n` shows up:**

- **Balanced binary search trees** (like AVL or red-black trees) have a height of `O(log n)`, which is why lookup, insertion, and deletion are all `O(log n)`.
- **Heaps**, used in priority queues, have `O(log n)` insert and remove operations because they're stored as balanced binary trees.
- **Divide-and-conquer algorithms** like merge sort split the input in half at every recursive step, giving them a `log n` factor in their `O(n log n)` runtime.
- **Information theory:** `log₂(n)` represents the minimum number of bits needed to distinguish between `n` possibilities — the basis for measuring information content.

**Quick intuition check:** if doubling the input size only adds a small, constant amount of extra work (rather than doubling the work), you're very likely looking at a logarithmic relationship — a strong sign of an efficient algorithm.

---

This wraps up the Introduction to Algebra series — from solving basic equations all the way to the exponential and logarithmic relationships that describe algorithm efficiency. These foundations directly support later topics in Data Structures & Algorithms and Discrete Mathematics.

[Previous](./[6]-Sequences-And-Series.md) | [Table of Contents](./[0]-Introduction-to-Algebra.md)
