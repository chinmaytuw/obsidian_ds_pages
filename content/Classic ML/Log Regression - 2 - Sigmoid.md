---
tags:
  - logistic_regression
  - classic_ml
  - classification
---

continued from [[Log Regression - 1 - Setup]]
# Module 2 — The Sigmoid Function: The Bridge to Probability

## The Layman Intuition

We need a function that takes any real number — from $-\infty$ to $+\infty$ — and squashes it into the range $(0, 1)$.

Think of it like a **volume knob that has been replaced by a soft limiter**. You can turn it as far as you want in either direction, but the output never actually reaches full blast or complete silence — it just asymptotically approaches those extremes.

That function is the **sigmoid**.

---

## Introducing the Sigmoid

$$\sigma(z) = \frac{1}{1 + e^{-z}}$$

where $z$ is any real number — we'll call it the **raw score** or #logit for now.

Let's just get a feel for what this does at key values:

|$z$|$e^{-z}$|$\sigma(z)$|Interpretation|
|---|---|---|---|
|$-\infty$|$\infty$|$\approx 0$|Certainly not churn|
|$-2$|$7.39$|$0.12$|Unlikely churn|
|$0$|$1$|$0.50$|Completely uncertain|
|$2$|$0.14$|$0.88$|Likely churn|
|$+\infty$|$0$|$\approx 1$|Certainly churn|

**At $z = 0$, the #sigmoid outputs exactly $0.5$ — maximum uncertainty.** Positive $z$ pushes toward 1, negative $z$ pushes toward 0. The further you go in either direction, the more confident the model becomes — but it never reaches absolute certainty.

---

## The Shape — Why It's S-Curved

The sigmoid produces the S-curve (sigmoid literally means S-shaped in Greek) we said we needed:

```
σ(z)
 1  |          _____________
    |        /
0.5 |-------/---------------
    |      /
 0  |_____/
         z=0
```

Notice three regions:

- **Left tail** ($z \ll 0$): curve is nearly flat near 0 — model is confidently predicting class 0
- **Middle** (around $z = 0$): curve is steep — small changes in $z$ produce big changes in probability
- **Right tail** ($z \gg 0$): curve is nearly flat near 1 — model is confidently predicting class 1

This directly addresses Problem 2 from Module 1. The effect of increasing your raw score $z$ is **not constant** — it matters most when you're near the decision boundary.

---

## Key Properties (Worth Knowing)

**Property 1 — Bounded output:**

$$0 < \sigma(z) < 1 \quad \forall z \in \mathbb{R}$$

Never actually reaches 0 or 1 — always strictly between. This is what makes it a valid probability.


**Property 2 — Symmetry:**

$$\sigma(-z) = 1 - \sigma(z)$$

If the model gives a 30% probability of churn, it's implicitly giving a 70% probability of not churning. Clean and consistent.

_Proof:_

$$\sigma(-z) = \frac{1}{1+e^{z}} = \frac{e^{-z}}{e^{-z}+1} \cdot \frac{e^z}{e^z} = 1 - \frac{1}{1+e^{-z}} = 1 - \sigma(z) \quad \checkmark$$


**Property 3 — Clean derivative:**

$$\frac{d\sigma}{dz} = \sigma(z)\left(1 - \sigma(z)\right)$$

This is one of those beautiful results in ML math. The derivative of the sigmoid is expressible entirely in terms of the sigmoid itself. This makes gradient computation during training very efficient. We'll use this in Module 5.

_Proof:_

Let $\sigma = (1 + e^{-z})^{-1}$. By the chain rule:

$$\frac{d\sigma}{dz} = -(1+e^{-z})^{-2} \cdot (-e^{-z}) = \frac{e^{-z}}{(1+e^{-z})^2}$$

Now rewrite:

$$= \frac{1}{1+e^{-z}} \cdot \frac{e^{-z}}{1+e^{-z}} = \sigma(z) \cdot (1 - \sigma(z)) \quad \checkmark$$

**Property 4 — Maximum slope at $z=0$:**

$$\left.\frac{d\sigma}{dz}\right|_{z=0} = \sigma(0)(1-\sigma(0)) = 0.5 \times 0.5 = 0.25$$

The sigmoid changes fastest right at the decision boundary, and flattens out as you move away. This matches our intuition about uncertainty.

---

## Where Does $z$ Come From?

We've been treating $z$ as an abstract raw score. In logistic regression, $z$ is just the familiar linear combination of your features:

$$z = w_0 + w_1 x_1 + w_2 x_2 + \cdots + w_n x_n = \mathbf{w}^T \mathbf{x}$$

So the full model is:

$$\hat{p} = \sigma(\mathbf{w}^T \mathbf{x}) = \frac{1}{1 + e^{-\mathbf{w}^T \mathbf{x}}}$$

**The linear regression lives inside the sigmoid.** We haven't abandoned linearity — we've just wrapped it in a function that enforces the probability constraint. This is the central architectural idea.

---

## Intuition Check

Before moving on, make sure these feel right:

- A player with $\mathbf{w}^T \mathbf{x} = 0$ is on the fence — model outputs $0.5$
- A player with a large positive score is confidently predicted to churn
- A player with a large negative score is confidently predicted to stay
- The weights $\mathbf{w}$ control _how_ features push the score up or down — same as linear regression

---

## Summary

|Concept|Key Takeaway|
|---|---|
|Sigmoid formula|$\sigma(z) = \frac{1}{1+e^{-z}}$|
|Output range|Strictly $(0, 1)$ — valid probabilities|
|Shape|S-curve — steep near 0, flat at extremes|
|Derivative|$\sigma(z)(1-\sigma(z))$ — elegant and efficient|
|Role in logistic regression|Wraps the linear score to produce probabilities|

---
