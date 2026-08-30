[Previous](./[1]-Descriptive-Statistics.md) | [Table of Contents](./[0]-Introduction-to-Statistics.md) | [Next](./[3]-Random-Variables-And-Expected-Value.md)

# Lesson 2 - Probability Basics

Probability is the mathematics of uncertainty — it lets us assign precise numbers to how likely an event is, rather than relying on vague words like "probably" or "unlikely." It's the theoretical foundation everything else in this topic builds on.

---

## 2.1 Sample Spaces and Events

The **sample space**, written S (or Ω), is the set of *all possible outcomes* of a random process. An **event** is any subset of the sample space — a collection of outcomes we're interested in.

**Example:** Rolling a standard six-sided die. The sample space is S = {1, 2, 3, 4, 5, 6}. The event "rolling an even number" is E = {2, 4, 6} — a subset of S.

**Example — a compound experiment:** Flipping a coin twice. The sample space is S = {HH, HT, TH, TT}. The event "at least one heads" is E = {HH, HT, TH} — three of the four equally likely outcomes.

**Types of events:**

| Term | Meaning |
|---|---|
| Simple event | contains exactly one outcome |
| Compound event | contains more than one outcome |
| Complement (Eᶜ) | all outcomes in S that are *not* in E |
| Mutually exclusive | two events that share no outcomes (can't both happen at once) |

**Example:** For the die roll, "rolling an even number" (E1 = {2,4,6}) and "rolling an odd number" (E2 = {1,3,5}) are mutually exclusive — no outcome belongs to both — and together they cover the entire sample space.

---

## 2.2 Probability Rules

The probability of an event E, written P(E), is defined for equally likely outcomes as:

P(E) = |E| / |S|   (number of favorable outcomes ÷ total outcomes)

**Example:** P(rolling an even number) = |{2,4,6}| / |{1,2,3,4,5,6}| = 3/6 = 0.5.

**Foundational axioms (rules every valid probability must satisfy):**

1. 0 ≤ P(E) ≤ 1 for any event E — probabilities are never negative or greater than 1.
2. P(S) = 1 — something in the sample space is guaranteed to happen.
3. If E1 and E2 are mutually exclusive, P(E1 ∪ E2) = P(E1) + P(E2).

**Complement rule:** P(Eᶜ) = 1 − P(E). This is often the fastest route to an answer when directly computing P(E) is awkward.

**Example:** What's the probability of rolling at least a 2 on a die? Direct computation requires adding P(2)+P(3)+P(4)+P(5)+P(6). Easier: P(at least 2) = 1 − P(rolling a 1) = 1 − 1/6 = 5/6.

**Addition rule for events that overlap (a direct echo of Inclusion-Exclusion from combinatorics):**

P(A ∪ B) = P(A) + P(B) − P(A ∩ B)

**Example:** In a deck of 52 cards, let A = "drawing a heart" (13 cards) and B = "drawing a face card" (12 cards: J, Q, K in each suit). There are 3 cards that are both a heart and a face card (J♥, Q♥, K♥). P(A ∪ B) = 13/52 + 12/52 − 3/52 = 22/52 ≈ 0.423.

---

## 2.3 Conditional Probability and Independence

**Conditional probability** asks: "given that event B has already happened, what's the probability of event A?" written P(A | B):

P(A | B) = P(A ∩ B) / P(B),   provided P(B) > 0

**Example:** In a class, 60% of students play sports (S), and 25% of students both play sports and are on the honor roll (S ∩ H). What's the probability a student is on the honor roll, *given* that they play sports? P(H | S) = P(H ∩ S) / P(S) = 0.25 / 0.60 ≈ 0.417.

**Independence:** two events A and B are **independent** if knowing one occurred tells you nothing about the other — formally, if P(A | B) = P(A). Equivalently, and more commonly used as the working definition:

A and B are independent ⟺ P(A ∩ B) = P(A) · P(B)

**Example — independent events:** Flipping a coin and rolling a die are independent — the coin result has zero bearing on the die result. P(heads AND rolling a 4) = P(heads) × P(rolling a 4) = 0.5 × 1/6 = 1/12.

**Example — dependent events:** Drawing two cards from a deck *without replacement* are dependent — drawing a heart first changes the composition of the remaining deck, and thus the probability of drawing a heart second. P(both hearts) = (13/52) × (12/51), *not* (13/52)².

**Bayes' Theorem** flips a conditional probability around — extremely useful when P(A | B) is hard to measure directly but P(B | A) is known:

P(A | B) = [P(B | A) · P(A)] / P(B)

**Example — a classic medical testing scenario:** A disease affects 1% of a population. A test correctly identifies sick patients 99% of the time (true positive rate) and correctly identifies healthy patients 95% of the time (so 5% false positive rate). If someone tests positive, what's the actual probability they have the disease?

Let D = "has disease," + = "tests positive." We know P(D) = 0.01, P(+|D) = 0.99, and P(+|Dᶜ) = 0.05.

First find P(+) using the law of total probability: P(+) = P(+|D)P(D) + P(+|Dᶜ)P(Dᶜ) = (0.99)(0.01) + (0.05)(0.99) = 0.0099 + 0.0495 = 0.0594.

Then apply Bayes' Theorem: P(D | +) = (0.99 × 0.01) / 0.0594 = 0.0099 / 0.0594 ≈ **0.167**, or about 16.7%.

This result surprises most people — even with a 99%-accurate test, a positive result only means about a 1-in-6 chance of actually having the disease, because the disease is so rare that false positives from the large healthy population outnumber true positives from the small sick population. This exact reasoning pattern — accounting for how rare something is *before* trusting a positive signal — underlies spam filters, medical screening policy, and fraud detection systems.

---

[Previous](./[1]-Descriptive-Statistics.md) | [Table of Contents](./[0]-Introduction-to-Statistics.md) | [Next](./[3]-Random-Variables-And-Expected-Value.md)
