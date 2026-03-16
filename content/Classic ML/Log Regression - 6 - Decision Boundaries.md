---
tags:
---
Continued from [[Log Regression - 5 - Optimization]]


# Module 6 — Decision Boundaries

## 6.1 What Is a Decision Boundary?

- After training, we classify a new example using:
$$\hat{y} = \begin{cases} 1 & \text{if } \hat{p} \geq 0.5 \\ 0 & \text{if } \hat{p} < 0.5 \end{cases}$$
- The decision boundary is the set of points where $\hat{p} = 0.5$ exactly — the model's
	"I genuinely don't know" zone.

- At $\hat{p} = 0.5$:
$$\sigma(w^T x) = 0.5 \implies w^T x = 0$$

- So the decision boundary is defined by:
$$\boxed{w^T x = 0}$$
- This is a **linear equation in feature space**. Which means the boundary is always:
	- A line in 2D
	- A plane in 3D
	- A hyperplane in $n$ dimensions

---

## 6.2 Visualizing It in 2D

- With two features $x_1$ (sessions/week) and $x_2$ (avg session length):
$$w_1 x_1 + w_2 x_2 + b = 0$$
- Rearranging:
$$x_2 = -\frac{w_1}{w_2} x_1 - \frac{b}{w_2}$$
- This is just a line. The weights control its **slope**. The bias $b$ controls its **intercept** — i.e., how far it slides from the origin.
	On one side: $w^T x > 0 \Rightarrow \hat{p} > 0.5 \Rightarrow \hat{y} = 1$
	On the other: $w^T x < 0 \Rightarrow \hat{p} < 0.5 \Rightarrow \hat{y} = 0$
- The further a point is from the boundary, the more confident the model is.
- Probability doesn't jump from 0 to 1 at the line — it fades smoothly via the sigmoid.

---

## 6.3 What the Weights Geometrically Mean
- The weight vector $w = [w_1, w_2]$ is **perpendicular to the decision boundary**.
- It points in the direction of increasing log-odds — i.e., the direction the model is most confident a point belongs to class 1.
- Large $|w_j|$ means feature $x_j$ strongly influences which side of the boundary a point lands on. Small $|w_j|$ means that feature barely moves the boundary.
- This is why L1 regularization (Module 8) is useful for interpretability — it zeros out weak weights entirely, giving you a cleaner geometric picture.

---

## 6.4 The Threshold Is a Choice, Not a Law
- We used 0.5 as the threshold, but this is arbitrary. You can shift it:
$$\hat{y} = 1 \quad \text{if } \hat{p} \geq \tau$$
- **Lower $\tau$** (e.g. 0.3): Model flags more players as churners. Catches more true churners (high recall) but also more false alarms (lower precision).
- **Higher $\tau$** (e.g. 0.7): Model only flags high-confidence churners. Fewer false alarms, but misses borderline cases.
- This threshold shift moves the decision boundary — it's equivalent to translating the hyperplane in feature space. This connects directly to the precision/recall tradeoff in Module 7.

---

## 6.5 When a Linear Boundary Is Enough — and When It Isn't
- Logistic regression can only draw **straight** decision boundaries.
- This works well when classes are **linearly separable** (or close to it):
```
 Retained  | Churned
   o o o   |  x x x
   o o o   |  x x x
-----------+--------
 (line separates them cleanly)
```

- It breaks down when the true boundary is curved or non-linear:
```
    x x x x x
  x o o o o o x
  x o o o o o x
    x x x x x
(churners surround retained players — no line can separate these)
```

### 6.5.2 Ways to handle non-linearity within logistic regression:

#### 1. Feature engineering — add polynomial features:**
$$x_1, x_2 \;\rightarrow\; x_1, x_2, x_1^2, x_2^2, x_1 x_2$$

- The boundary is still linear *in the new feature space*, but curves back in the original space. This is the kernel trick intuition.

#### 2. Interaction terms:**
- If churn depends on the *combination* of low sessions AND short sessions (neither alone is predictive), add $x_1 \cdot x_2$ as a feature.

#### 3. Move to a more expressive model:
- Decision trees, random forests, gradient boosting, or neural networks can learn non-linear boundaries natively. Logistic regression is often the baseline you beat with these.

---

## 6.6 The Connection to Linear Regression (Revisited)

- Remember from Module 3: logistic regression is **linear in log-odds space**.
$$\log \frac{\hat{p}}{1 - \hat{p}} = w^T x$$

- The decision boundary $w^T x = 0$ corresponds to log-odds $= 0$, i.e., odds $= 1$, i.e., $\hat{p} = 0.5$. The linearity of the model *directly produces* the linearity of the boundary. They're the same thing.
- <mark style="background: #FFF3A3A6;">If you understand that the model is a linear function under the hood, the linear boundary is not a limitation to memorize — it's an inevitable consequence.</mark>

---

## 6.7 Player Churn Example — Putting It Together
- Suppose our trained model gives:
$$w_1 = 1.8, \quad w_2 = 0.9, \quad b = -4.5$$
- Where $x_1$ = sessions/week, $x_2$ = avg session length (hours).
- Decision boundary:
$$1.8 x_1 + 0.9 x_2 - 4.5 = 0 \implies x_2 = -2x_1 + 5$$

- Interpretation:
	- Players above this line (high sessions AND long sessions) → predicted retained
	- Players below it → predicted churn
	- The weight ratio $w_1/w_2 = 2$ tells you sessions/week is twice as influential as session length in determining which side of the boundary a player lands on

- A player with 3 sessions/week and 1hr average:
$$1.8(3) + 0.9(1) - 4.5 = 5.4 + 0.9 - 4.5 = 1.8 > 0 \implies \hat{p} > 0.5 \implies \text{predicted retained}$$
- A player with 1 session/week and 1hr average:
$$1.8(1) + 0.9(1) - 4.5 = -1.8 < 0 \implies \hat{p} < 0.5 \implies \text{predicted churn}$$