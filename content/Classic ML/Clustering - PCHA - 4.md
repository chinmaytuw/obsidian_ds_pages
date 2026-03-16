---
tags:
  - clustering
  - pcha
  - "#objective-function"
  - "#pca"
  - "#nmf"
  - "#k_means"
---


# PCHA Series — Section 5: The PCHA Objective Function — What the Algorithm Optimizes

**Series:** [[PCHA Player Archetype Analysis]]
**Previous:** [[Clustering - PCHA - 3]]
**Next:** [[]]

---


## 🎯 5. Core Idea

PCHA finds archetypes and membership weights by solving a **constrained least-squares problem**: minimize the reconstruction error of the data while keeping:
	(1) each player as a convex combination of archetypes, and 
	(2) each archetype as a convex combination of actual data points.

Both constraints live on simplices. The objective function is what ties them together.

---

## 📐 5.1 The Full Objective Function

$$\min_{\mathbf{S}, \mathbf{B}} \; \|\mathbf{X} - \mathbf{S}\mathbf{B}\mathbf{X}\|_F^2$$

subject to:
$$s_{ij} \geq 0, \quad \sum_{j=1}^{k} s_{ij} = 1 \quad \forall i \quad \text{(player weights on simplex)}$$
$$b_{ji} \geq 0, \quad \sum_{i=1}^{n} b_{ji} = 1 \quad \forall j \quad \text{(archetypes on simplex)}$$
> - $C = BX$ from the earlier note. They are the same thing.
> - The matrix B (a k×n matrix, rows summing to 1) enforces this by expressing each archetype as a convex combination of actual data points


> [!Example]
> - Assume a scenario where you have 100 players and 10 features and 3 archetypes. The matrix flow would look like:
>  
$$\mathbf{X}_{100 \times 10} \approx \mathbf{S}_{100 \times 3} \cdot \mathbf{B}_{3 \times 100} \cdot \mathbf{X}_{100 \times 10}$$
Or as a step-by-step breakdown:
$$\mathbf{X}_{100 \times 10} \xrightarrow{\mathbf{B}_{3 \times 100}} \mathbf{C}_{3 \times 10} \xrightarrow{\mathbf{S}_{100 \times 3}} \hat{\mathbf{X}}_{100 \times 10}$$
Where each arrow means "multiplied by", and $\hat{\mathbf{X}}$ is your reconstruction. The goal is to minimize 
$$|\mathbf{X} - \hat{\mathbf{X}}|_F^2$$  **X — your data (fixed, never changes)** Shape: 100×10 Rows = players, Columns = features  
S — simplex weights (encoder) Shape: 100×3 Each row is a player's membership across 3 archetypes, summing to 1. So player ii i's row might look like [0.7,0.2,0.1]. 
B — archetype construction weights - Shape: 3×100. Each row defines one archetype as a convex combination of all 100 players. Row entries sum to 1. Most weights will be near-zero — only a few "extreme" players matter. B answers "which real players anchor this archetype?"
C — archetypes (derived, not learned directly) - Shape: 3×10. This is just BX, so (3×100)(100×10)=3×10. Three archetypes, each a 10-dimensional feature vector. C is the archetype positions in feature space.


---

### 5.1.2 Unpacking the Notation

| Symbol | Shape | Meaning |
|---|---|---|
| $\mathbf{X}$ | $n \times d$ | Original data matrix ($n$ players, $d$ features) |
| $\mathbf{S}$ | $n \times k$ | Membership weights — how each player mixes archetypes |
| $\mathbf{B}$ | $k \times n$ | Archetype weights — how each archetype mixes data points |
| $\mathbf{C} = \mathbf{B}\mathbf{X}$ | $k \times d$ | The actual archetype feature vectors |
| $\|\cdot\|_F$ | — | Frobenius norm — square root of sum of squared element errors |

The reconstruction chain is:

$$\mathbf{X} \xrightarrow{\mathbf{B}} \mathbf{C} = \mathbf{B}\mathbf{X} \xrightarrow{\mathbf{S}} \hat{\mathbf{X}} = \mathbf{S}\mathbf{C} = \mathbf{S}\mathbf{B}\mathbf{X}$$

---

## 🔑 5.2 The Two Simplex Constraints — Why Both?

### 5.2.1 Constraint 1: $\mathbf{S}$ — Players as mixtures of archetypes

- This is what we covered in Section 4. Every player $\mathbf{x}_i$ is reconstructed as a convex combination of the $k$ archetypes:

