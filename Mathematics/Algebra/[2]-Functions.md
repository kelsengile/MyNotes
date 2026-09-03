[Previous](./[1]-Foundations-Of-Algebra.md) | [Table of Contents](./[0]-Introduction-to-Algebra.md) | [Next](./[3]-Polynomials.md)

# Lesson 2 - Functions

## 2.1 What Is a Function?

A **function** is a rule that assigns exactly one output to each valid input. Think of it as a machine: you feed in a value, it does something consistent to that value, and exactly one result comes out.

Functions are usually named with a letter like `f`, `g`, or `h`, and written as:

```
f(x) = 2x + 1
```

Read as "f of x equals 2x plus 1." To **evaluate** the function at a specific input, substitute that value for `x`:

```
f(3) = 2(3) + 1 = 7
f(0) = 2(0) + 1 = 1
f(-2) = 2(-2) + 1 = -3
```

**The one rule that matters:** for a relation to be a function, *each input can only map to one output*. `f(x) = x^2` is a function (every `x` gives exactly one `x^2`), but `x = y^2` is not a function of `x` (e.g., `x = 4` maps to both `y = 2` and `y = -2`).

This is directly analogous to a function in code — the same input to a pure function must always produce the same, single output:

```python
def f(x):
    return 2 * x + 1
```

---

## 2.2 Domain, Range, and Notation

The **domain** of a function is the complete set of valid inputs — every value you're allowed to plug in. The **range** is the complete set of possible outputs the function can produce.

**Example 1**

```
f(x) = x^2
```
- Domain: all real numbers (you can square anything)
- Range: all numbers ≥ 0 (a square is never negative)

**Example 2 — domain restricted by division**

```
g(x) = 1 / (x - 3)
```
Since division by zero is undefined, `x` cannot equal `3`.
- Domain: all real numbers except `x = 3`

**Example 3 — domain restricted by a square root**

```
h(x) = √(x - 5)
```
You can't take the square root of a negative number (within the reals), so `x - 5` must be ≥ 0.
- Domain: `x ≥ 5`

**Notation reference**

| Notation | Meaning |
|---|---|
| `f(x)` | The function `f` evaluated at `x` |
| `f: A → B` | `f` maps elements from set `A` (domain) to set `B` (range/codomain) |
| `f(a) = b` | Plugging in `a` gives output `b` |
| `f⁻¹(x)` | The inverse function (undoes `f`) |

**Why this matters in CS:** domain restrictions are exactly the kind of edge cases you guard against in code — checking for division by zero, validating that an index is in bounds, or ensuring input to a `sqrt()` call is non-negative.

---

## 2.3 Graphing Functions

Graphing a function means plotting every pair `(x, f(x))` on a coordinate plane, with `x` on the horizontal axis and `f(x)` (often written `y`) on the vertical axis.

**Example — graphing `f(x) = 2x + 1`**

Build a small table of input/output pairs, then plot the points:

| x | f(x) = 2x + 1 |
|---|---|
| -2 | -3 |
| -1 | -1 |
| 0 | 1 |
| 1 | 3 |
| 2 | 5 |

Plotting these points and connecting them produces a straight line — confirming this is a **linear function**. The line crosses the y-axis at `(0, 1)` — this value is called the **y-intercept**. For every 1 unit `x` increases, `f(x)` increases by 2 — this rate of change is called the **slope**.

```
        y
        |
      5 |              *
      3 |         *
      1 |    *
        |________________ x
     -2 -1  0  1  2
     -1 *
     -3*
```

**The Vertical Line Test:** if you can draw any vertical line through a graph and it touches the curve more than once, the graph does **not** represent a function (that `x` value would have more than one output). A circle, for example, fails this test — it's not a function of `x`.

**Why this matters in CS:** visualizing functions is how you build intuition for how an algorithm's runtime grows (plotting `n` vs. number of operations), how a loss function behaves during training, or how a value changes over time in a simulation.

---

[Previous](./[1]-Foundations-Of-Algebra.md) | [Table of Contents](./[0]-Introduction-to-Algebra.md) | [Next](./[3]-Polynomials.md)
