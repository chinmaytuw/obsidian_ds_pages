---
tags:
  - AB_testing
Source: https://claude.ai/share/a397126a-a680-4b5b-91a2-f479e8f49f29
---
# Prompt
What test to perform if there are whales in my #AB_testing  variants? A regular t test might not be the best option, right?

# Reply

You're absolutely right to be cautious! When you have whales (high-value users with extreme values) in your A/B test, a regular #t_test  can be problematic because:

## **Why t-tests struggle with whales:**

- They're sensitive to outliers and extreme values
- They assume roughly normal distributions, which whale behavior violates
- A few whales can dominate the results and lead to misleading conclusions
- High variance from whales reduces statistical power

## **Better approaches:**

**1. Mann-Whitney U test (Wilcoxon rank-sum test)** This non-parametric test works on ranks rather than actual values, making it much more robust to outliers. It tests whether one group tends to have higher values than another without assuming normality.

**2. Quantile-based tests** Instead of comparing means, compare medians or other percentiles. You could use bootstrap methods to test if median revenue differs between variants.

**3. Trim or winsorize the data**

- Trimming: Remove the top X% of values from both groups
- Winsorizing: Cap extreme values at a certain percentile (e.g., 95th or 99th)
- Then run your t-test on the modified data

**4. Stratified analysis** Segment users into whales vs. non-whales and analyze separately. This gives you clearer insights: "Among regular users, variant B wins by 5%; among whales, variant A wins by 20%."

**5. Log transformation + t-test** If your metric is revenue-like, log-transform it first (handles zeros with log(1+x)). This can normalize the distribution enough for a t-test to be appropriate.

**6. Bootstrap methods** Resample your data many times to build a distribution of the difference in means (or medians) between groups. This doesn't assume normality and handles outliers better.

**My recommendation:** Start with the Mann-Whitney U test for a robust hypothesis test, then complement it with stratified analysis to understand how whales vs. regular users respond differently. This gives you both statistical rigor and actionable business insights.

What metric are you optimizing for in your test?



# Mann-Whitney U test
I'll walk you through the [[Mann-Whitney U test]] with a concrete example.

## The Scenario

You're testing two checkout flows (A and B) and measuring revenue per user over 7 days. You have whales making the data skewed:

**Variant A (Control):** $5, $12, $8, $150, $6, $11, $9 **Variant B (Treatment):** $15, $18, $22, $200, $14, $20, $16

Notice both groups have one whale ($150 and $200).

## How Mann-Whitney U Works

Instead of comparing actual dollar amounts, we **rank all values together** from lowest to highest, then see if one group tends to have higher ranks.

### Step 1: Combine and rank all values

|Value|$5|$6|$8|$9|$11|$12|$14|$15|$16|$18|$20|$22|$150|$200|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|**Rank**|1|2|3|4|5|6|7|8|9|10|11|12|13|14|
|**Group**|A|A|A|A|A|A|B|B|B|B|B|B|A|B|

### Step 2: Sum the ranks for each group

**Variant A ranks:** 1 + 2 + 3 + 4 + 5 + 6 + 13 = **34** **Variant B ranks:** 7 + 8 + 9 + 10 + 11 + 12 + 14 = **71**

### Step 3: Calculate the U statistic

For group A (n₁ = 7):

- U₁ = R₁ - [n₁(n₁+1)/2]
- U₁ = 34 - [7×8/2] = 34 - 28 = **6**

For group B (n₂ = 7):

- U₂ = R₂ - [n₂(n₂+1)/2]
- U₂ = 71 - [7×8/2] = 71 - 28 = **43**

We use the smaller U value: **U = 6**

### Step 4: Interpret

The U statistic represents "how many times did a value from group A come before a value from group B?" A small U means group A tends to be lower.

With n₁=7, n₂=7, and U=6, we'd compare this to critical values (or use software to get a p-value). In this case, the p-value would be small (around 0.01), suggesting variant B genuinely performs better.

## Why This Handles Whales Better

Notice what happened to our whales:

- The $150 whale in A got rank 13
- The $200 whale in B got rank 14

The difference between them is just **1 rank**, not $50! This prevents extreme values from dominating the test. Whether that whale spent $200 or $2,000, it would still just be the highest rank.

## The Key Insight

Mann-Whitney asks: "If I randomly pick one user from A and one from B, what's the probability the B user spent more?" It's testing the overall tendency of one group to outperform, not whether their means differ.

This makes it perfect for whale-heavy data where you care about typical user behavior, not just arithmetic averages inflated by a few big spenders.