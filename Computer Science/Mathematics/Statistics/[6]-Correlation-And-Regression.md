[Previous](./[5]-Inferential-Statistics.md) | [Table of Contents](./[0]-Introduction-to-Statistics.md) | [Next](./[7]-Statistics-In-Computer-Science.md)

# Lesson 6 - Correlation And Regression

So far, this topic has focused on a single variable at a time. This lesson looks at the **relationship between two variables** — whether they move together, and how to build a model that predicts one from the other.

---

## 6.1 Correlation

**Correlation** measures the strength and direction of a *linear* relationship between two numeric variables. The most common measure is the **Pearson correlation coefficient**, denoted `r`, which always falls between −1 and 1.

| Value of r | Meaning |
|---|---|
| r = 1 | perfect positive linear relationship (as one increases, the other increases proportionally) |
| r = −1 | perfect negative linear relationship (as one increases, the other decreases proportionally) |
| r = 0 | no linear relationship |
| 0 < r < 1 | positive relationship, strength increasing as r approaches 1 |
| −1 < r < 0 | negative relationship, strength increasing as r approaches −1 |

**Example:** Hours studied and exam score across a class of students might show r ≈ 0.8 — a strong positive correlation: more hours studied tends to go with higher scores, though not perfectly (individual variation exists).

**Formula:**

r = Σ[(xᵢ − x̄)(yᵢ − ȳ)] / [√Σ(xᵢ − x̄)² · √Σ(yᵢ − ȳ)²]

You'll rarely compute this by hand for real data, but it's worth recognizing the structure: the numerator captures how x and y vary *together* (both above their means, or both below, contributes positively), while the denominator normalizes by each variable's individual spread — this is exactly what keeps `r` bounded between −1 and 1 regardless of the original units.

**The critical warning: correlation does not imply causation.** Two variables can be strongly correlated without one causing the other. Classic example: ice cream sales and drowning incidents are positively correlated — but ice cream doesn't cause drownings. Both are driven by a hidden third factor (a **confounding variable**): hot summer weather increases both ice cream sales and swimming (and therefore drowning risk). Correlation only tells you two things move together; it says nothing about *why*.

**Correlation also only captures *linear* relationships:** two variables can have a strong, perfectly predictable *nonlinear* relationship (like y = x²) while still showing a Pearson correlation near 0, because the relationship isn't a straight line. Always look at a scatter plot, not just the r value, before concluding "no relationship."

---

## 6.2 Linear Regression

While correlation measures *how strongly* two variables relate, **linear regression** goes a step further and builds an actual equation to predict one variable from the other.

**Simple linear regression** fits a straight line of the form:

ŷ = b₀ + b₁x

where:
- `ŷ` (y-hat) is the *predicted* value of y for a given x (distinguished from the actual observed y).
- `b₀` is the **intercept** — the predicted value of y when x = 0.
- `b₁` is the **slope** — how much ŷ changes for each one-unit increase in x.

**How the line is chosen — Least Squares:** among all possible straight lines, regression picks the one that minimizes the sum of squared **residuals** (the vertical distance between each actual data point and the line): Σ(yᵢ − ŷᵢ)². Squaring, just like in variance, ensures positive and negative errors don't cancel out and penalizes large misses more heavily than small ones.

**Example:** Suppose a regression of exam score (y) on hours studied (x) produces the equation:

ŷ = 50 + 5x

This means: a student who studies 0 hours is predicted to score 50 (the intercept), and each additional hour of studying is associated with a predicted 5-point increase in score (the slope). A student who studies 6 hours would have a predicted score of ŷ = 50 + 5(6) = 80.

**R² (coefficient of determination):** measures how well the regression line explains the variation in y, ranging from 0 to 1. For simple linear regression, R² is simply the square of the correlation coefficient: R² = r².

**Example:** If r = 0.8 between hours studied and exam score, then R² = 0.64 — meaning 64% of the variation in exam scores can be explained by variation in hours studied, leaving the remaining 36% attributable to other factors (natural ability, sleep, test anxiety, etc.) not captured by this simple model.

**A key caution — extrapolation:** a regression line is only reliable *within* the range of x-values actually observed in the data. Predicting ŷ for an x far outside that range (e.g. "studying 100 hours") assumes the same linear relationship continues to hold indefinitely, which is often unrealistic — real relationships frequently curve, plateau, or break down entirely outside the observed range.

---

[Previous](./[5]-Inferential-Statistics.md) | [Table of Contents](./[0]-Introduction-to-Statistics.md) | [Next](./[7]-Statistics-In-Computer-Science.md)
