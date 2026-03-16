---
tags:
  - classic_ml
  - classification
  - logistic_regression
---

continued from [[Log Regression - 2 - Sigmoid]]
## Module 3 — The Model: What Logistic Regression Is _Actually_ Predicting

### 3.1 Layman Intuition First

- You already know the sigmoid squashes any real number into $(0, 1)$, giving you a probability. But here's the question most courses skip:
	What is the model doing on the _inside_?
- The dirty secret: <mark style="background: #ADCCFFA6;">logistic regression is still just a linear model</mark>. It draws a straight line (or hyperplane in higher dimensions) — just not in probability-space. <mark style="background: #ADCCFFA6;">It draws the line in a cleverly transformed space called log-odds space</mark>. Understanding this transformation is the key intuition most people miss.
- The answer comes from thinking about the problem differently. Instead of asking *"how do we squash a linear model into (0,1)?"* — ask the reverse:
> 		**What transformation of probability gives us something that is naturally linear in the features?**
- Think of it like this: you can't fit a straight line to probabilities because probabilities are bounded between 0 and 1, and a line is unbounded. So <mark style="background: #ADCCFFA6;">we unwrap the probability into something unbounded, fit a line there, then re-wrap it back into a probability</mark>.

>[!Quiz]
>Log regression is still linear. It is still drawing a line but in log-odds space.

---

### 3.2 Odds — The First Transformation

- Before log-odds, let's build up from **odds**, which you already know from sports betting.
- If the probability of an event is $p$, the **odds** are:
$$\text{odds} = \frac{p}{1 - p}$$

| $p$ (probability) | $\text{odds}$ |
| ----------------- | ------------- |
| 0.1               | 0.11 (1:9)    |
| 0.5               | 1.0 (1:1)     |
| 0.9               | 9.0 (9:1)     |
| 0.99              | 99.0          |

- Notice: <mark style="background: #ADCCFFA6;">odds are bounded below at 0 but unbounded above</mark>. Getting closer, but still not symmetric — and still not unbounded in both directions.

---

### 3.3 Log-Odds (The Logit) — The Final Transformation

- Take the natural log of the odds:
$$\text{logit}(p) = \log\left(\frac{p}{1-p}\right)$$
This is called the **logit function** (log + unit = logit). Now look what happens:

|$p$|$\text{odds}$|$\text{logit}(p)$|
|---|---|---|
|0.01|0.01|$-4.6$|
|0.1|0.11|$-2.2$|
|0.5|1.0|$0.0$|
|0.9|9.0|$+2.2$|
|0.99|99|$+4.6$|
> [!important]
> - Notice the table above, $p \in (0, 1)$, $odds \in (0,\infty)$, $logits \in (-\infty,+\infty)$
> - It's $(-\infty,+\infty)$ and not $[-\infty,+\infty]$ . Open brackets here infinities are excluded not included.
> - A probability of exactly 0 or exactly 1 is undefined in log-odds space. So you never actually *reach* ∞ — you approach it as a limit.
> - Also, notice the symmetry — p=0.1p=0.1 p=0.1 and p=0.9p=0.9 p=0.9 are equidistant from 0 in log-odds space. Uncertainty (p=0.5p=0.5 p=0.5) maps to exactly 0. This is a natural, well-behaved space to work in.

The logit maps $p \in (0, 1)$ to $(-\infty, +\infty)$. **It's now unbounded in both directions** — and perfectly symmetric around 0.
<mark style="background: #FFB86CA6;">This is the space where logistic regression is linear.</mark>

---
### 3.4 The Model Is Linear in Log-Odds Space

<mark style="background: #FFB86CA6;">Here is the core assumption of logistic regression — stated plainly:</mark>

>$$logit(p)=\log\left(\frac{p}{1-p}\right) = \beta_0 + \beta_1 x_1 + \beta_2 x_2 + \cdots + \beta_n x_n=z$$

> **The log-odds of the outcome is a linear function of the features.**

