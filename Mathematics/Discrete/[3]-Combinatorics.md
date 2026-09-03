[Previous](./[2]-Sets-Relations-And-Functions.md) | [Table of Contents](./[0]-Introduction-to-Discrete-Mathematics.md) | [Next](./[4]-Graph-Theory.md)

# Lesson 3 - Combinatorics

Combinatorics is the mathematics of counting — figuring out how many ways something can happen without listing every possibility by hand. It underpins probability, algorithm complexity analysis (how many states can a program be in?), and cryptography (how many possible keys are there?).

---

## 3.1 Counting Principles

### The Sum Rule

If a task can be done in one of two *disjoint* ways, and the first way has `m` options and the second has `n` options, the total number of options is `m + n`.

**Example:** A menu has 4 vegetarian mains and 6 meat mains, with no overlap. You can pick a main in 4 + 6 = 10 ways.

### The Product Rule

If a task consists of a *sequence* of independent steps, where step 1 has `m` options and step 2 has `n` options, the total number of ways to complete both steps is `m × n`.

**Example:** A password consists of 1 uppercase letter followed by 2 digits. There are 26 choices for the letter and 10 choices for each digit, so the total number of possible passwords is 26 × 10 × 10 = 2,600.

**Combining both rules:** Most real counting problems are a mix. Suppose a meal is either "soup + main" (3 soups × 5 mains = 15 ways) or "salad alone" (4 ways), and these two options don't overlap. Total = 15 + 4 = 19 (product rule inside each branch, sum rule across branches).

### Inclusion-Exclusion

When counting a union of sets that *do* overlap, adding them directly double-counts the overlap. The **Inclusion-Exclusion Principle** for two sets:

|A ∪ B| = |A| + |B| − |A ∩ B|

**Example:** In a class of 30 students, 18 take Math and 15 take Physics, and 8 take both. How many take at least one? 18 + 15 − 8 = 25 students.

---

## 3.2 Permutations and Combinations

Both count ways of choosing items from a set — the difference is entirely about whether **order matters**.

### Permutations (order matters)

The number of ways to arrange all `n` distinct items in a sequence is `n!` (n factorial): n! = n × (n−1) × (n−2) × ... × 1, with 0! defined as 1.

**Example:** How many ways can 5 books be arranged on a shelf? 5! = 120.

To choose and arrange `r` items out of `n` (an **r-permutation**), use:

P(n, r) = n! / (n − r)!

**Example:** In a race with 8 runners, how many ways can gold, silver, and bronze be awarded? Order matters (gold ≠ silver), so P(8, 3) = 8! / 5! = 8 × 7 × 6 = 336.

### Combinations (order doesn't matter)

To choose `r` items out of `n` where order is irrelevant (an **r-combination**), use:

C(n, r) = n! / [r! (n − r)!]

often written as "n choose r" or `(n r)`. Notice C(n, r) = P(n, r) / r! — you take the ordered count and divide out the r! ways each group could have been ordered, since those orderings no longer count as different.

**Example:** From a group of 10 people, how many different 3-person committees can be formed? Since a committee has no internal ordering, C(10, 3) = 10! / (3! × 7!) = (10 × 9 × 8) / (3 × 2 × 1) = 120.

**Side-by-side comparison:**

| Question | Order matters? | Formula | Answer for n=5, r=2 |
|---|---|---|---|
| "How many ways to arrange 2 of 5 books on a shelf?" | Yes | P(5,2) = 5!/3! | 20 |
| "How many ways to pick 2 of 5 books to take on a trip?" | No | C(5,2) = 5!/(2!3!) | 10 |

A quick way to check your intuition: combinations are always ≤ permutations for the same n and r, since combinations collapse each group of r! orderings into a single count.

---

## 3.3 The Pigeonhole Principle

**Statement:** If you place more than `n` items into `n` containers ("pigeonholes"), at least one container must hold more than one item. It sounds almost too simple to be useful, but it produces surprisingly strong guarantees.

**Example — the classic:** If you have 13 people in a room, at least two of them must share a birth month, since there are only 12 months (pigeonholes) but 13 people (items) — 13 > 12, so some month gets at least 2 people.

**Generalized pigeonhole principle:** If `N` items are placed into `k` containers, at least one container has at least `⌈N/k⌉` items (that's N/k rounded *up*).

**Example:** In a group of 100 people, how many are guaranteed to share the same birthday (day of year, ignoring leap years, 365 days)? ⌈100/365⌉ = 1 — not a strong guarantee with only 100 people. But with 400 people, ⌈400/365⌉ = 2, so at least two people are guaranteed to share a birthday.

**Example — a CS application:** Suppose a hash table has 10 buckets and you insert 11 items. By the pigeonhole principle, at least one bucket must contain 2 or more items — a **collision** is *guaranteed*, regardless of how good your hash function is. This is exactly why collision handling (chaining, open addressing) is a required part of any hash table design, not an edge case you can engineer away.

**A less obvious example:** Prove that among any 5 integers, at least two must have the same remainder when divided by 4. There are only 4 possible remainders (0, 1, 2, 3) — the "pigeonholes." With 5 integers (the "pigeons") and only 4 remainder classes, two integers must land in the same class. ∎

---

[Previous](./[2]-Sets-Relations-And-Functions.md) | [Table of Contents](./[0]-Introduction-to-Discrete-Mathematics.md) | [Next](./[4]-Graph-Theory.md)
