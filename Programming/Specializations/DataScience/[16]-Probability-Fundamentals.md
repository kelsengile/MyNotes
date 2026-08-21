*Statistics & Probability*

# Lesson 16 - Probability Fundamentals

[Previous](./[15]-Correlation-and-Feature-Relationships.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[17]-Probability-Distributions.md)

---

## 16.1 Basic Concepts

**Probability** measures how likely an event is, on a scale from 0 (impossible) to 1 (certain). Key vocabulary:

- **Experiment** — a process with an uncertain outcome (e.g. rolling a die).
- **Sample space** — the set of all possible outcomes.
- **Event** — a subset of outcomes we care about (e.g. "rolling an even number").

```python
# Probability of rolling an even number on a fair six-sided die
favorable_outcomes = 3   # {2, 4, 6}
total_outcomes = 6
probability = favorable_outcomes / total_outcomes   # 0.5
```

---

## 16.2 Independent and Conditional Events

Two events are **independent** if one occurring doesn't change the probability of the other (e.g. two separate coin flips). Otherwise, we use **conditional probability**, written P(A | B) — "the probability of A, given that B happened."

```
P(A and B) = P(A) * P(B)              # if independent
P(A | B)   = P(A and B) / P(B)         # conditional probability, general case
```

---

## 16.3 Bayes' Theorem

**Bayes' Theorem** lets you update a probability based on new evidence — it's the mathematical foundation behind spam filters, medical test interpretation, and many machine learning classifiers (like Naive Bayes):

```
P(A | B) = [ P(B | A) * P(A) ] / P(B)
```

For example, given a medical test with a known accuracy rate, Bayes' Theorem lets you calculate the true probability a patient has a disease *given a positive test result*, which is often surprisingly different from the test's raw accuracy — especially when the disease is rare.

---

## 16.4 Random Variables and Expected Value

A **random variable** assigns a number to each outcome of a random process. The **expected value** is its long-run average — the weighted sum of each outcome times its probability:

```
E(X) = Σ [ x * P(x) ]
```

For example, a game paying $10 with probability 0.2 and $0 otherwise has an expected value of `10 * 0.2 = $2` per play. Expected value is central to decision-making under uncertainty and underpins the probability distributions covered in the next lesson.

---

[Previous](./[15]-Correlation-and-Feature-Relationships.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[17]-Probability-Distributions.md)
