---
tags:
  - classification
  - classic_ml
  - logit
  - log_likelihood
  - logistic_regression
---

# Module 1 — The Problem Setup: Why Not Linear Regression?

## The Layman Intuition

Imagine you're trying to predict whether a Battlefield player will churn (quit the game) in the next 30 days. You have a feature — let's say **hours played last week** — and a binary outcome: **churned (1) or stayed (0)**.

Your instinct might be: _I already know linear regression, let me just fit a line through this data._ Let's see why that breaks down.

---

## The Experiment: Breaking Linear Regression

Suppose your data looks like this — players with low hours tend to churn, players with high hours tend to stay:

```
Churned (1) |       . . .
            |    . .
            | . .
Stayed  (0) |            . . .
            |         . .
            |      . .
            |________________________
                 Hours played
```

You fit a linear regression line: $\hat{y} = w_0 + w_1 x$

Seems okay in the middle — but notice two immediate problems.

---

## Problem 1 — The Output Leaves [0, 1]

A probability must live between 0 and 1. Linear regression has no such constraint. For a player who played 80 hours, your model might confidently output $\hat{y} = 1.7$. For someone who played 0 hours, it might output $\hat{y} = -0.3$.

**What does a probability of 1.7 mean? Nothing. It's nonsense.**

Mathematically, linear regression models:

$$\hat{y} = \mathbf{w}^T \mathbf{x}$$

where $\hat{y} \in (-\infty, +\infty)$, but we need $\hat{y} \in [0, 1]$.

---

## Problem 2 — The Relationship Isn't Linear

Think about it intuitively. Going from 0 to 1 hour played probably has a _big_ effect on churn probability — almost everyone who plays 0 hours is going to churn. But going from 40 to 41 hours probably barely moves the needle — you're already deeply engaged.

The effect of each additional hour is **not constant**. The relationship is inherently **S-shaped**, not linear. Linear regression can't model this.

---

## Problem 3 — Outliers Wreck the Decision Boundary

This one is subtle but important. Say you add a single extreme player — someone who played 200 hours. Linear regression will tilt the entire fitted line to accommodate that outlier. This shifts your decision threshold in ways that have nothing to do with the actual churn pattern in the bulk of your data.

A picture to hold in mind:

```
Without outlier:   -----/-----   (reasonable boundary)
With outlier:      ---------/--  (boundary shifts right, misclassifies churners)
```

---

## What We Actually Need

We need a model that:

1. **Outputs values strictly between 0 and 1** — so we can interpret them as probabilities
2. **Captures the S-shaped relationship** — big effect in the middle, tapering at the extremes
3. **Is robust to outliers** in the response variable
4. **Is still simple and interpretable** — ideally keeping linear regression's core structure underneath

The key insight that logistic regression exploits:

> **We don't model the probability directly. We model the _log-odds_ of the probability — and _that_ relationship with features is linear.**

We'll unpack log-odds fully in Module 3. But the bridge between raw scores and probabilities — the thing that enforces the $[0,1]$ constraint and creates the S-shape — is what we tackle next.

---

## Summary

|Problem with Linear Regression|What We Need Instead|
|---|---|
|Outputs can exceed [0,1]|Outputs bounded to [0,1]|
|Assumes constant marginal effect|S-shaped, diminishing returns|
|Sensitive to outliers in $y$|Robust probabilistic framing|

---

Ready for **Module 2 — The Sigmoid Function**? This is where it starts getting satisfying.