$$\hat{\mathbf{x}}_i = \sum_{j=1}^{k} s_{ij} \, \mathbf{c}_j, \quad s_{ij} \geq 0, \quad \sum_j s_{ij} = 1$$

### 5.2.2 Constraint 2: $\mathbf{B}$ — Archetypes as mixtures of real players

- <mark style="background: #FFB86CA6;">This is what makes PCHA unique. Each archetype</mark> $\mathbf{c}_j$ <mark style="background: #FFB86CA6;">must itself be a convex combination of actual observed players</mark>:

$$\mathbf{c}_j = \sum_{i=1}^{n} b_{ji} \, \mathbf{x}_i, \quad b_{ji} \geq 0, \quad \sum_i b_{ji} = 1$$

- This forces archetypes to lie **inside the data cloud** — specifically, near the extremes of the convex hull. Archetypes cannot be fictional points invented by the algorithm; they must be grounded in real players.

> **Intuition:** Your "Aggressive Rusher" archetype has to be a weighted average of actual aggressive rushers in your data, not a hallucinated feature vector.

---

## 📉 5.3 The Loss: Frobenius Norm Reconstruction Error

- The Frobenius norm squared is just the sum of squared errors across all players and all features:

$$\|\mathbf{X} - \hat{\mathbf{X}}\|_F^2 = \sum_{i=1}^{n} \sum_{l=1}^{d} (x_{il} - \hat{x}_{il})^2 = \sum_{i=1}^{n} \|\mathbf{x}_i - \hat{\mathbf{x}}_i\|_2^2$$

- Minimizing this pushes the archetypes to positions where the simplex-constrained reconstruction is as faithful as possible to the original data.
- Essentially, you just **square every element, sum them all up, take the square root**. In our previous example case:
$$|\mathbf{X} - \hat{\mathbf{X}}|_F^2 = \sum_{i=1}^{100} \sum_{j=1}^{10} (x_{ij} - \hat{x}_{ij})^2$$
<mark style="background: #BBFABBA6;">Which is simply the **sum of squared errors across every cell** in the matrix — every player, every feature</mark>.

---

## 🔄 5.4 Why This Pushes Archetypes to the Extremes

This is subtle but critical. The tension works as follows:

1. To minimize reconstruction error, archetypes need to **span the data** as widely as possible — covering the full range of player behavior.
2. The convex hull geometry from Section 3 means that a wider span = archetypes closer to the extreme edges of the data.
3. Any archetype that sits in the interior of the data cloud is wasteful — players near it could be represented more accurately if the archetype were pushed outward.

So the objective function implicitly drives archetypes toward the **vertices of the convex hull** even without explicitly optimizing for that. The simplex constraint on $\mathbf{B}$ ensures they can't leave the data entirely.

---

## ⚙️ 5.5 It's a Bi-Convex Problem

The objective is **not jointly convex** in $(\mathbf{S}, \mathbf{B})$ — it's quadratic in each separately when the other is held fixed. This structure is called **bi-convex**.

$$\mathcal{L}(\mathbf{S}, \mathbf{B}) = \|\mathbf{X} - \mathbf{S}\mathbf{B}\mathbf{X}\|_F^2$$

- Fix $\mathbf{B}$, optimize $\mathbf{S}$: constrained least squares → each row of $\mathbf{S}$ is a **simplex-projected least squares** problem.
- Fix $\mathbf{S}$, optimize $\mathbf{B}$: same structure → each row of $\mathbf{B}$ is a **simplex-projected least squares** problem.

This enables **alternating optimization** (coordinate descent), which is how PCHA is solved in practice. The algorithm alternates between the two steps until convergence. We'll dig into the mechanics in Section 6.

>[!Side note]
>This will come up later but here's how we arrive at a solution
>### The Feedback Loop for optimization 
>This is the key insight for understanding the full algorithm. After encoding all players with fixed $\mathbf{C}$, you look at the reconstruction errors across the whole dataset and ask: *"where should I move the archetypes to reduce total error?"* 
>That's the decoder/archetype update step. Then you re-encode everyone. Repeat until convergence. 
>``` 
>Initialize C, S randomly 
>↓ [Encoder step] Fix C → solve for all S rows independently 
>↓ [Decoder step] Fix S → update C (push archetypes toward data extremes) 
>↓ Repeat until ||X - SC||²_F stops decreasing
>```
>S does get initialized too — typically as uniform weights like `[0.33, 0.33, 0.33]` for k=3, or random valid weights (non-negative, summing to 1). The uniform start is common because it's a neutral "I don't know yet" position before any optimization has happened.

