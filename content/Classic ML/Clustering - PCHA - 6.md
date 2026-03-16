---
tags:
  - pcha
  - clustering
  - k_means
---
# PCHA Series — Section 7: Choosing the Number of Archetypes k

**Series:** [[PCHA Player Archetype Analysis]]
**Previous:** [[Clustering - PCHA - 5]]
**Next:** [[Clustering - PCHA - 7]] 

---

## The Core Challenge

In K-Means, choosing $k$ is already notoriously tricky. PCHA has the same problem, but with a different flavor: you're not just asking "how many clusters?" but **"how many *extreme* behavioral profiles does my player population meaningfully span?"**

- Too few archetypes → distinct playstyles collapse into a blurry average
- Too many archetypes → you fit noise; archetypes represent one-off edge cases, not true behavioral poles

The goal is the **smallest $k$ that still captures the meaningful extremes** of your player space.

---

## What PCHA Gives You to Work With

The primary diagnostic is the **reconstruction loss** (residual sum of squares) from the PCHA objective:

$$\text{RSS}(k) = \| X - X B A \|_F^2$$

As you increase $k$, #RSS  **monotonically decreases** — more archetypes always let you reconstruct the data better. So raw RSS alone can't tell you when to stop. You need principled stopping criteria.

---

## Method 1: The Scree / Elbow Plot

Plot RSS (or **variance explained**) against $k$ and look for an elbow — a point where adding another archetype gives diminishing returns.

$$\text{Variance Explained}(k) = 1 - \frac{\text{RSS}(k)}{\| X - \bar{X} \|_F^2}$$

The denominator is the total variance in $X$ (sum of squared deviations from the column mean) — giving you a familiar $R^2$-like quantity ranging from 0 to 1. #r_squared 

> [!warning] Practical Caveat
> The elbow is often ambiguous with player data. Behavioural spaces tend to be continuous, so the curve may be smooth without a sharp kink. Use this as a **first pass, not the final word**.

---

## Method 2: Marginal Variance Gain

Formalise the elbow by computing the **marginal gain** of adding each archetype:

$$\Delta(k) = \text{VarExplained}(k) - \text{VarExplained}(k-1)$$

Plot $\Delta(k)$ against $k$. When $\Delta(k)$ drops below a threshold (e.g. gain < 1–2% of total variance), stop.

**Why this is better than eyeballing:** Makes the "elbow" decision explicit and reproducible — important for justifying your choice to stakeholders.

---

## Method 3: Convex Hull Volume Ratio

A PCHA-specific diagnostic grounded in the geometry. The $k$ archetypes define a **simplex** (triangle for $k=3$, tetrahedron for $k=4$, etc.). Compare its volume to the full convex hull of your data:

$$\text{Volume Ratio}(k) = \frac{\text{Vol}(\text{simplex spanned by } C_k)}{\text{Vol}(\text{convex hull of } X)}$$

When this ratio **plateaus** — the new archetype no longer expands the simplex meaningfully — you've captured the extremes.

Where:
- $C_k = XB$ are the archetype coordinates
- $B \in \mathbb{R}^{n \times k}$ is the archetype weight matrix learned by PCHA

> [!note]
> This is more geometrically principled than RSS alone, but computationally expensive in high dimensions. Practical for $d \leq 10$ features; use approximations otherwise.

---

## Method 4: Archetype Interpretability (Human-in-the-Loop)

**Underrated and often decisive in applied settings like game analytics.**

For each candidate $k$, examine what each archetype looks like:

1. Pull the archetype coordinates $C = XB$ and inspect raw feature values
2. Apply the **label test**: can your team name the archetype? ("Aggressive Rusher", "Passive Farmer", "Completionist")
3. If two archetypes are nearly identical in feature space → $k$ is probably too high
4. If an archetype represents behaviour nobody on the team understands → signal of overfitting

In Battlefield specifically: **does archetype $j$ represent a coherent playstyle a game designer would recognise?** If yes, it earns its place.

---

## Method 5: Stability Analysis

Run PCHA with **multiple random initialisations** for the same $k$.

- If archetypes are stable (you reliably get the same extreme points regardless of seed) → that $k$ is supported by the data structure
- If archetypes shuffle significantly across runs → the data doesn't strongly support that many distinct poles

**Stability metric:** Average **Procrustes distance** between archetype sets across runs (after optimal matching via the Hungarian algorithm):

$$\text{Stability}(k) = \mathbb{E}_{\text{runs}} \left[ \min_{\pi} \sum_{j=1}^{k} \| c_j^{(1)} - c_{\pi(j)}^{(2)} \|^2 \right]$$

Low distance = stable = trustworthy.

---

## A Practical Decision Framework for Player Data

```
1. Fit PCHA for k ∈ {2, 3, ..., 12}
2. Plot variance explained + marginal gain Δ(k)
3. Shortlist 2–3 candidate k values from the elbow region
4. For each candidate:
   a. Check stability across random seeds
   b. Inspect archetype feature profiles
   c. Apply the label test with your game design team
5. Pick the k where archetypes are both geometrically stable AND humanly nameable
```

> [!tip] For Battlefield Segmentation
> $k$ in the range of **4–8** is typical for broad behavioural segmentation. Beyond 8, you're usually capturing mode-specific quirks rather than fundamental playstyle differences.

---

## Special Case: Always Run k = 2 First

The two-archetype solution gives you the **single most fundamental axis of variation** in your player space — the two most opposite behavioural poles.

This is a useful sanity check:
- If $k=2$ gives something interpretable (e.g. "objective-focused players vs kills-only players") → PCHA is finding real signal
- If $k=2$ gives something nonsensical → check your feature engineering before increasing $k$

---

## Summary Table

| Method | What It Measures | Best Used For |
|---|---|---|
| Scree / Elbow Plot | Variance explained vs $k$ | Initial exploration |
| Marginal Gain $\Delta(k)$ | Incremental improvement | Reproducible stopping rule |
| Convex Hull Volume Ratio | Geometric coverage | Low-dimensional spaces |
| Interpretability Check | Human label test | Validating final choice |
| Stability Analysis | Consistency across seeds | Confirming $k$ is data-supported |

---

## Key Formulas

| Formula                                                        | Meaning                                 |
| -------------------------------------------------------------- | --------------------------------------- |
| $\text{RSS}(k) = \| X - XBA \|_F^2$                            | Reconstruction loss at $k$ archetypes   |
| $\text{VE}(k) = 1 - \frac{\text{RSS}(k)}{\|X - \bar{X}\|_F^2}$ | Variance explained (0 to 1)             |
| $\Delta(k) = \text{VE}(k) - \text{VE}(k-1)$                    | Marginal gain from adding archetype $k$ |

---

## Tags
#variance 
