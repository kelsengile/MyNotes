[Previous](./[0]-Introduction-to-Statistics.md) | [Table of Contents](./[0]-Introduction-to-Statistics.md) | [Next](./[2]-Probability-Basics.md)

# Lesson 1 - Descriptive Statistics

Before you can reason about uncertainty or test a hypothesis, you need to be able to summarize a dataset clearly. Descriptive statistics answers the question "what does this data actually look like?" — where it's centered, how spread out it is, and what shape it takes.

---

## 1.1 Measures of Central Tendency (Mean, Median, Mode)

These three measures each try to answer "what's a typical value in this dataset?" — but they can disagree wildly depending on the shape of the data, so knowing when to use which matters.

**Mean (average):** sum all values and divide by how many there are.

mean = (x₁ + x₂ + ... + xₙ) / n

**Example:** For the dataset {2, 4, 4, 6, 9}, the mean = (2+4+4+6+9)/5 = 25/5 = 5.

**Median:** the middle value when the data is sorted. If there's an even number of values, average the two middle ones.

**Example:** For {2, 4, 4, 6, 9} (already sorted, n=5, odd), the median is the 3rd value: **4**.
For {2, 4, 6, 9} (n=4, even), the median is the average of the 2nd and 3rd values: (4+6)/2 = **5**.

**Mode:** the value(s) that appear most frequently. A dataset can have one mode, multiple modes, or none at all.

**Example:** For {2, 4, 4, 6, 9}, the mode is **4** (it appears twice, more than any other value).

**Why they disagree — the effect of outliers:** Consider salaries at a small company: {40k, 42k, 45k, 47k, 500k}. The mean is 134.8k — dragged way up by the one large outlier. The median is 45k — a far more representative "typical" salary. This is exactly why household income and home prices are usually reported as medians in the news, not means: a handful of extreme values can badly distort the mean, while the median stays anchored to where most of the data actually sits.

| Measure | Sensitive to outliers? | Best used when |
|---|---|---|
| Mean | Yes | Data is roughly symmetric, no extreme outliers |
| Median | No | Data is skewed or has outliers |
| Mode | No | Data is categorical, or you want the "most common" value |

---

## 1.2 Measures of Spread (Variance, Standard Deviation)

Central tendency alone can be misleading — two datasets can have the identical mean while looking completely different. {5, 5, 5, 5} and {1, 3, 7, 9} both have a mean of 5, but the second is far more spread out. **Measures of spread** quantify that difference.

**Range:** the simplest measure — max value minus min value. Easy to compute, but very sensitive to a single outlier.

**Variance:** the average of the squared differences between each value and the mean. Squaring ensures negative and positive deviations don't cancel out, and it penalizes large deviations more heavily.

Population variance: σ² = Σ(xᵢ − μ)² / n
Sample variance: s² = Σ(xᵢ − x̄)² / (n − 1)

Note the denominator difference: sample variance divides by `n − 1` instead of `n` (called **Bessel's correction**) to correct for the fact that a sample tends to slightly underestimate the true population spread — using `n − 1` gives a less biased estimate when you're working with a sample rather than the entire population.

**Standard deviation** is simply the square root of variance: σ = √σ². It's used more often than variance in practice because it's back in the *original units* of the data (variance is in squared units, which is hard to interpret — "squared dollars" means nothing intuitive).

**Worked example:** Find the sample variance and standard deviation of {2, 4, 4, 6, 9}. Mean = 5 (from section 1.1).

| xᵢ | xᵢ − x̄ | (xᵢ − x̄)² |
|---|---|---|
| 2 | −3 | 9 |
| 4 | −1 | 1 |
| 4 | −1 | 1 |
| 6 | 1 | 1 |
| 9 | 4 | 16 |

Sum of squared deviations = 9+1+1+1+16 = 28.
Sample variance: s² = 28 / (5−1) = 7.
Sample standard deviation: s = √7 ≈ 2.65.

---

## 1.3 Visualizing Data (Histograms, Box Plots)

Numbers alone are hard to build intuition from — visualizations reveal patterns, skew, and outliers at a glance.

**Histograms** group numeric data into ranges ("bins") and show how many values fall into each bin as a bar. They reveal the overall *shape* of a distribution:

- **Symmetric:** data mirrors around the center (like a classic bell curve).
- **Right-skewed (positively skewed):** a long tail stretches toward higher values (e.g. household income — most people cluster at moderate incomes, with a long tail of very high earners).
- **Left-skewed (negatively skewed):** a long tail stretches toward lower values (e.g. age at retirement — most people retire in a similar range, with a tail of people retiring quite young).

**Box plots (box-and-whisker plots)** summarize a dataset using five key numbers, collectively called the **five-number summary**:

| Component | Meaning |
|---|---|
| Minimum | smallest value (excluding extreme outliers) |
| Q1 (first quartile) | the value below which 25% of the data falls |
| Median (Q2) | the value below which 50% of the data falls |
| Q3 (third quartile) | the value below which 75% of the data falls |
| Maximum | largest value (excluding extreme outliers) |

The **Interquartile Range (IQR)** = Q3 − Q1 measures the spread of the "middle half" of the data, and is commonly used to flag outliers: any point more than 1.5 × IQR below Q1 or above Q3 is typically marked as an outlier and plotted separately on the box plot rather than included in the whiskers.

**Why box plots are useful for comparison:** placing several box plots side by side (e.g. test scores across 4 different classes) lets you compare medians, spread, and skew across groups instantly — something that's much harder to do by comparing raw histograms or lists of numbers.

---

[Previous](./[0]-Introduction-to-Statistics.md) | [Table of Contents](./[0]-Introduction-to-Statistics.md) | [Next](./[2]-Probability-Basics.md)
