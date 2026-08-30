[Previous](./[1]-Limits.md) | [Table of Contents](./[0]-Introduction-to-Calculus.md) | [Next](./[3]-Integrals.md)

# Lesson 2 - Derivatives

## 2.1 The Concept of a Derivative

A **derivative** measures the **instantaneous rate of change** of a function — how fast its output is changing at an exact point, rather than over some interval.

Recall from algebra that slope between two points `(x₁, y₁)` and `(x₂, y₂)` is:

```
slope = (y₂ - y₁) / (x₂ - x₁)
```

This gives the **average** rate of change between two points. A derivative is what you get when you shrink the distance between those two points down to zero, using a limit — giving the slope at a *single* point instead of between two of them.

Formally, the derivative of `f` at `x` is defined as:

```
f'(x) = lim  ( f(x + h) - f(x) ) / h
        h→0
```

Here, `h` represents a tiny step away from `x`; as `h→0`, the "average slope over a tiny step" becomes the "exact slope at that point."

**Worked example — deriving `f(x) = x^2` from the definition**

```
f'(x) = lim  ( (x+h)^2 - x^2 ) / h
        h→0

= lim  ( x^2 + 2xh + h^2 - x^2 ) / h
  h→0

= lim  ( 2xh + h^2 ) / h
  h→0

= lim  ( 2x + h )          (factor h out of numerator, cancel with denominator)
  h→0

= 2x                        (as h→0)
```

So `f'(x) = 2x`. Check it: at `x = 3`, the slope of `f(x) = x^2` is `f'(3) = 6` — matching what you'd see by zooming in on the curve at that point.

**Notation:** `f'(x)`, `dy/dx`, and `d/dx[f(x)]` all mean the same thing — "the derivative of `f` with respect to `x`."

---

## 2.2 Differentiation Rules

Deriving every function from the limit definition would be slow — in practice, a handful of rules cover almost everything.

| Rule | Formula | Example |
|---|---|---|
| Power Rule | `d/dx[x^n] = n*x^(n-1)` | `d/dx[x^5] = 5x^4` |
| Constant Rule | `d/dx[c] = 0` | `d/dx[7] = 0` |
| Constant Multiple | `d/dx[c*f(x)] = c*f'(x)` | `d/dx[3x^2] = 6x` |
| Sum/Difference Rule | `d/dx[f ± g] = f' ± g'` | `d/dx[x^2 + 3x] = 2x + 3` |
| Product Rule | `d/dx[f*g] = f'g + fg'` | see below |
| Quotient Rule | `d/dx[f/g] = (f'g - fg') / g^2` | see below |
| Chain Rule | `d/dx[f(g(x))] = f'(g(x)) * g'(x)` | see below |

**Product Rule example**

Differentiate `y = x^2 * sin(x)` (let `f = x^2`, `g = sin(x)`, so `f' = 2x`, `g' = cos(x)`):

```
y' = f'g + fg' = 2x*sin(x) + x^2*cos(x)
```

**Quotient Rule example**

Differentiate `y = x / (x + 1)` (let `f = x`, `g = x+1`, so `f' = 1`, `g' = 1`):

```
y' = (1*(x+1) - x*1) / (x+1)^2 = (x + 1 - x) / (x+1)^2 = 1 / (x+1)^2
```

**Chain Rule example**

Differentiate `y = (3x + 1)^4` (outer function `u^4`, inner function `u = 3x+1`):

```
y' = 4(3x+1)^3 * 3 = 12(3x+1)^3
```

The chain rule is essential for anything "nested" — a function inside a function — and is one of the most-used rules in calculus-based machine learning (see Lesson 6).

---

## 2.3 Applications: Rates of Change and Optimization

**Rates of change**

Because a derivative is a slope, it directly represents a rate: if `s(t)` is position over time, `s'(t)` is **velocity** (rate of change of position), and `s''(t)` (the derivative of the derivative) is **acceleration**.

**Example:** if `s(t) = t^3 - 6t^2 + 9t` (position in meters at time `t` seconds):

```
velocity:     s'(t) = 3t^2 - 12t + 9
acceleration: s''(t) = 6t - 12
```

At `t = 1`: `s'(1) = 3 - 12 + 9 = 0` — the object is momentarily at rest.

**Optimization — finding maxima and minima**

At the peak of a hill or the bottom of a valley on a graph, the tangent line is perfectly flat — meaning the derivative equals zero. This gives a powerful strategy for finding maximum or minimum values:

1. Take the derivative, `f'(x)`.
2. Set `f'(x) = 0` and solve — these are the **critical points**.
3. Determine whether each critical point is a max, min, or neither (e.g., using the second derivative, or checking the sign of `f'` on either side).

**Example:** find the minimum of `f(x) = x^2 - 4x + 7`.

```
f'(x) = 2x - 4
2x - 4 = 0
x = 2
```
Since `f''(x) = 2 > 0` (the function curves upward everywhere), `x = 2` is a **minimum**. The minimum value is `f(2) = 4 - 8 + 7 = 3`.

**Why this matters in CS:** this exact process — take a derivative, set it to zero, solve — is the mathematical foundation of training machine learning models. A "loss function" measures how wrong a model's predictions are, and training means adjusting the model's parameters to minimize that loss, which is optimization in precisely this sense (explored further in Lesson 6's coverage of gradient descent).

---

[Previous](./[1]-Limits.md) | [Table of Contents](./[0]-Introduction-to-Calculus.md) | [Next](./[3]-Integrals.md)
