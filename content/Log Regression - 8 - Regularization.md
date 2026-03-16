---
tags:
  - classic_ml
  - classification
  - logistic_regression
---
Continued from [[Log Regression - 7 - Eval]]

# Module 8 — Regularization

## 8.1 Why Models Overfit
- With enough features (or engineered polynomial features from Module 6), logistic regression can memorize training data perfectly — achieving near-zero training loss while failing badly on new data.

Signs of overfitting:
- Training loss much lower than validation loss
- Extreme weight values (very large $|w_j|$)
- Model is sensitive to small changes in input

<mark style="background: #FFF3A3A6;">The fix: add a penalty term to the loss function that discourages large weights</mark>.

---

## 8.2 The Regularized Loss Function
$$\mathcal{L}_{\text{reg}}(w) = \mathcal{L}(w) + \lambda \cdot \Omega(w)$$
Where:
- $\mathcal{L}(w)$ is the original cross-entropy loss
- $\Omega(w)$ is the **regularization term** (penalty for complexity)
- $\lambda > 0$ is the **regularization strength** (a hyperparameter you tune)

Large $\lambda$ → strong penalty → simpler model → risk of underfitting
Small $\lambda$ → weak penalty → closer to original model → risk of overfitting

---

## 8.3 L2 Regularization (Ridge)
$$\Omega(w) = \|w\|_2^2 = \sum_j w_j^2$$
Full regularized loss:
$$\mathcal{L}_{\text{L2}} = -\frac{1}{n}\sum_i \left[y_i \log \hat{p}_i + (1-y_i)\log(1-\hat{p}_i)\right] + \lambda \sum_j w_j^2$$

### What it does to the gradient

- Adding L2 appends a term to each weight update:
$$\nabla_{w_j} \mathcal{L}_{\text{L2}} = \frac{1}{n}\sum_i (\hat{p}_i - y_i)x_{ij} + 2\lambda w_j$$
- The update rule becomes:
$$w_j \leftarrow w_j - \eta \left(\frac{1}{n}\sum_i (\hat{p}_i - y_i)x_{ij} + 2\lambda w_j\right)$$
$$= w_j(1 - 2\eta\lambda) - \eta \cdot \frac{1}{n}\sum_i (\hat{p}_i - y_i)x_{ij}$$

The $(1 - 2\eta\lambda)$ <mark style="background: #FFF3A3A6;">factor shrinks</mark> $w_j$ <mark style="background: #FFF3A3A6;">toward zero at every step — hence L2 is also called weight decay</mark>.

### Geometric intuition

- L2 penalty defines a **sphere** in weight space (all points where $\sum w_j^2 = c$).
- The optimization finds the point where the loss contours first *touch* this sphere.
- Because a sphere has no corners, the solution almost never lands exactly at zero for any weight. L2 **shrinks all weights** but rarely eliminates any.
$$\text{L2 effect: } w_j \rightarrow \text{small but nonzero}$$

![[l2_reg_geometry.png|500]]
- You've almost certainly seen this equation before in a different context. In 2D, $x^2 + y^2 = r^2$ is a **circle**. In 3D, $x^2 + y^2 + z^2 = r^2$ is a **sphere**.
- The L2 constraint is exactly the same equation, just written in $p$ dimensions instead of 2 or 3. So:
	- 2 weights → constraint region is a **circle** in 2D weight space
	- 3 weights → constraint region is a **sphere** in 3D weight space  
	- $p$ weights → constraint region is a **hypersphere** in $p$-dimensional weight space
- We just call it a "sphere" colloquially regardless of dimensionality, because the geometry works the same way.
- <mark style="background: #FFB86CA6;">Notice how the weights can never be zero since it is a circle</mark>.

---
## 8.4 L1 Regularization (Lasso)
$$\Omega(w) = \|w\|_1 = \sum_j |w_j|$$
Full regularized loss:
$$\mathcal{L}_{\text{L1}} = -\frac{1}{n}\sum_i \left[y_i \log \hat{p}_i + (1-y_i)\log(1-\hat{p}_i)\right] + \lambda \sum_j |w_j|$$
### What it does to the gradient

- The derivative of $|w_j|$ is $\text{sign}(w_j)$ (undefined at 0, handled via subgradients):
$$\nabla_{w_j} \mathcal{L}_{\text{L1}} = \frac{1}{n}\sum_i (\hat{p}_i - y_i)x_{ij} + \lambda \cdot \text{sign}(w_j)$$
- <mark style="background: #ADCCFFA6;">The penalty is constant magnitude regardless of how large</mark> $w_j$ is. This has a fundamentally different character than L2.

### Geometric intuition

- L1 penalty defines a **diamond** (L1 ball) in weight space.
- The loss contours are most likely to first touch the diamond at a **corner** —and corners in $d$ dimensions occur where one or more weights are exactly zero.
$$\text{L1 effect: } w_j \rightarrow \text{exactly zero for unimportant features}$$
- L1 produces **sparse solutions**. It performs implicit feature selection — weights for irrelevant features get zeroed out entirely.
- L1 LASSO is a Diamond: The L1 constraint is on the sum of the _absolute values_ of the weights: $|w_1| + |w_2| \leq c$. Let's test a point, for example, if the budget $c=1.5$:
	- If $w_1=0$, then $|0| + |w_2| = 1.5$, so $w_2$ can be $1.5$ or $-1.5$. This gives us the top and bottom tips of the diamond.
	- If $w_2=0$, then $|w_1| + |0| = 1.5$, so $w_1$ can be $1.5$ or $-1.5$. This gives us the side tips of the diamond.
	- Connecting these four points results in the straight-edged, sharp-cornered diamond shape.
 ![[Pasted image 20260315181914.png|500]]
