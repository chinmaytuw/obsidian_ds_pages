---
tags:
  - classification
  - "#logistic_regression"
---
# Key points
- 


# 1. Logistic Regression

- Instead of predicting a continuous value, we predict the **probability** of a given class.
> $$Pr(default=Yes|balance)$$
- We cannot use linear regression here — it can produce values below 0 or above 1.
- The **logistic function** squeezes the output to the range $(0, 1)$:
> $$p(X) = \frac{e^{\beta_0 + \beta_1 X}}{1 + e^{\beta_0 + \beta_1 X}} \tag{4.2}$$

> [!AI Note] 
> - the logistic regression function _is_ the probability.
> - p(X) here represents **P(Y=1 | X)**, the conditional probability that the outcome is 1 given X. The logistic (sigmoid) function is simply the mathematical form chosen to model that probability. So when people say "the logistic regression function," they mean the specific functional form used to estimate the probability.

## 2. Odds and Log Odds

- Rearranging (4.2) gives us the **odds**:
> $$\frac{p(X)}{1-p(X)} = e^{\beta_0 + \beta_1 X} \tag{4.3}$$
- Odds range from $0$ to $\infty$. Close to $0$ = very unlikely, close to $\infty$ = very likely.
	- $p(X) = 0.2 \implies$ odds $= \frac{0.2}{0.8} = 1/4$ (1 in 5 default)
	- $p(X) = 0.9 \implies$ odds $= \frac{0.9}{0.1} = 9$ (9 in 10 default)