---
## 🧮 5.6 Expanded Form (Useful for Intuition)

Substituting $\mathbf{C} = \mathbf{B}\mathbf{X}$:

$$\mathcal{L} = \|\mathbf{X} - \mathbf{S}\mathbf{C}\|_F^2 = \text{tr}\left[(\mathbf{X} - \mathbf{S}\mathbf{C})^\top (\mathbf{X} - \mathbf{S}\mathbf{C})\right]$$

Expanding:

$$= \text{tr}(\mathbf{X}^\top \mathbf{X}) - 2\,\text{tr}(\mathbf{C}^\top \mathbf{S}^\top \mathbf{X}) + \text{tr}(\mathbf{C}^\top \mathbf{S}^\top \mathbf{S} \mathbf{C})$$

The first term is constant (data variance), so optimization only touches the last two terms. This is useful when implementing gradient updates — the gradient w.r.t. $\mathbf{S}$ is:

$$\frac{\partial \mathcal{L}}{\partial \mathbf{S}} = -2(\mathbf{X} - \mathbf{S}\mathbf{C})\mathbf{C}^\top = -2\,\mathbf{R}\,\mathbf{C}^\top$$

where $\mathbf{R} = \mathbf{X} - \mathbf{S}\mathbf{C}$ is the residual matrix.

Similarly, the gradient w.r.t. $\mathbf{B}$ (treating $\mathbf{C} = \mathbf{B}\mathbf{X}$):

$$\frac{\partial \mathcal{L}}{\partial \mathbf{B}} = -2\,\mathbf{S}^\top \mathbf{R}\, \mathbf{X}^\top \cdot \frac{1}{\|\cdot\|}$$

(projected onto the simplex after each gradient step)

---

## 🕹️ Battlefield 6 Framing

You have $n = 500{,}000$ players and $d = 20$ behavioral features (K/D, revives, movement speed, etc.). You want $k = 5$ archetypes.

The objective asks: <mark style="background: #FFB86CA6;">find 5 "extreme" player profiles ({B}) and a mixing recipe for each player (S) such that, if you blend each player from those 5 profiles, you get as close to their actual stats as possible.</mark>

The loss tells you how well your archetype model explains the player population. Watching it decrease during training tells you the archetypes are gaining explanatory power.

---

## 📊 Comparing Objective Functions: PCHA vs Others

| Model | Objective | Constraints |
|---|---|---|
| PCA | $\|\mathbf{X} - \mathbf{S}\mathbf{C}\|_F^2$ | $\mathbf{C}$ orthonormal, no constraint on $\mathbf{S}$ |
| K-Means | $\sum_i \|\mathbf{x}_i - \mathbf{c}_{z_i}\|^2$ | $\mathbf{S}$ one-hot (hard assignment) |
| NMF | $\|\mathbf{X} - \mathbf{S}\mathbf{C}\|_F^2$ | $\mathbf{S} \geq 0$, $\mathbf{C} \geq 0$ (no sum-to-1) |
| PCHA | $\|\mathbf{X} - \mathbf{S}\mathbf{B}\mathbf{X}\|_F^2$ | Both $\mathbf{S}$ and $\mathbf{B}$ on simplex |
>[!side-note]
>NMF: Non-negative Matrix Factorization.

PCHA's distinguishing feature in this table: **$\mathbf{C}$ is not a free parameter** — it's forced to be $\mathbf{B}\mathbf{X}$, i.e., a convex combination of real data points.

---

## 📝 Summary

$$\boxed{\min_{\mathbf{S}, \mathbf{B}} \; \|\mathbf{X} - \mathbf{S}\mathbf{B}\mathbf{X}\|_F^2 \quad \text{s.t.} \quad \mathbf{S}, \mathbf{B} \text{ row-stochastic, non-negative}}$$

- **$\mathbf{B}$** anchors archetypes to real data → interpretability
- **$\mathbf{S}$** gives every player a soft archetype membership → richness
- **Frobenius loss** measures how well the model explains variance
- **Bi-convex structure** → solved via alternating simplex-projected least squares
- **No explicit extremality term** — the geometry does the work; pushing archetypes outward naturally minimizes reconstruction error

---

## 🔗 Connected Concepts

-

---

*Tags: #PCHA #objective-function #optimization  
