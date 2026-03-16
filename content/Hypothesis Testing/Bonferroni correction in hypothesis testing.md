---
Source: https://claude.ai/share/89faa9dc-2f5b-4fb0-a6ee-d51fb27070da
tags:
  - AB_testing
---
# Overview
The Bonferroni correction is a statistical adjustment used when you're conducting multiple hypothesis tests ( #AB_testing ) simultaneously. It helps control the problem of increased false positives ( #Type_1_error ) that naturally occurs when you run many tests at once.

## The core problem it addresses:**

When you perform a single hypothesis test at a #significance_level of α = 0.05, you accept a 5% chance of a false positive ( #Type_1_error ). But if you run 20 independent tests at this level, you'd expect about one false positive just by chance. The more tests you run, the higher your overall chance of finding at least one "significant" result that's actually just noise.

## How it works:**

The Bonferroni correction is simple: divide your desired significance level by the number of tests you're performing.

If you want an overall α = 0.05 and you're running _n_ tests, use α/n as the threshold for each individual test.

For example, with 10 tests: 0.05/10 = 0.005, so you'd only consider results with p < 0.005 as significant.

## Strengths and limitations:**

<span class='orange'>The main advantage is its simplicity and that it provides strong control over false positives across your entire set of tests (controlling the "family-wise error rate").</span>

<span class='orange'>The downside is that it's quite conservative, especially with many tests.</span> This increases your chance of false negatives (missing real effects). For instance, with 100 tests, you'd need p < 0.0005 to declare significance, which is a very stringent threshold.

Because of this conservativeness, researchers sometimes use less stringent alternatives like the Benjamini-Hochberg procedure, which controls the false discovery rate instead.

## Alternatives
There are actually several alternatives to Bonferroni! The best choice depends on your specific situation and what you're trying to control.

## **Main alternatives:**

**Holm-Bonferroni (or Holm's method)** - A step-down procedure that's less conservative than Bonferroni while still controlling the family-wise error rate. You order your p-values from smallest to largest and use different thresholds for each. It's strictly more powerful than Bonferroni with no real downside.

**Benjamini-Hochberg** - As I mentioned, this controls the false discovery rate (FDR) rather than family-wise error rate. It asks: "Of the results I call significant, what proportion can be false positives?" This is less stringent than controlling the probability of _any_ false positive.

**Šidák correction** - Similar to Bonferroni but slightly less conservative. Uses α_new = 1 - (1 - α)^(1/n). Works best when tests are independent.

**Hochberg's procedure** - A step-up version that's more powerful than Holm when certain assumptions hold.

**Tukey's HSD (Honest Significant Difference)** - Specifically designed for pairwise comparisons after ANOVA.

**Dunnett's test** - Used when you're comparing multiple treatments to a single control group (rather than all possible pairs).

**Permutation-based methods** - Estimate the null distribution empirically through resampling, which can be more powerful when you have the computational resources.

**Which to choose?**

It depends on whether you want strict control over any false positives (family-wise error rate → Bonferroni, Holm) or are willing to accept some false positives among your discoveries (false discovery rate → Benjamini-Hochberg). Exploratory research often uses FDR control, while confirmatory research tends toward stricter family-wise error control.