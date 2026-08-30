[Previous](./[5]-Sequences-Series-And-Approximation.md) | [Table of Contents](./[0]-Introduction-to-Calculus.md)

# Lesson 6 - Calculus In Computer Science

## 6.1 Gradient Descent and Optimization

**Gradient descent** is an algorithm for finding the minimum of a function — most often, a machine learning model's **loss function**, which measures how wrong the model's predictions currently are. It puts the derivative and gradient concepts from Lessons 2 and 4 directly to work.

**The core idea**

Recall from Lesson 4 that the gradient, `∇f`, points in the direction of steepest *increase*. To *minimize* a function, you repeatedly step in the *opposite* direction — steepest *decrease* — a little bit at a time:

```
xₙ₊₁ = xₙ - α * ∇f(xₙ)
```

Where:
- `xₙ` is the current position (e.g., the model's current parameters)
- `∇f(xₙ)` is the gradient of the loss function at that position
- `α` (alpha) is the **learning rate** — how big a step to take each time
- `xₙ₊₁` is the updated position after one step

**Worked example — single-variable gradient descent**

Minimize `f(x) = x^2 - 4x + 7` (the same function from Lesson 2.3, where we found the minimum analytically at `x = 2`) using gradient descent instead, starting at `x₀ = 5` with a learning rate `α = 0.1`.

Recall `f'(x) = 2x - 4`.

```
Step 1: x₁ = 5   - 0.1*(2*5 - 4)   = 5   - 0.1*(6)   = 5   - 0.6  = 4.4
Step 2: x₂ = 4.4 - 0.1*(2*4.4 - 4) = 4.4 - 0.1*(4.8) = 4.4 - 0.48 = 3.92
Step 3: x₃ = 3.92 - 0.1*(2*3.92-4) = 3.92 - 0.1*(3.84)= 3.92 - 0.384= 3.536
```

Notice `x` is steadily creeping toward `2`, the true minimum found analytically in Lesson 2 — and the steps get smaller as the slope (`f'(x)`) flattens out near the minimum. Repeating this process enough times converges very close to `x = 2`.

**Why not just solve `f'(x) = 0` directly, like in Lesson 2?** For simple functions, you can. But real machine learning loss functions often depend on millions of parameters and have no clean algebraic solution — gradient descent works iteratively instead, using only the gradient at the current point, which makes it computationally practical at massive scale.

**Choosing the learning rate matters:** too small, and training takes an enormous number of steps to converge; too large, and the steps can overshoot the minimum entirely, causing the algorithm to bounce around or diverge instead of settling down.

---

## 6.2 Calculus in Machine Learning — An Overview

Calculus isn't a side topic in machine learning — it's the mechanism that makes training possible. Here's how the pieces from this entire topic connect:

- **Loss functions (Integrals & Derivatives, Lessons 2–3):** a loss function quantifies how far a model's predictions are from the correct answers. Training a model means minimizing this function — a direct application of the optimization ideas from Lesson 2.3.

- **Gradients (Lesson 4):** modern models have enormous numbers of parameters (weights). The gradient of the loss function with respect to *every* parameter at once tells the training algorithm exactly which direction to adjust each one to reduce error fastest.

- **The Chain Rule (Lesson 2.2) and Backpropagation:** neural networks are, mathematically, functions nested inside functions inside functions — one layer feeding into the next. Computing the gradient through all of those nested layers is done using the chain rule, applied repeatedly. This specific application of the chain rule, working backward from the output layer to the input layer, is called **backpropagation**, and it's the algorithm that actually trains neural networks.

- **Gradient Descent (6.1):** once gradients are known (via backpropagation), gradient descent (or one of its many variants, like Adam or RMSProp, all built on the same core idea) is used to actually update the model's parameters, step by step, epoch by epoch.

- **Taylor Series & Approximation (Lesson 5):** many activation functions and optimization techniques rely on polynomial approximations under the hood, and understanding a function's local behavior via its derivatives (as a Taylor series does) underlies techniques like Newton's Method, an alternative optimization approach that converges faster than gradient descent in some cases by also using second derivatives.

- **Integrals (Lesson 3):** probability distributions used throughout statistics and ML (like the normal distribution) are defined and manipulated using integrals — the area under a probability density function over some range gives the probability of an outcome falling in that range.

**The big picture**

Every time a model "learns" from data, it is running a loop that:
1. Computes predictions and measures error with a loss function.
2. Computes the gradient of that loss with respect to every parameter (via the chain rule / backpropagation).
3. Nudges every parameter slightly in the direction that reduces the loss (via gradient descent).
4. Repeats, often millions of times, until the loss stops meaningfully decreasing.

Every one of those four steps is a direct, practical application of a concept covered in this Calculus topic — which is exactly why a solid grasp of limits, derivatives, gradients, and optimization pays off well beyond the math classroom.

---

This wraps up the Introduction to Calculus series — from the foundational idea of a limit all the way to the exact mechanism that trains modern machine learning models. These foundations build directly on the Algebra topic and support further study in Statistics and applied Machine Learning topics.

[Previous](./[5]-Sequences-Series-And-Approximation.md) | [Table of Contents](./[0]-Introduction-to-Calculus.md)
