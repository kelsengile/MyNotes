[Previous](./[0]-Introduction-to-Discrete-Mathematics.md) | [Table of Contents](./[0]-Introduction-to-Discrete-Mathematics.md) | [Next](./[2]-Sets-Relations-And-Functions.md)

# Lesson 1 - Logic And Proofs

Logic is the backbone of mathematics and computer science. Before you can prove an algorithm correct, verify a database query, or write a boolean condition in code, you need a precise way to talk about statements being true or false, and how truth flows from one statement to another. This lesson builds that foundation.

---

## 1.1 Propositional Logic

A **proposition** is a statement that is either true (`T`) or false (`F`), but never both, and never unknown. "5 is a prime number" is a proposition (true). "What time is it?" is not a proposition — it's a question, not a statement with a truth value.

We combine propositions using **logical connectives**:

| Connective | Symbol | Name | Meaning |
|---|---|---|---|
| Negation | ¬p | NOT | True when p is false |
| Conjunction | p ∧ q | AND | True only when both p and q are true |
| Disjunction | p ∨ q | OR | True when at least one of p, q is true |
| Conditional | p → q | IMPLIES | False only when p is true and q is false |
| Biconditional | p ↔ q | IFF | True when p and q have the same truth value |

**Truth table for the conditional (the one people misread most often):**

| p | q | p → q |
|---|---|---|
| T | T | T |
| T | F | F |
| F | T | T |
| F | F | T |

The surprising rows are the last two: if `p` is false, `p → q` is automatically true, regardless of `q`. Think of it as a promise: "If it rains, I'll bring an umbrella." If it doesn't rain, you haven't broken your promise no matter what you do with the umbrella.

**Example:** Let `p` = "n is divisible by 4" and `q` = "n is divisible by 2". The statement `p → q` reads "if n is divisible by 4, then n is divisible by 2" — this is true for every integer n, since any multiple of 4 is automatically a multiple of 2.

