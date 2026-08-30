[Previous](./[0]-Introduction-to-Algebra.md) | [Table of Contents](./[0]-Introduction-to-Algebra.md) | [Next](./[2]-Functions.md)

# Lesson 1 - Foundations Of Algebra

## 1.1 Variables, Expressions, and Equations

A **variable** is a symbol (usually a letter like `x`, `y`, or `n`) that stands in for a value that can change or is unknown. Variables are what let us write general rules instead of one-off arithmetic — the same idea you rely on every time you name a variable in code.

An **expression** is any combination of numbers, variables, and operations that represents a value, but does *not* contain an equals sign:

```
3x + 5
n^2 - 4n
```

An **equation** states that two expressions are equal, using `=`:

```
3x + 5 = 20
```

Solving an equation means finding the value(s) of the variable that make the statement true.

**Example**

Solve `3x + 5 = 20`:

```
3x + 5 = 20
3x     = 15      (subtract 5 from both sides)
 x     = 5       (divide both sides by 3)
```

Check: `3(5) + 5 = 15 + 5 = 20`. ✅

**Terminology recap**

| Term | Example | Meaning |
|---|---|---|
| Constant | `5` | A fixed value that doesn't change |
| Variable | `x` | A placeholder for an unknown value |
| Coefficient | `3` in `3x` | The number multiplying a variable |
| Term | `3x`, `5` | A single piece of an expression, separated by `+` or `-` |
| Expression | `3x + 5` | Terms combined, no `=` |
| Equation | `3x + 5 = 20` | Two expressions set equal |

---

## 1.2 Order of Operations

When an expression has multiple operations, we need a consistent rule for which to do first — otherwise the same expression could evaluate to different answers depending on who's reading it. That rule is commonly remembered with the acronym **PEMDAS**:

1. **P**arentheses — resolve anything inside `( )` first
2. **E**xponents — powers and roots
3. **M**ultiplication and **D**ivision — done left to right, same priority
4. **A**ddition and **S**ubtraction — done left to right, same priority

**Example**

Evaluate `2 + 3 * (4 - 1)^2`:

```
2 + 3 * (4 - 1)^2
2 + 3 * (3)^2       (parentheses)
2 + 3 * 9           (exponent)
2 + 27              (multiplication)
29                  (addition)
```

This is the exact same rule your programming language's parser uses to evaluate `2 + 3 * (4 - 1) ** 2` — algebra's order of operations *is* operator precedence.

**Common mistake:** Multiplication and division are equal priority, done left to right — not "always multiply before you divide." The same goes for addition and subtraction:

```
20 / 4 * 2 = 5 * 2 = 10      (NOT 20 / 8 = 2.5)
```

---

## 1.3 Solving Linear Equations

A **linear equation** is one where the variable appears only to the first power (no `x^2`, no `1/x`, no `x` inside a root). Its graph is always a straight line, hence the name.

**Goal:** isolate the variable on one side using inverse operations, always applying the same operation to both sides to keep the equation balanced.

**Example 1 — one variable, one step**

```
x + 7 = 12
x     = 5        (subtract 7 from both sides)
```

**Example 2 — variable on both sides**

```
5x - 3 = 2x + 9
3x - 3 = 9        (subtract 2x from both sides)
3x     = 12       (add 3 to both sides)
 x     = 4        (divide both sides by 3)
```

**Example 3 — with distribution**

```
2(x + 3) = 16
2x + 6   = 16     (distribute the 2)
2x       = 10     (subtract 6)
 x       = 5      (divide by 2)
```

**Why this matters in CS:** solving linear equations is the same skill used to balance loop bounds, work out array offsets, and reason about time budgets in performance calculations (e.g., "if each request takes `x` ms, how many can I process in 2 seconds?").

---

## 1.4 Inequalities

An **inequality** compares two expressions using `<`, `>`, `≤`, or `≥` instead of `=`. It describes a *range* of valid values rather than a single one.

| Symbol | Meaning |
|---|---|
| `<` | less than |
| `>` | greater than |
| `≤` | less than or equal to |
| `≥` | greater than or equal to |

Solving inequalities works almost exactly like solving equations, with **one critical rule**:

> **Flip the inequality sign whenever you multiply or divide both sides by a negative number.**

**Example 1**

```
2x + 3 < 11
2x     < 8        (subtract 3)
 x     < 4        (divide by 2, positive — sign stays)
```

**Example 2 — the flip rule in action**

```
-3x ≥ 12
  x ≤ -4          (divide by -3, negative — flip ≥ to ≤)
```

Check with a value: if `x = -5`, then `-3(-5) = 15`, and `15 ≥ 12` is true. If we hadn't flipped the sign, we'd have wrongly concluded `x ≥ -4`, which `-5` doesn't satisfy.

**Why this matters in CS:** inequalities show up constantly in bounds checking (`0 ≤ i < array.length`), algorithm constraints, and Big-O analysis, where you're often proving one function is bounded above or below by another.

---

[Previous](./[0]-Introduction-to-Algebra.md) | [Table of Contents](./[0]-Introduction-to-Algebra.md) | [Next](./[2]-Functions.md)
