[Previous](./[0]-Introduction-to-Calculus.md) | [Table of Contents](./[0]-Introduction-to-Calculus.md) | [Next](./[2]-Derivatives.md)

# Lesson 1 - Limits

## 1.1 What Is a Limit?

A **limit** describes the value a function *approaches* as its input gets closer and closer to some point — even if the function is never actually evaluated at that exact point.

Written as:

```
lim   f(x) = L
x→a
```

Read as "the limit of `f(x)` as `x` approaches `a` equals `L`." Crucially, this is about the *trend* as you get arbitrarily close to `a` from both sides, not necessarily the value `f(a)` itself.

**Example — a function that's undefined at the point of interest**

```
f(x) = (x^2 - 1) / (x - 1)
```

Plugging in `x = 1` directly gives `0/0`, which is undefined. But look at what happens as `x` gets close to `1`:

| x | 0.9 | 0.99 | 0.999 | → 1 ← | 1.001 | 1.01 | 1.1 |
|---|---|---|---|---|---|---|---|
| f(x) | 1.9 | 1.99 | 1.999 | ? | 2.001 | 2.01 | 2.1 |

From both sides, `f(x)` is clearly heading toward `2`. So:

```
lim   (x^2 - 1)/(x - 1) = 2
x→1
```

even though `f(1)` itself is undefined. This is the core idea limits capture: behavior *near* a point, which turns out to be exactly what's needed to define derivatives and integrals later in this topic.

**One-sided limits:** sometimes a function approaches different values from the left (`x→a⁻`) versus the right (`x→a⁺`). The two-sided limit only exists if both one-sided limits agree.

---

## 1.2 Evaluating Limits

Most limits you'll encounter can be evaluated with a few standard techniques.

**Technique 1 — Direct substitution**

If the function is well-behaved (continuous) at the point, just plug in the value.

```
lim  (3x + 2) = 3(4) + 2 = 14
x→4
```

**Technique 2 — Factoring to cancel the problem term**

Used when direct substitution gives `0/0` — factor and cancel the term causing the issue.

```
lim   (x^2 - 9)/(x - 3)
x→3

= lim  ((x-3)(x+3))/(x-3)     (factor the numerator)
  x→3

= lim  (x + 3)                (cancel the (x-3) terms)
  x→3

= 3 + 3 = 6
```

**Technique 3 — Limits at infinity**

Describes what a function does as `x` grows without bound — essential for understanding long-run/large-input behavior.

```
lim   (2x^2 + 3) / (x^2 - 1)
x→∞
```
Divide every term by the highest power of `x` in the denominator (`x^2`):
```
= lim  (2 + 3/x^2) / (1 - 1/x^2)
  x→∞
```
As `x→∞`, the terms `3/x^2` and `1/x^2` shrink toward `0`:
```
= (2 + 0) / (1 - 0) = 2
```

**When a limit doesn't exist:** if the left and right one-sided limits disagree, or the function oscillates or grows without settling on a value, the limit **does not exist (DNE)**.

---

## 1.3 Continuity

A function is **continuous** at a point `a` if all three of these hold:

1. `f(a)` is defined (the function has a value there)
2. `lim f(x)` as `x→a` exists (the function approaches a specific value)
3. `lim f(x)` as `x→a` **equals** `f(a)` (the approached value matches the actual value)

Informally: a function is continuous if you can draw its graph without lifting your pen.

**Example — a discontinuity**

```
f(x) = 1/x
```
At `x = 0`, `f(0)` is undefined (division by zero), so the function is discontinuous there — it has a **vertical asymptote**, and the one-sided limits don't even agree (`f(x)→-∞` from the left, `f(x)→+∞` from the right).

**Example — a removable discontinuity**

Recall `f(x) = (x^2-1)/(x-1)` from 1.1. The limit as `x→1` exists and equals `2`, but `f(1)` itself is undefined — this is called a **removable discontinuity** because redefining `f(1) = 2` would "patch the hole" and make the function continuous.

**Why this matters in CS:** continuity is the mathematical guarantee that "small changes in input produce small changes in output" — exactly the assumption behind numerical methods (like gradient descent, covered later in this topic), simulations, and any algorithm that relies on smoothly interpolating between known data points. A discontinuous cost function, for example, can break optimization algorithms that assume smooth behavior.

---

[Previous](./[0]-Introduction-to-Calculus.md) | [Table of Contents](./[0]-Introduction-to-Calculus.md) | [Next](./[2]-Derivatives.md)
