[Previous](./[3]-Integrals.md) | [Table of Contents](./[0]-Introduction-to-Calculus.md) | [Next](./[5]-Sequences-Series-And-Approximation.md)

# Lesson 4 - Multivariable Calculus (Intro)

## 4.1 Partial Derivatives

Every function up to this point has had a single input, `x`. But many real functions — especially in machine learning — depend on *multiple* inputs at once:

```
f(x, y) = x^2 + 3xy + y^2
```

A **partial derivative** measures the rate of change of a multivariable function with respect to *one* variable, while treating every other variable as a constant. It's written with a stylized "curly d," `∂`, instead of the ordinary `d`.

**Notation:**

```
∂f/∂x    — the partial derivative of f with respect to x
∂f/∂y    — the partial derivative of f with respect to y
```

**Worked example**

For `f(x, y) = x^2 + 3xy + y^2`:

**Finding `∂f/∂x`** — treat `y` as a constant:

```
∂f/∂x = 2x + 3y + 0 = 2x + 3y
```
(`x^2` differentiates normally to `2x`; `3xy` is `3x` times the constant `y`, so it differentiates to `3y`; `y^2` is treated as a plain constant, so it differentiates to `0`.)

**Finding `∂f/∂y`** — treat `x` as a constant:

```
∂f/∂y = 0 + 3x + 2y = 3x + 2y
```
(`x^2` is now the constant, differentiating to `0`; `3xy` is `3y` times constant `x`, differentiating to `3x`; `y^2` differentiates normally to `2y`.)

**Interpreting the result:** at a specific point, say `(x, y) = (1, 2)`, `∂f/∂x = 2(1) + 3(2) = 8` tells you how steeply `f` rises if you nudge `x` slightly while holding `y` fixed at `2`. All the differentiation rules from Lesson 2 (power rule, product rule, chain rule) still apply — you're just applying them to one variable at a time.

---

## 4.2 Gradients

The **gradient** of a multivariable function collects *all* of its partial derivatives into a single vector. For `f(x, y)`, the gradient is written `∇f` (pronounced "del f" or "grad f"):

```
∇f(x, y) = [ ∂f/∂x , ∂f/∂y ]
```

**Worked example**

Using the partial derivatives found in 4.1 for `f(x, y) = x^2 + 3xy + y^2`:

```
∇f(x, y) = [ 2x + 3y , 3x + 2y ]
```

At the point `(1, 2)`:

```
∇f(1, 2) = [ 2(1)+3(2) , 3(1)+2(2) ] = [ 8, 7 ]
```

**What the gradient represents, geometrically**

The gradient vector at a point always points in the direction of the **steepest increase** of the function at that point — imagine standing on a hill described by `f(x,y)`, where `x` and `y` are your position and `f` is your elevation; the gradient points straight uphill in the steepest direction. Its opposite, `-∇f`, points in the direction of **steepest decrease**.

The gradient's *magnitude* (its length, using the same formula from the Algebra topic's vector lesson: `√(a² + b²)`) tells you *how steep* that steepest direction is — a larger magnitude means a steeper slope.

**Setting the gradient to zero**

Just as setting `f'(x) = 0` finds critical points for single-variable functions (Lesson 2.3), setting **every** entry of `∇f` to zero finds critical points for multivariable functions — points that could be a local max, local min, or a "saddle point" (a max in one direction and a min in another).

**Why this matters in CS:** the gradient is the single most important concept behind training modern machine learning models. A model's loss function depends on potentially millions of parameters at once; the gradient tells you, all at once, which direction to nudge every parameter to reduce that loss the fastest — which is precisely the idea behind **gradient descent**, covered in the final lesson of this topic.

---

[Previous](./[3]-Integrals.md) | [Table of Contents](./[0]-Introduction-to-Calculus.md) | [Next](./[5]-Sequences-Series-And-Approximation.md)
