[Previous](./[2]-Probability-Basics.md) | [Table of Contents](./[0]-Introduction-to-Statistics.md) | [Next](./[4]-Probability-Distributions.md)

# Lesson 3 - Random Variables And Expected Value

Random variables let us attach *numbers* to the outcomes of a random process, which is what makes it possible to compute averages, variances, and eventually build full probability distributions out of them.

---

## 3.1 What Is a Random Variable?

A **random variable** (often written X) is a function that assigns a numeric value to each outcome in a sample space. It isn't "random" in the sense of being unpredictable in its definition — it's a well-defined function — the randomness comes from which outcome actually occurs.

**Example:** Flip a coin 3 times. Let X = "the number of heads." The sample space has 8 equally likely outcomes (HHH, HHT, ..., TTT), and X maps each one to a number:

| Outcome | X (number of heads) |
|---|---|
| TTT | 0 |
| HTT, THT, TTH | 1 |
| HHT, HTH, THH | 2 |
| HHH | 3 |

**Discrete vs. continuous random variables:**

| Type | Description | Example |
|---|---|---|
| Discrete | takes on a countable set of values (often integers) | number of heads in 3 flips, number of customers per hour |
| Continuous | takes on any value in a range (uncountably many) | height, temperature, time until failure |

For a discrete random variable, we describe its behavior with a **probability mass function (PMF)**: P(X = x) for each possible value x. From the coin example: P(X=0) = 1/8, P(X=1) = 3/8, P(X=2) = 3/8, P(X=3) = 1/8. Notice these probabilities sum to 1, as they must for any valid PMF.

---

## 3.2 Expected Value

The **expected value** (or mean) of a random variable, written E(X) or μ, is the long-run average value you'd see if you repeated the random process many, many times. For a discrete random variable:

E(X) = Σ [x · P(X = x)]   (sum over every possible value x)

**Example:** Using the coin-flip example above:

E(X) = 0(1/8) + 1(3/8) + 2(3/8) + 3(1/8) = 0 + 3/8 + 6/8 + 3/8 = 12/8 = **1.5**

This matches intuition: flipping a fair coin 3 times, you'd expect 1.5 heads on average — even though 1.5 is not a value X can actually take on any single trial. Expected value describes the *long-run average*, not a guaranteed or even possible single outcome.

**Example — a simple game:** You pay $2 to roll a die. If you roll a 6, you win $10; otherwise, you win nothing. Is this a good bet? Let X = net profit.

P(X = $8) = 1/6 (rolled a 6: $10 won − $2 paid)
P(X = −$2) = 5/6 (didn't roll a 6: $0 won − $2 paid)

E(X) = 8(1/6) + (−2)(5/6) = 8/6 − 10/6 = −2/6 ≈ **−$0.33**

On average, you lose about 33 cents every time you play — this is exactly the calculation casinos and lotteries run (in their own favor) to guarantee long-run profitability, regardless of any single game's outcome.

**Linearity of expectation** — a property so useful it's worth memorizing on its own: for any random variables X and Y (even if they're *dependent*), and any constants a, b:

E(aX + bY) = a·E(X) + b·E(Y)

This holds *even when X and Y are not independent*, which makes it one of the most powerful shortcuts in probability — you can often find E(X) for a complicated combined quantity by breaking it into simpler pieces and adding up their individual expectations.

---

## 3.3 Variance of a Random Variable

Just like with a raw dataset (Lesson 1), knowing the expected value alone doesn't tell you how spread out a random variable's outcomes tend to be. **Variance** of a random variable measures exactly that:

Var(X) = E[(X − μ)²] = E(X²) − [E(X)]²

The second form (E(X²) − [E(X)]²) is almost always easier to compute in practice and is the formula you'll use most often.

**Worked example — variance of the coin-flip count X from section 3.1:**

We already found E(X) = 1.5. Now compute E(X²) = Σ [x² · P(X=x)]:

E(X²) = 0²(1/8) + 1²(3/8) + 2²(3/8) + 3²(1/8) = 0 + 3/8 + 12/8 + 9/8 = 24/8 = 3

Var(X) = E(X²) − [E(X)]² = 3 − (1.5)² = 3 − 2.25 = **0.75**

Standard deviation: SD(X) = √0.75 ≈ 0.866.

**Variance of a linear transformation:** if Y = aX + b, then Var(Y) = a² · Var(X) — note the *b* (a shift) has no effect on spread, and the coefficient *a* gets squared. This makes sense intuitively: shifting every value by a constant doesn't change how spread out they are relative to each other, but scaling them stretches the spread by that same factor.

**Why this matters going forward:** expected value and variance are the two numbers that define the "shape" of many named probability distributions covered in the next lesson — once you know a distribution's mean and variance, you often know almost everything you need to reason about it.

---

[Previous](./[2]-Probability-Basics.md) | [Table of Contents](./[0]-Introduction-to-Statistics.md) | [Next](./[4]-Probability-Distributions.md)
