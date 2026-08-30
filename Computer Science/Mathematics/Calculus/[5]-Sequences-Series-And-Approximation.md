[Previous](./[4]-Multivariable-Calculus-Intro.md) | [Table of Contents](./[0]-Introduction-to-Calculus.md) | [Next](./[6]-Calculus-In-Computer-Science.md)

# Lesson 5 - Sequences, Series And Approximation

## 5.1 Sequences and Series Recap

This lesson builds directly on the Sequences & Series lesson from the Algebra topic — a quick recap of the essentials before extending them with calculus:

- A **sequence** is an ordered list of terms, `a₁, a₂, a₃, ...`, often following a pattern (arithmetic: constant difference; geometric: constant ratio).
- A **series** is the sum of a sequence's terms, written with summation notation: `Σ aᵢ`.
- An **infinite series** can either **converge** (settle on a finite total) or **diverge** (grow without bound). The infinite geometric series formula, `S = a₁ / (1 - r)` (valid when `|r| < 1`), was the key convergence example covered previously.

**What calculus adds:** in Algebra, sequences and series were treated as patterns of numbers. In calculus, we use them to approximate *functions themselves* — replacing a complicated function with an infinite sum of simple polynomial terms.

**A motivating example**

Consider trying to compute `sin(0.1)` without a calculator's built-in trig function. It turns out:

```
sin(x) ≈ x - x^3/3! + x^5/5! - x^7/7! + ...
```

Plugging in `x = 0.1` and using just the first two terms already gives a highly accurate approximation — this is exactly the kind of series this lesson builds toward.

---

## 5.2 Taylor and Maclaurin Series

A **Taylor series** represents a function as an infinite sum of terms calculated from the function's derivatives at a single point, `a`:

```
f(x) = f(a) + f'(a)(x-a) + f''(a)/2!*(x-a)^2 + f'''(a)/3!*(x-a)^3 + ...
```

Each term uses a higher-order derivative, and the pattern continues indefinitely. The general term is:

```
f⁽ⁿ⁾(a)/n! * (x-a)^n
```

A **Maclaurin series** is simply a Taylor series centered at `a = 0` — the most common and simplest case:

```
f(x) = f(0) + f'(0)x + f''(0)/2!*x^2 + f'''(0)/3!*x^3 + ...
```

**Worked example — building the Maclaurin series for `f(x) = eˣ`**

A key property of `eˣ` is that its derivative is itself: `f'(x) = f''(x) = f'''(x) = ... = eˣ`. Evaluating every derivative at `x=0` gives `e⁰ = 1` every time:

```
f(x) = 1 + 1*x + 1/2!*x^2 + 1/3!*x^3 + ...
     = 1 + x + x^2/2 + x^3/6 + x^4/24 + ...
```

**Checking accuracy:** approximate `e^0.5` using just four terms:

```
1 + 0.5 + 0.5^2/2 + 0.5^3/6
= 1 + 0.5 + 0.125 + 0.0208
= 1.6458
```

The true value of `e^0.5 ≈ 1.6487` — accurate to within `0.003` using only four terms, and it gets more accurate with each additional term.

---

## 5.3 Using Series to Approximate Functions

Taylor/Maclaurin series matter because many functions (`sin`, `cos`, `eˣ`, `ln(x)`, etc.) are expensive or impossible to compute exactly by hand — but their polynomial approximations are made entirely of additions and multiplications, which computers can execute extremely fast.

**Common Maclaurin series worth recognizing:**

```
eˣ      = 1 + x + x^2/2! + x^3/3! + ...
sin(x)  = x - x^3/3! + x^5/5! - x^7/7! + ...
cos(x)  = 1 - x^2/2! + x^4/4! - x^6/6! + ...
1/(1-x) = 1 + x + x^2 + x^3 + ...        (for |x| < 1)
```

**Truncation and error**

Using only a finite number of terms from an infinite series is called **truncating** the series. The more terms you keep, the smaller the **error** between the approximation and the true function value — but also the more computation required. Choosing how many terms to use is a classic engineering trade-off between accuracy and performance.

**Where this connects back to convergence:** a Taylor series is only useful within its **radius of convergence** — the range of `x` values for which the infinite sum actually converges to the function (rather than diverging). `1/(1-x) = 1 + x + x^2 + ...`, for example, only converges for `|x| < 1`, exactly matching the geometric series convergence condition from 5.1.

**Why this matters in CS:** this is precisely how math libraries compute functions like `sin`, `cos`, `exp`, and `log` under the hood — a CPU or GPU doesn't "know trigonometry," it evaluates a truncated polynomial approximation extremely quickly. The same idea (approximating a complicated function with a simpler, computable one) reappears in numerical methods, computer graphics (approximating curves), and machine learning (approximating complex functions with simpler ones during optimization).

---

[Previous](./[4]-Multivariable-Calculus-Intro.md) | [Table of Contents](./[0]-Introduction-to-Calculus.md) | [Next](./[6]-Calculus-In-Computer-Science.md)
