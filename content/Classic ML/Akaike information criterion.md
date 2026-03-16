---
tags:
  - regression
---

# 1. Motivation

- AIC tries to answer: "<mark style="background: #FFB86CA6;">Which model balances fit and simplicity best?</mark>"
- You know R² rewards better fit. The problem? <mark style="background: #D2B3FFA6;">Adding any variable increases R²</mark> (or decreases RSS), even if that variable is garbage.
- <mark style="background: #D2B3FFA6;">AIC penalizes you for adding parameters</mark>.

```
AIC = n·ln(RSS/n) + 2k
Where:
	- n = number of observations
	- RSS = residual sum of squares (you know this!)
	- k = number of parameters (including the intercept)
```

**Key insight:** Lower AIC is better. You want small RSS (good fit) but also small k (simple model).

#### **1.2 Understanding ln(RSS/n):**

- RSS/n is just the **average squared residual** — it's measuring how badly your model misses on average.
- Taking the natural log (ln) does two things:
	1. **Puts it on a different scale** - this connects to likelihood (don't worry about the math details)
	2. **Makes the penalty proportional** - doubling RSS doesn't double the AIC, it adds a constant amount

The key intuition: **ln(RSS/n) gets smaller (more negative) when your model fits better** (smaller RSS).

---

**Why not just use RSS directly?**

The ln transformation comes from maximum likelihood theory. For normal linear regression, minimizing RSS is actually the same as maximizing likelihood. The ln connects these two ideas mathematically. [[Maximum Likelihood Estimation]]

But honestly? For _using_ AIC, just remember: **smaller RSS → smaller ln(RSS/n) → smaller AIC → better model** (all else equal).


# 2. Example
### 2.1 AIC Comparison Example: House Price Models

### Setup
- n = 100 houses
- Model 1: Price ~ Size + Bedrooms (k = 3)
- Model 2: Price ~ Size + Bedrooms + Age + Garage (k = 5)

### Fitted Results
- Model 1: RSS = 5,000
- Model 2: RSS = 4,500

---

### 2.2 Model 1 AIC Calculation

$$
\begin{aligned}
AIC_1 &= n \cdot \ln(RSS/n) + 2k \\
&= 100 \cdot \ln(5000/100) + 2(3) \\
&= 100 \cdot \ln(50) + 6 \\
&= 100 \cdot (3.912) + 6 \\
&= 397.2
\end{aligned}
$$
---
### 2.3 Model 2 AIC Calculation

$$
\begin{aligned}
AIC_2 &= n \cdot \ln(RSS/n) + 2k \\
&= 100 \cdot \ln(4500/100) + 2(5) \\
&= 100 \cdot \ln(45) + 10 \\
&= 100 \cdot (3.807) + 10 \\
&= 390.7
\end{aligned}
$$---
### 2.4 Interpretation

- Model 1: AIC = 397.2
- Model 2: AIC = 390.7

**Winner: Model 2** (lower AIC is better)
Model 2's improved fit (lower RSS) outweighs the penalty for 2 extra parameters.

---
### 2.5 **Notice what happened:**

- The penalty for Model 2 was higher (+10 vs +6)
- But the ln(RSS/n) part improved enough to overcome it
- AIC automatically did the tradeoff for us!



# 3. AIC vs BIC

## When to use BIC:

- You want to find the **"true" model** (if you believe one exists)
- You have a **large dataset** (n > 40-50 or so)
- You prefer **simpler, more parsimonious models**
- Common in fields like economics, social sciences

## When to use AIC:

- Your goal is **prediction accuracy**
- You have a **smaller dataset**
- You're okay with slightly more complex models if they predict better
- Common in machine learning, ecology

## The key difference:

BIC's penalty grows with sample size [k·ln(n)], while AIC's is fixed [2k]. So:

- With n=100: BIC penalty ≈ 4.61k vs AIC penalty = 2k
- With n=1000: BIC penalty ≈ 6.91k vs AIC penalty = 2k

**Rule of thumb:** BIC is more conservative. If BIC and AIC disagree, BIC will choose the simpler model.

**In practice?** Many people calculate both and see if they agree. If they point to the same model, you're golden. If they disagree, think about your goal: parsimony (BIC) or prediction (AIC)?