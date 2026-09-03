[Previous](./[5]-Boolean-Algebra.md) | [Table of Contents](./[0]-Introduction-to-Discrete-Mathematics.md) | [Next](./[7]-Automata-And-Formal-Languages.md)

# Lesson 6 - Number Theory Basics

Number theory studies the properties of integers — divisibility, primes, and remainders. It might sound purely theoretical, but it's the direct foundation of modern cryptography (RSA encryption relies entirely on properties of primes) and shows up constantly in hashing, checksums, and random number generation.

---

## 6.1 Divisibility and Primes

**Divisibility:** We say `a` divides `b` (written `a | b`) if there exists an integer `k` such that `b = a·k` — in other words, `b / a` has no remainder.

**Example:** 4 | 20 because 20 = 4 × 5. But 4 ∤ 21 (4 does *not* divide 21), since 21/4 leaves a remainder.

**Useful divisibility facts:**
- If a | b and a | c, then a | (b + c) and a | (b − c).
- If a | b and b | c, then a | c (transitivity).
- Every integer n > 1 is divisible by at least 1 and itself.

**Prime numbers:** An integer `p > 1` is **prime** if its only positive divisors are 1 and itself. Numbers greater than 1 that aren't prime are called **composite** — they can be factored into smaller positive integers other than 1 and themselves. (Note: 1 is neither prime nor composite, by definition.)

**Example:** 2, 3, 5, 7, 11, 13 are prime. 4 = 2×2, 9 = 3×3, and 15 = 3×5 are composite.

**The Fundamental Theorem of Arithmetic:** Every integer greater than 1 can be written as a product of primes in exactly one way (ignoring the order of factors). This is why primes are called the "building blocks" of the integers.

**Example — prime factorization:** 84 = 2 × 2 × 3 × 7 = 2² × 3 × 7. No other combination of primes multiplies to 84.

**Why this matters for CS:** RSA public-key encryption relies on the fact that multiplying two large primes together is computationally easy, but factoring the resulting large number *back* into its two prime factors is computationally hard (given current algorithms and hardware). That asymmetry — easy one direction, hard the other — is the entire basis of the security guarantee.

---

## 6.2 Modular Arithmetic

**Modular arithmetic** deals with remainders. We write `a mod n` for the remainder when `a` is divided by `n`. Two integers `a` and `b` are **congruent modulo n**, written `a ≡ b (mod n)`, if they leave the same remainder when divided by `n` — equivalently, if `n | (a − b)`.

**Example:** 17 mod 5 = 2, and 7 mod 5 = 2 as well, so 17 ≡ 7 (mod 5).

**A useful mental model:** clock arithmetic. A 12-hour clock is arithmetic mod 12 — if it's 9 o'clock and 5 hours pass, you land on (9 + 5) mod 12 = 2 o'clock, not 14 o'clock.

**Arithmetic rules that make modular math easy to work with (all mod n):**

- (a + b) mod n = [(a mod n) + (b mod n)] mod n
- (a × b) mod n = [(a mod n) × (b mod n)] mod n

This means you can reduce numbers *before* combining them, which is exactly how computers handle huge modular exponentiation (as used in cryptography) without ever storing astronomically large intermediate numbers.

**Example:** Compute (23 × 41) mod 7 without first computing 943.
23 mod 7 = 2, and 41 mod 7 = 6.
(2 × 6) mod 7 = 12 mod 7 = 5.
Check: 943 mod 7 = 5. ✓ Matches, and was far less arithmetic.

**Where you've already used this:** hash tables commonly compute `index = hash(key) mod table_size` to map an arbitrary key into a fixed range of bucket indices. Modular arithmetic is what makes that mapping possible.

---

## 6.3 GCD and the Euclidean Algorithm

The **greatest common divisor** of two integers `a` and `b`, written `gcd(a, b)`, is the largest positive integer that divides both of them.

**Example:** gcd(24, 36) = 12, since 12 is the largest number dividing both 24 and 36.

Listing all divisors and comparing works for small numbers, but is far too slow for large ones. The **Euclidean Algorithm** computes gcd efficiently using one key fact:

gcd(a, b) = gcd(b, a mod b)

Repeating this — replacing the pair `(a, b)` with `(b, a mod b)` — shrinks the numbers quickly until the remainder hits 0. At that point, the *other* number in the pair is the gcd.

**Example — computing gcd(252, 105) step by step:**

| Step | a | b | a mod b |
|---|---|---|---|
| 1 | 252 | 105 | 42 |
| 2 | 105 | 42 | 21 |
| 3 | 42 | 21 | 0 |

When the remainder hits 0, the algorithm stops, and the gcd is the last nonzero value of `b`, which is **21**. Check: 252 = 21 × 12, and 105 = 21 × 5 — 21 divides both, and no larger number does.

**Why the Euclidean Algorithm is fast:** each step roughly halves the size of the numbers involved (in the worst case, related to Fibonacci numbers), so it runs in a number of steps proportional to the number of *digits* in the input, not the size of the input itself — dramatically faster than checking every possible divisor.

**Least Common Multiple (LCM):** closely related to gcd via the identity:

lcm(a, b) = (a × b) / gcd(a, b)

**Example:** lcm(4, 6): gcd(4, 6) = 2, so lcm(4, 6) = (4 × 6) / 2 = 12.

**CS applications:** the Euclidean Algorithm is a core step in the RSA key-generation process (finding modular inverses relies on a variant called the Extended Euclidean Algorithm), and gcd/lcm calculations show up in scheduling problems (e.g., "these two repeating events next coincide after lcm(period1, period2) time units").

---

[Previous](./[5]-Boolean-Algebra.md) | [Table of Contents](./[0]-Introduction-to-Discrete-Mathematics.md) | [Next](./[7]-Automata-And-Formal-Languages.md)