**Logical equivalence:** Two statements are logically equivalent if they have identical truth tables for every combination of inputs. Two equivalences worth memorizing because they show up constantly in code (De Morgan's Laws):

- ¬(p ∧ q) ≡ ¬p ∨ ¬q
- ¬(p ∨ q) ≡ ¬p ∧ ¬q

In plain English: "NOT (A and B)" means "NOT A, or NOT B." This is exactly why `!(x > 0 && y > 0)` in code is equivalent to `!(x > 0) || !(y > 0)`, i.e. `x <= 0 || y <= 0`.

---

## 1.2 Predicate Logic

Propositional logic can't express statements like "every integer has a successor," because that involves a variable ranging over a whole domain. **Predicate logic** fixes this by introducing:

- **Predicates**: statements that depend on a variable, e.g. `P(x)` = "x is even." `P(x)` isn't true or false on its own — it becomes a proposition once you plug in a value, like `P(4)` (true) or `P(7)` (false).
- **Quantifiers**: symbols that state how many elements of a domain satisfy a predicate.

| Quantifier | Symbol | Reads as | True when... |
|---|---|---|---|
| Universal | ∀x P(x) | "for all x, P(x)" | P(x) holds for every x in the domain |
| Existential | ∃x P(x) | "there exists an x such that P(x)" | P(x) holds for at least one x in the domain |

**Example:** Let the domain be all integers, and `P(x)` = "x² ≥ 0."
- `∀x P(x)` is **true** — every integer squared is non-negative.

Let `Q(x)` = "x is prime and x is even."
- `∃x Q(x)` is **true** — x = 2 satisfies it (and it's the *only* one, but existential just needs one).

**Negating quantifiers** follows a predictable pattern, sometimes called the "quantifier De Morgan's Laws":

- ¬(∀x P(x)) ≡ ∃x ¬P(x)  — "not everyone passed" means "someone failed"
- ¬(∃x P(x)) ≡ ∀x ¬P(x)  — "no one passed" means "everyone failed"

This matters in practice: to disprove "all users have a valid email" (∀x P(x)), you only need to produce *one* counterexample (∃x ¬P(x)) — you don't need to check every user.

---

## 1.3 Proof Techniques (Direct, Contradiction)

A **proof** is a logically airtight argument that a statement is true, built from definitions, previously proven facts, and valid rules of inference. Two of the most common techniques:

### Direct Proof

To prove `p → q` directly: assume `p` is true, and use valid logical steps to show `q` must also be true.

**Example — Prove: if n is an even integer, then n² is even.**

1. Assume n is even. By definition, n = 2k for some integer k.
2. Then n² = (2k)² = 4k² = 2(2k²).
3. Since 2k² is an integer, n² is 2 times an integer — so n² is even. ∎

Notice how each step follows mechanically from the definition of "even" (a number is even if it equals 2 times an integer).

### Proof by Contradiction

To prove a statement `p`, assume `¬p` (that it's *false*) and derive a logical contradiction — something that can't possibly be true, like `1 = 0` or "x is both even and odd." Since a true assumption can never lead to a contradiction, `¬p` must be false, meaning `p` is true.

**Example — Prove: √2 is irrational.**

1. Assume, for contradiction, that √2 *is* rational. Then √2 = a/b for integers a, b with no common factors (the fraction is fully reduced).
2. Squaring both sides: 2 = a²/b², so a² = 2b². This means a² is even, which means a itself must be even (from the direct proof above, contrapositive-style). So a = 2k for some integer k.
3. Substituting: (2k)² = 2b² → 4k² = 2b² → b² = 2k². So b² is even, meaning b is also even.
4. But if both a and b are even, they share a common factor of 2 — contradicting our assumption that the fraction was fully reduced.
5. This contradiction means our original assumption was false, so √2 is irrational. ∎

This is one of the oldest known proofs in mathematics and a great illustration of why contradiction is such a powerful tool: some facts are far easier to prove by showing the *opposite* is impossible than by direct construction.

---

## 1.4 Mathematical Induction in Practice

**Induction** proves a statement `P(n)` holds for every natural number n (usually starting at n = 0 or n = 1) using two steps:

1. **Base case:** Show `P(base)` is true (usually the smallest value, like n = 1).
2. **Inductive step:** Assume `P(k)` is true for some arbitrary k (this assumption is called the **inductive hypothesis**), then show that `P(k+1)` must also be true.

If both steps hold, `P(n)` is true for all n starting at the base case — like an infinite row of dominoes: if the first one falls, and each domino falling knocks over the next, they all fall.

**Example — Prove: 1 + 2 + 3 + ... + n = n(n+1)/2 for all n ≥ 1.**

**Base case (n = 1):** Left side = 1. Right side = 1(2)/2 = 1. They match. ✓

**Inductive step:** Assume `P(k)` is true, i.e. 1 + 2 + ... + k = k(k+1)/2. We want to show `P(k+1)` holds: 1 + 2 + ... + k + (k+1) = (k+1)(k+2)/2.

Starting from the left side of `P(k+1)` and using the inductive hypothesis to replace the sum up to k:

1 + 2 + ... + k + (k+1) = [k(k+1)/2] + (k+1)

Factor out (k+1):

= (k+1)[k/2 + 1] = (k+1)(k+2)/2

This matches the right side of `P(k+1)` exactly. ✓

Since the base case holds and the inductive step holds, the formula is true for all n ≥ 1. ∎

**Where this shows up in CS:** induction is the formal justification behind proving loop invariants and recursive algorithm correctness — e.g. showing a recursive function that sums a list correctly handles a list of size k+1, given that it works for size k.

---

[Previous](./[0]-Introduction-to-Discrete-Mathematics.md) | [Table of Contents](./[0]-Introduction-to-Discrete-Mathematics.md) | [Next](./[2]-Sets-Relations-And-Functions.md)
