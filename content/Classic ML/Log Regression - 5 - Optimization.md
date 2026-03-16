---
tags:
  - classic_ml
  - classification
  - logistic_regression
  - gradient_descent
  - optimization
---


Continued from [[Log Regression - 4 -  Binary Cross-Entropy]]
# Module 5 — Optimization: Gradient Descent on Cross-Entropy Loss

## 5.1 Why There's No Closed-Form Solution

- In linear regression, we could solve for weights directly:
$$\hat{w} = (X^T X)^{-1} X^T y$$
- This works because the MSE loss is a perfect bowl (quadratic, convex) with an algebraically solvable bottom.
- Binary cross-entropy is *also* convex — there's one global minimum — but it has **no closed-form solution**. The sigmoid function wrapped around the linear term makes the algebra intractable. We talk about this in the [[Maximum Likelihood Estimation]] note as well.
- So we have to use iterative optimization. The tool: **gradient descent**. #gradient_descent

---

## 5.2 Gradient Descent: The Core Idea

- We want to minimize the loss $\mathcal{L}(w)$ over our weights $w$.
- Gradient descent does this by repeatedly updating weights in the direction *opposite* to the gradient:
$$w \leftarrow w - \eta \cdot \nabla_w \mathcal{L}$$
	Where:
	- $\eta$ (eta) is the **learning rate** — how big each step is
	- $\nabla_w \mathcal{L}$ is the **gradient** — the direction of steepest *ascent*
	- We subtract it because we want to go *downhill*

---
## 5.3 Deriving the Gradient — Step by Step

- Recall from Module 4 that our loss for $n$ training examples is:
$$\mathcal{L}(w) = -\frac{1}{n} \sum_{i=1}^{n} \left[ y_i \log(\hat{p}_i) + (1 - y_i) \log(1 - \hat{p}_i) \right]$$
	Where $\hat{p}_i = \sigma(w^T x_i)$ and $\sigma(z) = \frac{1}{1 + e^{-z}}$.

### Step 1: The sigmoid derivative

- A key fact we'll need:
$$\frac{d\sigma}{dz} = \sigma(z)(1 - \sigma(z))$$
- *Proof sketch:* Let $\sigma = \frac{1}{1+e^{-z}}$. Then:
$$\frac{d\sigma}{dz} = \frac{e^{-z}}{(1+e^{-z})^2} = \frac{1}{1+e^{-z}} \cdot \frac{e^{-z}}{1+e^{-z}} = \sigma \cdot (1 - \sigma)$$
- This is a beautiful self-referential derivative — the sigmoid knows how to differentiate itself.

### Step 2: Gradient of the loss w.r.t. a single weight $w_j$

- For a single example, the loss is:
$$\ell_i = -\left[ y_i \log(\hat{p}_i) + (1 - y_i) \log(1 - \hat{p}_i) \right]$$
- Using the chain rule:
$$\frac{\partial \ell_i}{\partial w_j} = \frac{\partial \ell_i}{\partial \hat{p}_i} \cdot \frac{\partial \hat{p}_i}{\partial z_i} \cdot \frac{\partial z_i}{\partial w_j}$$
- Let's compute each piece:
**Piece 1:** 
$\frac{\partial \ell_i}{\partial \hat{p}_i}$ $= -\frac{y_i}{\hat{p}_i} + \frac{1 - y_i}{1 - \hat{p}_i}$
**Piece 2:** $\frac{\partial \hat{p}_i}{\partial z_i} = \hat{p}_i(1 - \hat{p}_i)$ (from Step 1)

**Piece 3:** $\frac{\partial z_i}{\partial w_j} = x_{ij}$ (since $z_i = w^T x_i$)

### Step 3: Multiply the pieces together

$$\frac{\partial \ell_i}{\partial w_j} = \left( -\frac{y_i}{\hat{p}_i} + \frac{1 - y_i}{1 - \hat{p}_i} \right) \cdot \hat{p}_i(1 - \hat{p}_i) \cdot x_{ij}$$

