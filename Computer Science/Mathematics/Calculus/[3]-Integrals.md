[Previous](./[2]-Derivatives.md) | [Table of Contents](./[0]-Introduction-to-Calculus.md) | [Next](./[4]-Multivariable-Calculus-Intro.md)

# Lesson 3 - Integrals

## 3.1 The Concept of an Integral

Where a derivative measures instantaneous *rate of change*, an **integral** measures **accumulation** — most commonly, the area under a curve.

Imagine a function `f(x)` plotted on a graph. The integral of `f` between two points `a` and `b` represents the total area trapped between the curve and the x-axis over that interval.

**Building the intuition — Riemann sums**

One way to approximate this area is to slice the region into thin rectangles, each of width `Δx`, with height equal to the function's value at that slice, then add up all the rectangle areas:

```
Area ≈ f(x₁)Δx + f(x₂)Δx + f(x₃)Δx + ... + f(xₙ)Δx
```

This is exactly the summation notation from the Algebra topic's Sequences & Series lesson:

```
        n
Area ≈  Σ  f(xᵢ) Δx
       i=1
```

As you make the rectangles thinner and thinner (more of them, each with smaller `Δx`), the approximation gets more accurate. The integral is defined as what this sum approaches as the rectangle width shrinks toward zero — i.e., a limit of a sum:

```
 b                    n
∫ f(x) dx  =  lim     Σ  f(xᵢ) Δx
 a          Δx→0     i=1
```

The elongated "S" symbol, `∫`, literally stands for "sum." The `dx` represents an infinitesimally small width — the limit of `Δx` as it shrinks to zero.

---

## 3.2 Basic Integration Techniques

Integration is, in a very real sense, the **reverse of differentiation** — finding a function whose derivative is the one you started with. This reverse function is called an **antiderivative**.

**The Power Rule for Integration**

The reverse of the differentiation power rule:

```
∫ x^n dx = x^(n+1) / (n+1) + C     (for n ≠ -1)
```

The `+C` is the **constant of integration** — since the derivative of any constant is `0`, integrating "undoes" the derivative but can't recover any constant that was there originally, so we account for that uncertainty with `C`.

**Example 1**

```
∫ x^3 dx = x^4/4 + C
```

Check by differentiating the result: `d/dx[x^4/4 + C] = 4x^3/4 = x^3` ✅ — it matches what we integrated.

**Example 2 — sum and constant multiple rules apply just like in differentiation**

```
∫ (3x^2 + 2x) dx = 3*(x^3/3) + 2*(x^2/2) + C = x^3 + x^2 + C
```

**Example 3 — a common special case**

```
∫ 1/x dx = ln|x| + C
```

(This is the `n = -1` case the power rule for integration excludes — its antiderivative is a natural logarithm instead.)

---

## 3.3 Definite vs. Indefinite Integrals

**Indefinite integrals** (like every example in 3.2) have no bounds and represent a *family* of antiderivative functions, differing only by the constant `C`:

```
∫ f(x) dx = F(x) + C
```

**Definite integrals** have bounds, `a` and `b`, and evaluate to a single **number** — the actual accumulated area between those two points:

```
 b
∫ f(x) dx
 a
```

**The Fundamental Theorem of Calculus** ties integrals and derivatives together and gives a practical way to compute a definite integral: find the antiderivative `F(x)`, then evaluate it at the bounds and subtract:

```
 b
∫ f(x) dx = F(b) - F(a)
 a
```

**Worked example**

Find the area under `f(x) = x^2` from `x = 1` to `x = 3`:

```
 3
∫ x^2 dx = [x^3/3]  from 1 to 3
 1

= (3^3/3) - (1^3/3)
= (27/3) - (1/3)
= 9 - 0.333...
= 8.667
```

So the area under the curve `y = x^2` between `x=1` and `x=3` is approximately `8.67` square units. Notice the `+C` from the indefinite integral cancels out automatically when you subtract `F(a)` from `F(b)` — which is why it's omitted in definite integral calculations.

**Why this matters in CS:** definite integrals (and their discrete cousin, summation) show up in computing total resource usage over time, calculating probabilities under continuous probability distributions (e.g., in statistics and ML), and numerical methods that approximate accumulated quantities when no clean formula exists — using the same Riemann sum idea from 3.1, just computed directly by a computer instead of taking a limit by hand.

---

[Previous](./[2]-Derivatives.md) | [Table of Contents](./[0]-Introduction-to-Calculus.md) | [Next](./[4]-Multivariable-Calculus-Intro.md)
