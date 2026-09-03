[Previous](./[3]-Random-Variables-And-Expected-Value.md) | [Table of Contents](./[0]-Introduction-to-Statistics.md) | [Next](./[5]-Inferential-Statistics.md)

# Lesson 4 - Probability Distributions

A **probability distribution** describes, for every possible value a random variable can take, how likely that value is. Rather than deriving one from scratch for every new problem, statisticians rely on a handful of well-studied "named" distributions that show up again and again in real data.

---

## 4.1 Discrete Distributions (Binomial, Poisson)

### Binomial Distribution

Models the number of successes in a fixed number of independent trials, where each trial has the same probability of success. A random variable X follows a **binomial distribution** with parameters `n` (number of trials) and `p` (probability of success on each trial), written X ~ Binomial(n, p), when:

- There are a fixed number `n` of trials.
- Each trial is independent of the others.
- Each trial has only two outcomes ("success" or "failure").
- The probability of success `p` is the same on every trial.

**PMF (probability of exactly k successes):**

P(X = k) = C(n, k) · pᵏ · (1−p)ⁿ⁻ᵏ

where C(n, k) is the combination formula from combinatorics — "n choose k" — counting how many different *orders* of successes and failures produce exactly k successes.

**Mean and variance:** E(X) = np, Var(X) = np(1−p).

**Example:** A fair coin is flipped 10 times. What's the probability of getting exactly 6 heads? Here n=10, p=0.5, k=6.

P(X=6) = C(10,6) · (0.5)⁶ · (0.5)⁴ = 210 · (0.5)¹⁰ = 210/1024 ≈ **0.205**

The coin-flip example from Lesson 3 (X = number of heads in 3 flips) was actually a Binomial(3, 0.5) distribution the whole time — the general formulas here reproduce those results exactly, and let you scale to any n and p without recomputing from scratch.

### Poisson Distribution

Models the number of times a rare, independent event occurs in a fixed interval of time or space, when events happen at a known constant average rate λ ("lambda"), and one event happening doesn't make the next one more or less likely.

**PMF:**

P(X = k) = (λᵏ · e⁻λ) / k!

**Mean and variance:** E(X) = λ, Var(X) = λ — uniquely, the mean and variance are equal for a Poisson distribution.

**Example:** A call center receives an average of 4 calls per minute (λ = 4). What's the probability of receiving exactly 2 calls in a given minute?

P(X=2) = (4² · e⁻⁴) / 2! = (16 × 0.0183) / 2 ≈ **0.147**

**Common Poisson use cases:** modeling website traffic spikes, number of typos per page, number of server requests per second, or number of defects per manufactured unit — anywhere you're counting rare, independent occurrences over a fixed window.

---

## 4.2 Continuous Distributions (Normal, Uniform)

Continuous random variables can take *any* value within a range, so instead of a PMF (which would assign 0 probability to every individual point), we use a **probability density function (PDF)**, where probability corresponds to the *area under the curve* over an interval, not the height at a single point.

### Uniform Distribution

Every value in an interval [a, b] is equally likely. The PDF is a flat, constant line: f(x) = 1/(b−a) for a ≤ x ≤ b, and 0 elsewhere.

**Example:** A bus arrives randomly, uniformly, at some point between 0 and 20 minutes after the hour. The probability it arrives in the first 5 minutes is simply the proportion of the interval covered: 5/20 = 0.25 — with a uniform distribution, probability over any sub-interval is just its length relative to the whole range.

**Mean and variance:** E(X) = (a+b)/2, Var(X) = (b−a)²/12.

### Normal (Gaussian) Distribution

The single most important continuous distribution in statistics — the familiar symmetric "bell curve." Defined by two parameters: mean μ (where the peak/center is) and standard deviation σ (how wide the spread is), written X ~ N(μ, σ²).

**The Empirical Rule (68-95-99.7 Rule):** for any normal distribution:

| Range | Approx. % of data contained |
|---|---|
| μ ± 1σ | 68% |
| μ ± 2σ | 95% |
| μ ± 3σ | 99.7% |

**Example:** Adult heights are approximately normal with μ = 170 cm and σ = 10 cm. About 68% of adults fall between 160cm and 180cm (μ ± 1σ), and about 95% fall between 150cm and 190cm (μ ± 2σ). Someone at 200cm is 3 standard deviations above the mean — genuinely rare, in roughly the top 0.15% of the distribution.

**The Standard Normal Distribution (Z-scores):** Any normal distribution can be converted to the "standard" normal N(0, 1) using:

Z = (X − μ) / σ

A **Z-score** tells you how many standard deviations a value is from the mean, letting you compare values from *different* normal distributions on the same scale, and look up exact probabilities in a standard table.

**Example:** For the height distribution above (μ=170, σ=10), what's the Z-score for someone 185cm tall? Z = (185 − 170)/10 = 1.5 — they're 1.5 standard deviations above average.

**Why the normal distribution is everywhere:** the **Central Limit Theorem** (covered in the next lesson) guarantees that sums and averages of many independent random quantities tend toward a normal distribution, *regardless of the shape of the original data* — which is exactly why so many natural and measured quantities (heights, measurement errors, test scores) end up looking approximately normal in practice.

---

[Previous](./[3]-Random-Variables-And-Expected-Value.md) | [Table of Contents](./[0]-Introduction-to-Statistics.md) | [Next](./[5]-Inferential-Statistics.md)
