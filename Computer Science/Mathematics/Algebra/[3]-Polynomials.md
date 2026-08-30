[Previous](./[2]-Functions.md) | [Table of Contents](./[0]-Introduction-to-Algebra.md) | [Next](./[4]-Systems-Of-Equations.md)

# Lesson 3 - Polynomials

## 3.1 Polynomial Operations

A **polynomial** is an expression made up of terms with non-negative integer exponents on the variable, combined with addition or subtraction:

```
3x^3 - 2x^2 + 5x - 7
```

Key vocabulary:

| Term | Meaning |
|---|---|
| Monomial | A polynomial with one term (`5x^2`) |
| Binomial | A polynomial with two terms (`x + 3`) |
| Trinomial | A polynomial with three terms (`x^2 + 2x + 1`) |
| Degree | The highest exponent on the variable (degree of `3x^3 - 2x^2 + 5x - 7` is `3`) |
| Leading coefficient | The coefficient of the highest-degree term (`3` above) |

**Adding/Subtracting Polynomials**

Combine **like terms** — terms with the same variable raised to the same power.

```
(3x^2 + 2x - 1) + (x^2 - 5x + 4)
= (3x^2 + x^2) + (2x - 5x) + (-1 + 4)
= 4x^2 - 3x + 3
```

**Multiplying Polynomials**

Use the distributive property — every term in the first polynomial multiplies every term in the second. For two binomials, this is often remembered as **FOIL** (First, Outer, Inner, Last):

```
(x + 3)(x + 5)
= x*x + x*5 + 3*x + 3*5     (First, Outer, Inner, Last)
= x^2 + 5x + 3x + 15
= x^2 + 8x + 15
```

**Larger example**

```
(x + 2)(x^2 - 3x + 4)
= x(x^2 - 3x + 4) + 2(x^2 - 3x + 4)
= x^3 - 3x^2 + 4x + 2x^2 - 6x + 8
= x^3 - x^2 - 2x + 8
```

---

## 3.2 Factoring

**Factoring** is the reverse of multiplying — rewriting a polynomial as a product of simpler polynomials. It's one of the most useful algebraic skills because it turns sums into products, which is often the key step in solving equations.

**Method 1 — Greatest Common Factor (GCF)**

Pull out the largest factor common to every term:

```
6x^2 + 9x
= 3x(2x + 3)          (GCF of 6x^2 and 9x is 3x)
```

**Method 2 — Factoring Trinomials**

For `x^2 + bx + c`, find two numbers that **multiply to `c`** and **add to `b`**.

```
x^2 + 8x + 15
```
We need two numbers that multiply to `15` and add to `8` → `3` and `5`.
```
= (x + 3)(x + 5)
```
(Notice this is the reverse of the FOIL example above — a good way to check factoring is to multiply it back out.)

**Method 3 — Difference of Squares**

A special, very common pattern:

```
a^2 - b^2 = (a + b)(a - b)
```

Example:
```
x^2 - 16 = x^2 - 4^2 = (x + 4)(x - 4)
```

**Why this matters in CS:** factoring is the algebraic equivalent of refactoring — pulling shared structure out of an expression, similar to extracting a common subexpression or a shared function in code.

---

## 3.3 Quadratic Equations

A **quadratic equation** is a polynomial equation of degree 2, in the standard form:

```
ax^2 + bx + c = 0    (where a ≠ 0)
```

There are three common ways to solve these.

**Method 1 — Factoring**

```
x^2 + 8x + 15 = 0
(x + 3)(x + 5) = 0
```
By the **Zero Product Property**, if two factors multiply to zero, at least one of them must be zero:
```
x + 3 = 0   or   x + 5 = 0
x = -3      or   x = -5
```

**Method 2 — The Quadratic Formula**

Works for *any* quadratic, even when factoring isn't obvious:

```
x = (-b ± √(b^2 - 4ac)) / 2a
```

**Example:** solve `2x^2 + 3x - 2 = 0` (`a=2, b=3, c=-2`):

```
x = (-3 ± √(3^2 - 4(2)(-2))) / (2*2)
x = (-3 ± √(9 + 16)) / 4
x = (-3 ± √25) / 4
x = (-3 ± 5) / 4
```
So `x = 2/4 = 0.5` or `x = -8/4 = -2`.

**The Discriminant**

The part under the root, `b^2 - 4ac`, is called the **discriminant** — it tells you how many real solutions exist *without solving the whole equation*:

| Discriminant | Meaning |
|---|---|
| `> 0` | Two distinct real solutions |
| `= 0` | Exactly one real solution (a repeated root) |
| `< 0` | No real solutions (two complex solutions) |

**Why this matters in CS:** quadratics show up in projectile/physics simulations, optimization problems, and any time you're modeling area or growth that depends on the square of a variable. The discriminant is a great example of checking a condition cheaply before doing expensive work — much like a guard clause in code.

---

[Previous](./[2]-Functions.md) | [Table of Contents](./[0]-Introduction-to-Algebra.md) | [Next](./[4]-Systems-Of-Equations.md)