- <mark style="background: #FFB86CA6;">This is an assumption</mark> — not something we derive from first principles. It's the logistic regression analogue of assuming $y = \mathbf{w}^T \mathbf{x}$ in linear regression. We choose it because:
	1. It produces an S-shaped probability curve (as we'll show below)
	2. It matches empirical patterns in many real-world classification problems
	3. It keeps the model interpretable and computationally tractable
	
> Just as linear regression assumes the mean of $y$ is linear in $\mathbf{x}$ — logistic regression assumes the **log-odds** of $p$ is linear in $\mathbf{x}$.

### 3.5 Recovering the Sigmoid

- Now watch what happens when we solve for $p$ from the log-odds equation. This is a clean derivation — every step follows from algebra alone.
- Start with:
$$\log\left(\frac{p}{1-p}\right) = \mathbf{w}^T \mathbf{x}$$
- Exponentiate both sides:
$$\frac{p}{1-p} = e^{\mathbf{w}^T \mathbf{x}}$$
- Solve for $p$ — multiply both sides by $(1-p)$:
$$p = e^{\mathbf{w}^T \mathbf{x}} (1 - p) = e^{\mathbf{w}^T \mathbf{x}} - p \cdot e^{\mathbf{w}^T \mathbf{x}}$$
- Collect $p$ terms:

$$p + p \cdot e^{\mathbf{w}^T \mathbf{x}} = e^{\mathbf{w}^T \mathbf{x}}$$
$$p\left(1 + e^{\mathbf{w}^T \mathbf{x}}\right) = e^{\mathbf{w}^T \mathbf{x}}$$
- Divide:
$$p = \frac{e^{\mathbf{w}^T \mathbf{x}}}{1 + e^{\mathbf{w}^T \mathbf{x}}}$$
- Multiply numerator and denominator by $e^{-\mathbf{w}^T \mathbf{x}}$:
$$\boxed{p = \frac{1}{1 + e^{-\mathbf{w}^T \mathbf{x}}} = \sigma(\mathbf{w}^T \mathbf{x})}$$
- The sigmoid isn't arbitrary — it is the unique function you get when you assume log-odds linearity and solve for probability. The choice of sigmoid and the choice of log-odds linearity are two sides of the same coin.


>[!Key Formula]
>$$p = \text{logit}^{-1}(z) = \frac{1}{1 + e^{-z}} = \sigma(z)$$

---
### 3.6 Interpreting the Weights

- This is where log-odds makes the model interpretable. Since:
$$\log\left(\frac{p}{1-p}\right) = w_0 + w_1 x_1 + \cdots + w_n x_n$$
- A unit increase in feature $x_j$ increases the log-odds by exactly $w_j$ — holding all other features constant.
- Equivalently, it **multiplies the odds by $e^{w_j}$**:
$$\frac{p_{\text{new}}}{1-p_{\text{new}}} = e^{w_j} \cdot \frac{p_{\text{old}}}{1-p_{\text{old}}}$$
- In the churn example:
	- $w_j = +0.5$ for "days since last session" means each additional day *multiplies* the churn odds by $e^{0.5} \approx 1.65$ — a 65% increase in odds
	- $w_j = -0.8$ for "hours played last week" means each additional hour *multiplies* churn odds by $e^{-0.8} \approx 0.45$ — a 55% reduction in odds
---

### 3.7 The Full Picture — Three Equivalent Views

- It helps to hold all three representations simultaneously:

>[!Key Insight]
>$$\underbrace{\log\left(\frac{p}{1-p}\right) = \mathbf{w}^T\mathbf{x}}_{\text{linear in log-odds space}} \iff \underbrace{\frac{p}{1-p} = e^{\mathbf{w}^T\mathbf{x}}}_{\text{exponential in odds space}} \iff \underbrace{p = \sigma(\mathbf{w}^T\mathbf{x})}_{\text{sigmoid in probability space}}$$

| Representation | Space | Range | Linearity? |
|---|---|---|---|
| $\mathbf{w}^T \mathbf{x}$ | Log-odds | $(-\infty, +\infty)$ | ✅ Yes |
| $e^{\mathbf{w}^T \mathbf{x}}$ | Odds | $(0, +\infty)$ | ❌ No |
| $\sigma(\mathbf{w}^T \mathbf{x})$ | Probability | $(0, 1)$ | ❌ No |

- The model is linear exactly once — in log-odds space. Everything else is a transformation of that linear structure.
---
## Summary

| Concept               | Key Takeaway                                                |
| --------------------- | ----------------------------------------------------------- |
| Odds                  | $\frac{p}{1-p}$ — ratio of success to failure               |
| Log-odds (logit)      | $\log\frac{p}{1-p}$ — maps probability to $\mathbb{R}$      |
| Core assumption       | Log-odds is linear in features — assumed, not derived       |
| Sigmoid origin        | Algebraic consequence of log-odds linearity                 |
| Weight interpretation | $e^{w_j}$ = multiplicative change in odds per unit of $x_j$ |