- Expand the bracket:
$$= \left( -y_i(1 - \hat{p}_i) + (1 - y_i)\hat{p}_i \right) \cdot x_{ij}$$
$$= \left( -y_i + y_i \hat{p}_i + \hat{p}_i - y_i \hat{p}_i \right) \cdot x_{ij}$$
$$= \left( \hat{p}_i - y_i \right) \cdot x_{ij}$$

### Step 4: Average over all examples
$$\frac{\partial \mathcal{L}}{\partial w_j} = \frac{1}{n} \sum_{i=1}^{n} (\hat{p}_i - y_i) \cdot x_{ij}$$

In vector form — the full gradient for all weights at once:
$$\boxed{\nabla_w \mathcal{L} = \frac{1}{n} X^T (\hat{p} - y)}$$

---

## 5.4 The Beautiful Result

- Pause and look at what we got:
$$\nabla_w \mathcal{L} = \frac{1}{n} X^T (\hat{p} - y)$$
- This says: **the gradient is just the average residual** (prediction minus truth), weighted by the input features.
- <mark style="background: #FFB86CA6;">This is structurally identical to the gradient of MSE loss in linear regression</mark>. The sigmoid, the log — all of it cancels out into this clean expression. The chain rule pieces conspire perfectly because the cross-entropy loss was specifically designed to pair with the sigmoid. <mark style="background: #ADCCFFA6;">This is not a coincidence — MLE on a Bernoulli likelihood produces exactly this pairing.</mark>

> **Intuition:** The gradient "blames" each weight proportionally to how much the feature $x_j$ contributed to the error $(\hat{p} - y)$. Weights connected to uninformative features get small updates. Weights connected to predictive features get large updates.

---

## 5.5 The Weight Update Rule
- Putting it all together, the gradient descent update at each step is:
$$w \leftarrow w - \eta \cdot \frac{1}{n} X^T (\hat{p} - y)$$
- One full pass through all training data = one **epoch**. Repeat until convergence.
---
## 5.6 Variants of Gradient Descent

| Variant                 | Data used per update        | Pros                                  | Cons                    |
| ----------------------- | --------------------------- | ------------------------------------- | ----------------------- |
| **Batch GD**            | All $n$ examples            | Stable, true gradient                 | Slow for large $n$      |
| **Stochastic GD (SGD)** | 1 example                   | Fast updates, can escape local minima | Noisy, high variance    |
| **Mini-batch GD**       | $k$ examples (e.g. 32, 128) | Balances speed + stability            | Needs batch size tuning |

In practice, **mini-batch is the default** in most ML frameworks.

---

## 5.7 The Learning Rate $\eta$: The Critical Hyperparameter

- **Too large:** You overshoot the minimum, loss bounces or diverges
- **Too small:** Convergence is painfully slow
- **Just right:** Loss decreases smoothly toward the minimum

Modern practice uses adaptive learning rate schedulers (Adam, RMSProp) that adjust $\eta$ per-weight automatically. But gradient descent with fixed $\eta$ is the conceptual foundation.

---
## 5.8 Convergence

- <mark style="background: #FFF3A3A6;">Because BCE loss is convex, gradient descent is guaranteed to find the global minimum</mark> (given small enough $\eta$ and enough iterations). There are no local minima to get stuck in — only *one* bowl.
- This is a meaningful guarantee. Neural networks don't have this property; logistic regression does.
---
## Player Churn Example

- Recall our setup: $\hat{p}_i$ = predicted churn probability for player $i$.
- If a player churned ($y_i = 1$) but we predicted $\hat{p}_i = 0.2$:
	- Error $= \hat{p}_i - y_i = 0.2 - 1 = -0.8$
	- The gradient pushes weights in the direction that *increases* $\hat{p}$ for players like this one
- If a player didn't churn ($y_i = 0$) but we predicted $\hat{p}_i = 0.9$:
	- Error $= 0.9 - 0$
	- The gradient pushes weights down, reducing $\hat{p}$ for players like this one
- The model self-corrects, example by example (or batch by batch).