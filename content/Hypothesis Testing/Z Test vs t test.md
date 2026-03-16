---
tags:
  - z_test
  - t_test
  - hypothesis_testing
  - AB_testing
---
related to [[3. Hypothesis Testing - Part III]]

The **T-test** is the Z-test's more cautious sibling. While they look similar, the T-test is used when you are dealing with **means** (rather than proportions) and have more uncertainty—specifically regarding the population's standard deviation.

---

## Z-test vs. T-test: The Core Differences

The main difference lies in **what you know** about the population and **how much data** you have.

| **Feature**             | **Z-test**                                 | **T-test**                               |
| ----------------------- | ------------------------------------------ | ---------------------------------------- |
| **Population Variance** | Known                                      | Unknown (Estimated from sample)          |
| **Sample Size**         | Large (typically $n > 30$)                 | Small ($n < 30$)                         |
| **Distribution Shape**  | Normal Distribution                        | T-Distribution (heavier tails)           |
| **Data Type**           | Proportions (like your 5% vs 6%)<br><br>+1 | Continuous Means (e.g., average revenue) |

---

## When to use the T-test

You should reach for a T-test in these specific scenarios:

### 1. Small Sample Sizes

The T-distribution is designed to be "conservative." It has "thicker tails" than a normal distribution, meaning it expects more extreme values by chance when you have very few data points. As your sample size $n$ increases, the T-distribution eventually turns into the Normal (Z) distribution.

### 2. Unknown Population Standard Deviation

In the real world, we rarely know the true standard deviation of the entire population.

- **Z-test:** Assumes you know the exact population variance or have enough data to be "certain" about your estimate.
- **T-test:** Acknowledges that you are _estimating_ the variance from your sample, adding an extra layer of uncertainty to the calculation.

### 3. Comparing Means (Not Proportions)

While your notes focus on conversion rates (proportions), T-tests are the gold standard for comparing **average values**.

- **Example:** Is the _average amount spent_ per player higher in the ML model than in the Heuristic model? Since "amount spent" is a continuous number (e.g., $5.50), you would use a T-test.

---

## How it Changes the Calculation

The process flow in your notes remains very similar , but with two key adjustments:

1. **Standard Error:** Instead of using the pooled proportion formula, you use the sample standard deviation ($s$).
2. **Degrees of Freedom ($df$):** To look up the p-value, you need $df$ (usually $n - 1$). This accounts for the sample size; the smaller the $df$, the higher the "bar" for statistical significance.

**Summary Rule of Thumb:** <mark style="background: #FFB86CA6;">If you are comparing proportions with millions of users like in your ML scenario, stick with the Z-test. If you are comparing the average session time of 20 players, switch to the T-test.</mark>


Would you like me to show you how the Standard Error formula would change if you were testing the **average revenue per user (ARPU)** instead of conversion rates?