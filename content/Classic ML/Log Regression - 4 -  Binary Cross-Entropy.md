---
tags:
  - classic_ml
  - classification
  - cross_entropy
  - loss_function
  - log_likelihood
---

Continued from [[Log Regression - 3 - Linearity in log odds space]]
# 4. The Loss Function: Binary Cross-Entropy

## 4.1 The Layman Intuition

- We have a model that outputs a probability. Now we need a way to score how good or bad those probability predictions are — a **loss function**.
- Your first instinct might be: _just use squared error, like linear regression._ We'll show why that's a bad idea. Then we'll derive the right loss function from a very natural question:
> **Given that we observed the data we did — what weights $\mathbf{w}$ make that data most plausible?**

- This is the [[Maximum Likelihood Estimation]] principle, and it leads us directly to Binary Cross-Entropy — not as an arbitrary choice, but as the mathematically correct scoring rule for a probabilistic classifier.

---

## 4.2 Why Not Squared Error?

- With linear regression, squared error made sense because we were predicting continuous values. For a binary classifier, it creates two problems. 

**Problem 1 — Non-convex loss surface**
- When you plug the sigmoid into a squared error loss and plot the surface over weight space, you get a bumpy, non-convex landscape with many local minima. Gradient descent can get stuck. With the right loss function (as we'll derive), the surface is perfectly bowl-shaped — one global minimum, guaranteed.

**Problem 2 — Mismatched punishment**
- Squared error penalizes wrong predictions, but doesn't penalize _confident wrong predictions_ severely enough. 
	- If your model outputs $\hat{p} = 0.999$ for a player who didn't churn — that's a catastrophically wrong confident prediction. Squared error gives this a loss of $(0 - 0.999)^2 \approx 0.98$. 
	- For a player that had $\hat{p} = 0.6$, Squared loss will be $(0 - 0.6)^2 \approx 0.16$. 
	- So yes, losses do differ but there's a ceiling of 1 as max loss.
	- <mark style="background: #FFB86CA6;">We need a loss that blows up when the model is confidently wrong</mark>.

---

## 4.3 Building the Loss from MLE

### 4.3.1 Step 1 — Model Each Observation as a Bernoulli Trial

- For a single player $i$, the model says:
$$P(y_i = 1 \mid \mathbf{x}_i) = \hat{p}_i = \sigma(\mathbf{w}^T \mathbf{x}_i)$$
$$P(y_i = 0 \mid \mathbf{x}_i) = 1 - \hat{p}_i$$
- We can write both cases in one compact expression:
$$P(y_i \mid \mathbf{x}_i) = \hat{p}_i^{y_i}(1-\hat{p}_i)^{1-y_i}$$
- Check this works:
	- When $y_i = 1$: gives $\hat{p}_i^1 \cdot (1-\hat{p}_i)^0 = \hat{p}_i$ ✅
	- When $y_i = 0$: gives $\hat{p}_i^0 \cdot (1-\hat{p}_i)^1 = 1 - \hat{p}_i$ ✅

### 4.3.2 Step 2 — Write the Likelihood Over All $N$ Players

- Assuming players are independent, the probability of observing the entire dataset is the product of individual probabilities — this is the **likelihood**:
$$\mathcal{L}(\mathbf{w}) = \prod_{i=1}^{N} \hat{p}_i^{y_i}(1-\hat{p}_i)^{1-y_i}$$
- We want to find $\mathbf{w}$ that maximizes this.

### 4.3.3 Step 3 — Log-Likelihood (Products become Sums)
- Products are numerically horrible — multiplying thousands of small probabilities together causes floating point underflow. Take the natural log. Since $\log$ is monotonically increasing, maximizing $\log \mathcal{L}$ is equivalent to maximizing $\mathcal{L}$:
$$\log \mathcal{L}(\mathbf{w}) = \sum_{i=1}^{N} \left[ y_i \log \hat{p}_i + (1 - y_i) \log(1 - \hat{p}_i) \right]$$

### 4.3.4 Step 4 — Flip to a Loss (Minimization)
- By convention, optimizers **minimize** loss. So we negate the log-likelihood and average over $N$:
$$\boxed{\mathcal{J}(\mathbf{w}) = -\frac{1}{N}\sum_{i=1}^{N} \left[ y_i \log \hat{p}_i + (1 - y_i) \log(1 - \hat{p}_i) \right]}$$
- This is **Binary Cross-Entropy (BCE)** — also called **log loss**. Every term comes directly from MLE. Nothing was assumed about the loss function's form — it was derived.

> [!Key Intuition]
> The Shift in Thinking
> - With squared error, you first compute the error $(y - \hat{p})$ and then penalize it. 
> - Log loss works differently. It doesn't compute an error first and then transform it. It asks a completely different question:
> 		"How surprised should I be by the true label, given what the model predicted?"
> - This comes from information theory / probability. If the model says $hat{p} = 0.001$ for churn, it's essentially saying *"churn is nearly impossible."* Then the player churns. You should be **enormously surprised**. The loss directly measures that surprise.

### 4.3.4 Side note: Log
- Here's the graph for $ln(x)$ and $-ln(x)$. Notice how for lower values of x, the loss becomes really large! 
![[ln_neg_ln.png]]
- Another visualization
- ![[ln_table.png]]

>[!Note]
>Notice how for y=1, we take p and for y=0, we consider (1-p).
>This is not like MSE where always take (pred-actual) as residuals
>You can rethink it as:
> > for y =1 -> abs(0-p) or the distance from being wrong
> > for y =0 -> abs(1-p) ,also a distance from being wrong. Although abs doesn't necessarily make sense here as p is never greater than 1 


---

## Anatomy of a Single Term

- For one player, the loss contribution is:
$$\ell_i = -\left[ y_i \log \hat{p}_i + (1 - y_i) \log(1 - \hat{p}_i) \right]$$
- Only **one term is ever active at a time**:
$$\ell_i = \begin{cases} -\log \hat{p}_i & \text{if } y_i = 1 \text{ (player churned)} \ -\log(1 - \hat{p}_i) & \text{if } y_i = 0 \text{ (player stayed)} \end{cases}$$
- Now look at the behavior of each case:
**Case 1 — Player churned ($y_i = 1$), loss $= -\log\hat{p}_i$:**

| $\hat{p}_i$ | $-\log\hat{p}_i$ | Verdict                                 |
| ----------- | ---------------- | --------------------------------------- |
| $0.99$      | $0.01$           | Correct and confident — tiny loss       |
| $0.70$      | $0.36$           | Correct but uncertain — moderate loss   |
| $0.50$      | $0.69$           | Completely uncertain — significant loss |
| $0.10$      | $2.30$           | Wrong — large loss                      |
| $0.01$      | $4.61$           | Confidently wrong — huge loss           |

**Case 2 — Player stayed ($y_i = 0$), loss $= -\log(1-\hat{p}_i)$:**

|$\hat{p}_i$|$-\log(1-\hat{p}_i)$|Verdict|
|---|---|---|
|$0.01$|$0.01$|Correct and confident — tiny loss|
|$0.50$|$0.69$|Completely uncertain — significant loss|
|$0.99$|$4.61$|Confidently wrong — huge loss|

- This is the behavior we wanted — **the loss approaches infinity as the model becomes confidently wrong**. Squared error never does this.

---

## Visualizing the Loss Curves

```
Loss
 ↑
 |\ ← -log(p̂) when y=1       . ← -log(1-p̂) when y=0
 | \                          .
 |  \                        .
 |   \                      .
 |    `.                  .'
 |      `-.          _.-'
 |          `------'
 |________________________________→ p̂
 0          0.5          1
```

- Both curves are **convex** — bowl shaped. This is important: it means the total loss surface will also be convex, guaranteeing gradient descent finds the global minimum.

---

## The Convexity Payoff
- This is why BCE is the right loss — not just intuitively but geometrically. With sigmoid output and BCE loss, the total loss $\mathcal{J}(\mathbf{w})$ as a function of weights is **strictly convex**. One bowl, one bottom, no traps.
- With squared error and sigmoid output — not convex. Local minima appear. Optimisation becomes unreliable.

---
## Derived vs Assumed — Keeping Score

- Following the principle from Module 3:

| Component                 | Status      | Justification                                            |
| ------------------------- | ----------- | -------------------------------------------------------- |
| Bernoulli likelihood      | **Assumed** | Binary outcomes modelled as independent Bernoulli trials |
| Log-likelihood derivation | **Derived** | Algebraic consequence of the likelihood                  |
| BCE as loss               | **Derived** | Negative log-likelihood — follows from MLE               |
| Convexity of BCE          | **Derived** | Provable mathematical property                           |

---

## Summary

|Concept|Key Takeaway|
|---|---|
|Why not squared error|Non-convex surface, insufficient penalty for confident errors|
|MLE principle|Find $\mathbf{w}$ that makes observed data most probable|
|Likelihood|$\prod_i \hat{p}_i^{y_i}(1-\hat{p}_i)^{1-y_i}$|
|BCE loss|$-\frac{1}{N}\sum_i \left[y_i \log \hat{p}_i + (1-y_i)\log(1-\hat{p}_i)\right]$|
|Key behaviour|Loss $\to \infty$ when model is confidently wrong|
|Convexity|BCE gives a convex loss surface — one global minimum|
