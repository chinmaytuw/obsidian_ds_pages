---
tags:
  - regression
---

# 1. Motivation
- This is similar to [[Akaike information criterion]] and <mark style="background: #FFB86CA6;">BIC does the same thing, but it penalizes complexity more heavily when you have lots of data</mark>.
-  The formula is: 
	```
	 BIC = n·ln(RSS/n) + k·ln(n)
	 
	 Where:
		- n = number of observations
		- RSS = residual sum of squares (how much error is left)
		- k = number of parameters (including the intercept)
	```

- Notice that $k·ln(n)$ penalty term? As your sample size grows, ln(n) grows, so BIC punishes extra parameters more and more. AIC's penalty is just 2k, which doesn't change with sample size.

# 2. Example

### 2.1 Example 1: Simple Linear Regression

**Model:** 
```
Price = β₀ + β₁·SquareFeet + error

	Given:
		- n = 100
		- RSS = 5000
		- k = 2
```


**Calculation:**

```
Step 1: n·ln(RSS/n)
RSS/n = 5000/100 = 50
ln(50) ≈ 3.91
100 × 3.91 = 391

Step 2: k·ln(n)
ln(100) ≈ 4.61
2 × 4.61 = 9.22

Step 3: BIC
BIC = 391 + 9.22 = 400.22
```

---

### 2.2 Example 2: Multiple Linear Regression

**Model:** 
```
Price = β₀ + β₁·SquareFeet + β₂·Bedrooms + error

	Given:
	- n = 100
	- RSS = 4200 (lower due to additional predictor)
	- k = 6
```

**Calculation:**

```
Step 1: n·ln(RSS/n)
RSS/n = 4200/100 = 42
ln(42) ≈ 3.74
100 × 3.74 = 374

Step 2: k·ln(n)
ln(100) ≈ 4.61
6 × 4.61 = 27.66

Step 3: BIC
BIC = 374 + 27.66 = 401.66
```

### 2.3 Interpretation:
- Even though Model 2 has lower RSS, its BIC = 401.66 is _higher_ than Model 1's BIC = 400.22. **BIC prefers the simpler model** because the improvement in fit doesn't justify adding 4 extra parameters.


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