- Taking the log of both sides gives the **log odds** (or #logit):
> $$\log\left(\frac{p(X)}{1-p(X)}\right) = \beta_0 + \beta_1 X \tag{4.4}$$
- The logit is **linear in $X$** — this is the key property of logistic regression.

## 3. Interpreting $\beta_1$

| Model               | Interpretation of $\beta_1$                         |
| ------------------- | --------------------------------------------------- |
| Linear Regression   | Average change in $Y$ per one-unit increase in $X$  |
| Logistic Regression | Change in **log odds** per one-unit increase in $X$ |

- A one-unit increase in $X$ **changes the log odds by $\beta_1$** (from 4.4)
- Equivalently, it **multiplies the odds by $e^{\beta_1}$** (from 4.3)
- $\beta_1$ does **not** correspond to a fixed change in $p(X)$ — the effect depends on the current value of $X$ (S-shaped curve)

> [!important]
> Regardless of the value of $X$:
> - If $\beta_1 > 0$ → increasing $X$ increases $p(X)$
> - If $\beta_1 < 0$ → increasing $X$ decreases $p(X)$

> [!Remember]
> The key insight from this form is that while the relationship between p(X) and X is nonlinear (the S-curve), the relationship between the **log-odds** and X is perfectly linear. So logistic regression is really just linear regression on the log-odds scale.

## 4. Estimating the Coefficients

- We use [[Maximum Likelihood Estimation]] as #objective-function  to estimate $\beta_0$ and $\beta_1$. The loss function is binary cross entropy. #cross_entropy And [[gradient descent]] as solver.
- Intuition: find $\hat{\beta}_0$ and $\hat{\beta}_1$ such that:
	- $\hat{p}(x_i) \approx 1$ for individuals who defaulted
	- $\hat{p}(x_i) \approx 0$ for individuals who did not
- This is formalized with the <mark style="background: #FFB86CA6;">likelihood function</mark>. We then take the log of likelihood for computational ease.
>$$\ell(\beta_0, \beta_1) = \prod_{i:\, y_i=1} p(x_i) \prod_{i':\, y_{i'}=0} (1 - p(x_{i'})) \tag{4.5}$$
- We choose $\hat{\beta}_0$ and $\hat{\beta}_1$ to **maximize** this function.

![[log_reg_summary_stats.png]]

### 4.1 Interpreting the Output
- $\hat{\beta}_1 = 0.0055$: a one-unit increase in `balance` increases the **log odds** of `default` by $0.0055$.
- Statistical testing mirrors linear regression:
	- Coefficient accuracy is measured via #standard_error
	- The #z_statistic plays the same role as the #t_statistic in linear regression ([[Regression 2- Simple LR]])
	- $z = \hat{\beta}_1 / \text{SE}(\hat{\beta}_1)$ — a large $|z|$ is evidence against $H_0: \beta_1 = 0$
- Under $H_0: \beta_1 = 0$, the model reduces to $p(X) = \frac{e^{\beta_0}}{1 + e^{\beta_0}}$, meaning `balance` has no effect on default probability.

## 5. Making Predictions
- Once coefficients are estimated, we plug them into the logistic function to get predicted probabilities: $$\hat{p}(X) = \frac{e^{\hat{\beta}_0 + \hat{\beta}_1 X}}{1 + e^{\hat{\beta}_0 + \hat{\beta}_1 X}}$$
### 5.1 Example: Predicting Default from Balance 
- Using the estimates from Table 4.1, the predicted default probability for an individual with a `balance` of $\$1{,}000$: $$\hat{p}(X) = \frac{e^{-10.6513 + 0.0055 \times 1000}}{1 + e^{-10.6513 + 0.0055 \times 1000}} = 0.00576 \approx 0.576\%$$ 
- For comparison, a `balance` of $\$2{,}000$ yields a predicted probability of $0.586$ or $58.6\%$ — dramatically higher, illustrating the non-linear nature of the model.

### 5.2 Using Qualitative Predictors 
- Qualitative predictors can be incorporated using the **dummy variable** approach (Section 3.3.1). For example, `student` status is encoded as: $$\text{student[Yes]} = \begin{cases} 1 & \text{if student} \\ 0 & \text{if non-student} \end{cases}$$

#### 5.2.2 Predicted Probabilities by Student Status

$$\widehat{\Pr}(\texttt{default=Yes} \mid \texttt{student=Yes}) = \frac{e^{-3.5041 + 0.4049 \times 1}}{1 + e^{-3.5041 + 0.4049 \times 1}} = 0.0431$$

$$\widehat{\Pr}(\texttt{default=Yes} \mid \texttt{student=No}) = \frac{e^{-3.5041 + 0.4049 \times 0}}{1 + e^{-3.5041 + 0.4049 \times 0}} = 0.0292$$

> [!summary] Key Takeaway
Students have a default probability of **4.31%** vs **2.92%** for non-students — a meaningful difference captured by the positive $\hat{\beta}_1 = 0.4049$.


## 6. The Math behind

### 6.1 The Setup

We want to predict a binary outcome $y \in {0, 1}$ given input $x$. The model outputs a probability:

$$\hat{y} = \sigma(z) = \frac{1}{1 + e^{-z}}, \quad z = \mathbf{w} \cdot \mathbf{x} + b$$

---

### 6.2 Step 1: The Likelihood Function

Assume each sample is Bernoulli-distributed. For a single sample $(x_i, y_i)$:
$$P(y_i \mid x_i) = \hat{y}_i^{y_i} \cdot (1 - \hat{y}_i)^{1 - y_i}$$

This handles both cases naturally:
- If $y_i = 1$: gives $\hat{y}_i$
- If $y_i = 0$: gives $1 - \hat{y}_i$

For $N$ i.i.d. samples, the **joint likelihood** is:
$$\mathcal{L}(\mathbf{w}, b) = \prod_{i=1}^{N} \hat{y}_i^{y_i} \cdot (1 - \hat{y}_i)^{1 - y_i}$$
---

### 6.3 Step 2: Log-Likelihood

Products are painful to differentiate, so we take the log:
$$\log \mathcal{L} = \sum_{i=1}^{N} \left[ y_i \log \hat{y}_i + (1 - y_i) \log(1 - \hat{y}_i) \right]$$

We want to **maximize** this. Equivalently, we minimize the **negative log-likelihood**, which is our **Binary Cross-Entropy loss**:
$$\mathcal{J}(\mathbf{w}, b) = -\frac{1}{N} \sum_{i=1}^{N} \left[ y_i \log \hat{y}_i + (1 - y_i) \log(1 - \hat{y}_i) \right]$$

---

### 6.4 Step 3: Deriving the Gradients

We need $\frac{\partial \mathcal{J}}{\partial \mathbf{w}}$ and $\frac{\partial \mathcal{J}}{\partial b}$. Using the chain rule:
$$\frac{\partial \mathcal{J}}{\partial w_j} = \frac{\partial \mathcal{J}}{\partial \hat{y}} \cdot \frac{\partial \hat{y}}{\partial z} \cdot \frac{\partial z}{\partial w_j}$$

#### 6.4.1 Part 1: Loss w.r.t. prediction
$$\frac{\partial}{\partial \hat{y}} \left[ y \log \hat{y} + (1-y) \log(1-\hat{y}) \right] = \frac{y}{\hat{y}} - \frac{1-y}{1-\hat{y}}$$

#### 6.4.2 Part 2: Sigmoid derivative

We want to compute $\frac{d}{dz} \sigma(z)$ where: $$\sigma(z) = \frac{1}{1 + e^{-z}} = (1 + e^{-z})^{-1}$$ **Apply the chain rule** (power rule on the outer function, then differentiate the inside): $$\frac{d}{dz}(1 + e^{-z})^{-1} = -1 \cdot (1 + e^{-z})^{-2} \cdot \frac{d}{dz}(e^{-z})$$ The derivative of $e^{-z}$ is $-e^{-z}$, so: $$= -1 \cdot (1 + e^{-z})^{-2} \cdot (-e^{-z}) = \frac{e^{-z}}{(1 + e^{-z})^{2}}$$ **Now we rewrite this in terms of $\sigma(z)$ itself.** Split the fraction: $$\frac{e^{-z}}{(1 + e^{-z})^{2}} = \frac{1}{1 + e^{-z}} \cdot \frac{e^{-z}}{1 + e^{-z}}$$ The left factor is just $\sigma(z)$. For the right factor, add and subtract 1 in the numerator: $$\frac{e^{-z}}{1 + e^{-z}} = \frac{(1 + e^{-z}) - 1}{1 + e^{-z}} = 1 - \frac{1}{1 + e^{-z}} = 1 - \sigma(z)$$ Putting it together: $$\boxed{\frac{d\sigma}{dz} = \sigma(z)\left(1 - \sigma(z)\right) = \hat{y}(1 - \hat{y})}$$**Why this is remarkable:** the derivative is expressible purely in terms of the output $\hat{y}$ itself — no need to store $z$ separately during backprop. And since $\hat{y} \in (0, 1)$, the product $\hat{y}(1-\hat{y})$ is always positive and is maximized at $\hat{y} = 0.5$ (maximum uncertainty), and approaches 0 near $\hat{y} = 0$ or $1$ (high confidence). This is what causes **vanishing gradients** when sigmoid outputs are saturated. 
> **Intuition check:** Think of $\hat{y}(1-\hat{y})$ as the variance of a Bernoulli($\hat{y}$) random variable. The gradient is largest when the model is most uncertain — exactly when it should be learning the most.
#### 6.4.3 Part 3: Linear part derivatives

$$\frac{\partial z}{\partial w_j} = x_j, \qquad \frac{\partial z}{\partial b} = 1$$

#### 6.4.4 Chaining it together
$$\frac{\partial \mathcal{J}}{\partial \hat{y}} \cdot \frac{\partial \hat{y}}{\partial z} = \left(\frac{y}{\hat{y}} - \frac{1-y}{1-\hat{y}}\right) \cdot \hat{y}(1-\hat{y})$$

Expanding — the $\hat{y}(1-\hat{y})$ cancels beautifully:
$$= y(1 - \hat{y}) - (1 - y)\hat{y} = y - y\hat{y} - \hat{y} + y\hat{y} = y - \hat{y}$$

So the full gradients (with the negative sign from $\mathcal{J}$, averaged over $N$):

$$\boxed{\frac{\partial \mathcal{J}}{\partial \mathbf{w}} = \frac{1}{N} \sum_{i=1}^{N} (\hat{y}_i - y_i) \mathbf{x}_i}$$
$$\boxed{\frac{\partial \mathcal{J}}{\partial b} = \frac{1}{N} \sum_{i=1}^{N} (\hat{y}_i - y_i)}$$

> The gradient is just the **prediction error** times the input. Clean and intuitive.

---

## 6.5: Gradient Descent Update Rule
$$\mathbf{w} \leftarrow \mathbf{w} - \eta \cdot \frac{\partial \mathcal{J}}{\partial \mathbf{w}}$$
$$b \leftarrow b - \eta \cdot \frac{\partial \mathcal{J}}{\partial b}$$

where $\eta$ is the learning rate.

---

## 6.6 Key Intuitions

- The gradient $(\hat{y}_i - y_i)\mathbf{x}_i$ has a clean story: **when your prediction is wrong, the update is large; when it's right, near zero.**
- The sigmoid's derivative canceling out with the loss derivative is not a coincidence — it's why **cross-entropy is the right loss for logistic regression.**
- Pairing **MSE loss with sigmoid** doesn't give you this clean cancellation, leading to vanishing gradients in the loss landscape and slow learning.