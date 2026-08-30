[Previous](./[4]-Graph-Theory.md) | [Table of Contents](./[0]-Introduction-to-Discrete-Mathematics.md) | [Next](./[6]-Number-Theory-Basics.md)

# Lesson 5 - Boolean Algebra

Boolean algebra is the algebra of `true`/`false` (or `1`/`0`) values. It's the mathematical foundation of every `if` statement you've ever written and every logic gate inside a CPU. If Lesson 1 (propositional logic) was about *reasoning* with true/false statements, this lesson is about *computing* with them.

---

## 5.1 Boolean Operations

A **Boolean variable** takes exactly one of two values: `0` (false) or `1` (true). The three fundamental operations mirror the logical connectives from Lesson 1, but are typically written algebraically:

| Operation | Notation | Also written as | Meaning |
|---|---|---|---|
| AND | A · B or AB | A ∧ B | 1 only if both A and B are 1 |
| OR | A + B | A ∨ B | 1 if at least one of A, B is 1 |
| NOT | A' or Ā | ¬A | flips the value: 0 becomes 1, 1 becomes 0 |

**Key algebraic laws** (each has a direct parallel to ordinary algebra, plus some laws that only make sense for Boolean values):

| Law | AND form | OR form |
|---|---|---|
| Identity | A · 1 = A | A + 0 = A |
| Null (Domination) | A · 0 = 0 | A + 1 = 1 |
| Idempotent | A · A = A | A + A = A |
| Complement | A · A' = 0 | A + A' = 1 |
| Commutative | A · B = B · A | A + B = B + A |
| Associative | (A·B)·C = A·(B·C) | (A+B)+C = A+(B+C) |
| Distributive | A·(B+C) = A·B + A·C | A+(B·C) = (A+B)·(A+C) |
| De Morgan's | (A·B)' = A' + B' | (A+B)' = A' · B' |

The **Null law** (`A + 1 = 1`) has no equivalent in ordinary arithmetic — adding 1 doesn't normally force a result to 1! This is a reminder that Boolean algebra follows its own rules, even though the notation borrows `+` and `·` from regular math.

**Simplification example:** Simplify `A·B + A·B'`.

A·B + A·B' = A·(B + B')   [distributive law]
           = A·1          [complement law]
           = A            [identity law]

This kind of simplification is exactly what hardware designers do to reduce the number of physical logic gates needed to implement a circuit — fewer gates means less cost, less power, and less delay.

---

## 5.2 Truth Tables

A **truth table** lists every possible combination of input values and the resulting output — a complete, exhaustive description of a Boolean expression's behavior.

**Truth table for A·B (AND) and A+B (OR):**

| A | B | A·B | A+B |
|---|---|---|---|
| 0 | 0 | 0 | 0 |
| 0 | 1 | 0 | 1 |
| 1 | 0 | 0 | 1 |
| 1 | 1 | 1 | 1 |

**Truth table for a more complex expression, F = A·B + A'·C:**

| A | B | C | A·B | A'·C | F |
|---|---|---|---|---|---|
| 0 | 0 | 0 | 0 | 0 | 0 |
| 0 | 0 | 1 | 0 | 1 | 1 |
| 0 | 1 | 0 | 0 | 0 | 0 |
| 0 | 1 | 1 | 0 | 1 | 1 |
| 1 | 0 | 0 | 0 | 0 | 0 |
| 1 | 0 | 1 | 0 | 0 | 0 |
| 1 | 1 | 0 | 1 | 0 | 1 |
| 1 | 1 | 1 | 1 | 0 | 1 |

With `n` input variables, a truth table has `2ⁿ` rows, since each variable independently doubles the number of combinations — this is the product rule from Lesson 3 in action.

**Why truth tables matter:** two Boolean expressions are equivalent if and only if they produce identical output columns for every row. This gives you a mechanical, no-ambiguity way to verify a simplification (like the A·B + A·B' = A example above) is actually correct.

---

## 5.3 Logic Gates and Circuits

A **logic gate** is a physical (or simulated) device that implements a Boolean operation on electrical signals — the actual building block CPUs are made of.

| Gate | Symbol shape | Boolean equivalent | Truth output |
|---|---|---|---|
| AND | flat-back D shape | A·B | 1 only if both inputs are 1 |
| OR | curved-back shape | A+B | 1 if either input is 1 |
| NOT (inverter) | triangle with a bubble | A' | flips the single input |
| NAND | AND + bubble | (A·B)' | 0 only if both inputs are 1 |
| NOR | OR + bubble | (A+B)' | 1 only if both inputs are 0 |
| XOR | OR with a double curved input line | A ⊕ B | 1 if inputs *differ* |

**XOR deserves special attention** because it doesn't map directly to AND/OR/NOT — it's true when exactly one input is 1, but false when both match:

| A | B | A ⊕ B |
|---|---|---|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

XOR is the operation behind binary addition (adding two bits without carry) and is heavily used in cryptography and error-detection because of this "differs" property.

**NAND and NOR are functionally complete:** remarkably, *every* other logic gate (AND, OR, NOT, XOR, everything) can be built using only NAND gates, or only NOR gates. For example:

- NOT A = NAND(A, A) — feed the same signal into both inputs.
- A AND B = NOT(NAND(A, B)) — a NAND followed by an inverter.

This is why real chips are often built almost entirely out of a single gate type — it simplifies manufacturing enormously.

**Circuit example — building a simple alarm:** Suppose an alarm should trigger (output = 1) if a door sensor (D) is open AND the system is armed (S), OR if a smoke sensor (F) is triggered regardless of arming state. The Boolean expression is:

Alarm = (D · S) + F

Translated to gates: an AND gate takes D and S as inputs, its output feeds into an OR gate along with F, and the OR gate's output drives the alarm. This is exactly how digital circuit design starts — write the Boolean expression from the requirements, then translate directly into gates.

---

[Previous](./[4]-Graph-Theory.md) | [Table of Contents](./[0]-Introduction-to-Discrete-Mathematics.md) | [Next](./[6]-Number-Theory-Basics.md)
