[Previous](./[1]-Logic-And-Proofs.md) | [Table of Contents](./[0]-Introduction-to-Discrete-Mathematics.md) | [Next](./[3]-Combinatorics.md)

# Lesson 2 - Sets, Relations And Functions

Sets are the most basic objects in discrete math — everything else (relations, functions, graphs, even numbers themselves) can be built out of them. This lesson covers set fundamentals, how sets relate to each other via relations, and functions as a special, well-behaved kind of relation.

---

## 2.1 Set Theory Basics

A **set** is an unordered collection of distinct elements. We write sets with curly braces: `A = {1, 2, 3}`. Order doesn't matter and duplicates collapse: `{1, 2, 2, 3} = {1, 2, 3}`.

**Notation you'll see constantly:**

| Symbol | Meaning |
|---|---|
| x ∈ A | x is an element of A |
| x ∉ A | x is not an element of A |
| ∅ or {} | the empty set (contains nothing) |
| \|A\| | cardinality — the number of elements in A |
| A ⊆ B | A is a subset of B (every element of A is also in B) |
| A ⊂ B | A is a proper subset of B (subset, and A ≠ B) |

**Set-builder notation** describes a set by a rule rather than listing elements: `{x ∈ ℤ | x > 0}` reads "the set of all x in the integers such that x is greater than 0" — i.e., the positive integers.

**Set operations:**

| Operation | Symbol | Definition | Example (A = {1,2,3}, B = {2,3,4}) |
|---|---|---|---|
| Union | A ∪ B | elements in A, B, or both | {1,2,3,4} |
| Intersection | A ∩ B | elements in both A and B | {2,3} |
| Difference | A − B | elements in A but not B | {1} |
| Complement | Aᶜ | elements not in A (relative to a universal set U) | depends on U |

**Example:** Let U = {1,...,10} be the universal set, and A = {2,4,6,8,10} (even numbers). Then Aᶜ = {1,3,5,7,9} — the odd numbers in U.

**Power sets:** The power set of A, written P(A), is the set of *all* subsets of A, including ∅ and A itself. If |A| = n, then |P(A)| = 2ⁿ.

**Example:** If A = {a, b}, then P(A) = {∅, {a}, {b}, {a,b}} — 4 subsets, matching 2² = 4.

**Cartesian product:** A × B is the set of all ordered pairs (a, b) where a ∈ A and b ∈ B. If A = {1,2} and B = {x,y}, then A × B = {(1,x), (1,y), (2,x), (2,y)}. Note |A × B| = |A| · |B|.

---

## 2.2 Relations

A **relation** R from set A to set B is simply a subset of A × B — a collection of ordered pairs describing which elements of A are "related to" which elements of B. When A = B, we call it a relation *on* A.

**Example:** Let A = {1, 2, 3}. Define R = {(x, y) | x < y} on A. Then R = {(1,2), (1,3), (2,3)} — every pair where the first is smaller than the second.

**Properties a relation on a set A can have:**

| Property | Definition | Example (on integers) |
|---|---|---|
| Reflexive | (a, a) ∈ R for every a ∈ A | "=" (a = a always) |
| Symmetric | if (a,b) ∈ R then (b,a) ∈ R | "is a sibling of" |
| Antisymmetric | if (a,b) ∈ R and (b,a) ∈ R, then a = b | "≤" |
| Transitive | if (a,b) ∈ R and (b,c) ∈ R, then (a,c) ∈ R | "<", "=", "⊆" |

**Equivalence relations:** A relation that is reflexive, symmetric, *and* transitive is an **equivalence relation** — it partitions a set into disjoint groups ("equivalence classes") of elements that are all related to each other. "Has the same remainder when divided by 3" is an equivalence relation on the integers; it splits all integers into exactly 3 classes.

**Partial orders:** A relation that is reflexive, antisymmetric, and transitive is a **partial order** — it lets you compare *some* pairs of elements without requiring that *every* pair be comparable. "⊆" on sets is a partial order: {1} ⊆ {1,2}, but {1,2} and {2,3} aren't comparable to each other at all under ⊆.

---

## 2.3 Functions Revisited

A **function** f: A → B is a relation from A to B with one extra rule: **every element of A maps to exactly one element of B.** A is the **domain**, B is the **codomain**, and the set of actual outputs {f(a) | a ∈ A} is the **range** (a subset of the codomain, possibly smaller).

**Example:** f: ℤ → ℤ defined by f(x) = x² is a function — every integer has exactly one square. It is *not* a relation like "y is a square root of x," because that would map, say, 4 to both 2 and −2, violating the "exactly one output" rule.

**Three key properties of functions:**

| Property | Definition | Test |
|---|---|---|
| Injective (one-to-one) | different inputs always give different outputs | f(a) = f(b) implies a = b |
| Surjective (onto) | every element of the codomain is hit by some input | for every b ∈ B, some a ∈ A has f(a) = b |
| Bijective | both injective and surjective | a perfect pairing between A and B |

**Example — checking injectivity:** f: ℝ → ℝ, f(x) = x² is **not** injective, because f(2) = f(−2) = 4 — two different inputs give the same output.

**Example — checking surjectivity:** f: ℝ → ℝ, f(x) = x² is also **not** surjective, because negative numbers (like −4) are never outputs — no real x satisfies x² = −4.

**Example — a bijection:** f: ℝ → ℝ, f(x) = 2x + 1 is bijective: it's injective (solve 2a+1 = 2b+1 → a = b), and surjective (for any target y, x = (y−1)/2 always works).

**Why bijections matter:** A bijection between two sets proves they have the *same cardinality* (the same "size," even for infinite sets). This is exactly how computer scientists show two data structures can represent the same information without loss — a bijective encoding is a lossless one.

---

[Previous](./[1]-Logic-And-Proofs.md) | [Table of Contents](./[0]-Introduction-to-Discrete-Mathematics.md) | [Next](./[3]-Combinatorics.md)