- Left panel (L2): The purple circle is smooth everywhere. The loss contours (green ellipses) expand outward from the unconstrained minimum and graze the circle at an arbitrary point — somewhere on the surface that has nothing geometrically special about it. Both w1​ and w2​ end up nonzero.
- Right panel (L1): The orange diamond has four corners, each sitting exactly on an axis. When the green ellipses expand toward the diamond, they're geometrically much more likely to snag on a corner — which corresponds to one weight being exactly zero. That's where sparsity comes from. It's a question of probability and dimension, not geometric impossibility that the contours don't meet at some random point other than edge. 
	- Think about what needs to happen for the contour to touch a flat edge instead of a corner. The loss contours are ellipses centered at w_MLE​. For the ellipse to graze a flat edge, it needs to arrive at that edge at **exactly the right angle** — tangent to the flat face. That's a very specific geometric coincidence.
	- Corners, by contrast, are "attractors." They stick out toward the unconstrained optimum, so an expanding ellipse tends to snag on them first.
	- The dashed arrows show the "path" from the unconstrained optimum toward the constraint boundary — wherever that path first touches the boundary is your regularized solution.
---

## 8.5 L1 vs L2: Side-by-Side

| Property | L2 (Ridge) | L1 (Lasso) |
|---|---|---|
| Penalty term | $\sum w_j^2$ | $\sum \|w_j\|$ |
| Effect on weights | Shrinks toward zero | Pushes to exactly zero |
| Sparsity | No | Yes |
| Feature selection | Implicit (soft) | Explicit (hard) |
| Geometry | Sphere (smooth) | Diamond (corners) |
| When to use | Many small effects | Few strong features |
| Interpretability | Lower | Higher |
| Correlated features | Spreads weight across them | Picks one, drops others |

## 8.6 Elastic Net: Having Both

$$\Omega(w) = \alpha \|w\|_1 + (1-\alpha)\|w\|_2^2$$

- Blends L1 sparsity with L2 stability. <mark style="background: #FFF3A3A6;">Useful when you have correlated features and still want some zeroing-out behavior</mark>.

---

## 8.7 The Bias-Variance Tradeoff
#bias_variance_tradeoff
- Regularization is a direct handle on the bias-variance tradeoff:
$$\text{Expected Error} = \text{Bias}^2 + \text{Variance} + \text{Irreducible Noise}$$
- **Variance:** How much does the model change if we retrain on slightly different data? High variance = overfitting = memorizing noise.
- **Bias:** How wrong is the model on average, even with infinite data? High bias = underfitting = too simple to capture the signal.

**Increasing $\lambda$:**
- Weights are pushed smaller → model becomes simpler
- Bias increases (we're constraining the model)
- Variance decreases (model is less sensitive to training data fluctuations)

**Decreasing $\lambda$:**
- Weights are free to grow → model becomes more expressive
- Bias decreases
- Variance increases

The optimal $\lambda$ lives somewhere in the middle, found via **cross-validation** — train at multiple $\lambda$ values, pick the one with the best held-out performance.

---

## 8.8 sklearn Convention: $C = \frac{1}{\lambda}$

sklearn's `LogisticRegression` uses $C$ instead of $\lambda$:

$$C = \frac{1}{\lambda}$$

**Large $C$** → small penalty → less regularization → more complex model
**Small $C$** → large penalty → more regularization → simpler model

This trips people up constantly. Remember: $C$ is *inversely* related to
regularization strength.
```python
from sklearn.linear_model import LogisticRegression

# Strong regularization (simple model)
model = LogisticRegression(C=0.01, penalty='l2')

# Weak regularization (complex model)
model = LogisticRegression(C=100, penalty='l2')

# L1 — sparse solution, needs compatible solver
model = LogisticRegression(C=1.0, penalty='l1', solver='liblinear')
```

---

## 8.9 Connection to Bayesian Priors

Regularization has a clean probabilistic interpretation:

- **L2** is equivalent to placing a **Gaussian prior** $\mathcal{N}(0, \frac{1}{2\lambda})$ on each weight and doing MAP estimation instead of MLE. The prior pulls weights toward zero; the data pulls them toward the MLE solution.
- **L1** is equivalent to placing a **Laplace prior** on each weight. The Laplace distribution has heavier tails and a sharper peak at zero —  which is why it encourages sparsity more aggressively.

MLE (no regularization) = flat prior = full trust in the data.
MAP with regularization = informative prior = data + prior belief that weights should be small.

---

## Player Churn Example

Suppose you've engineered 50 features: session counts, event types, social connections, purchase history, device type, time-of-day patterns...

- **L2:** All 50 features contribute something. Good if you believe churn is driven by many small signals acting together.
- **L1:** Model zeroes out 35 features, keeps 15. Easier to interpret —  you can present "these are the 15 signals that drive churn" to stakeholders. Good if you suspect a few features dominate.

In practice for player segmentation work: L1 or Elastic Net often wins when feature count is high and you want a story to tell alongside the model.