---
tags:
  - clustering
  - pcha
  - "#nmf"
  - "#k_means"
---

# PCHA Series — Section 4: Convex Combinations and Membership Weights

**Series:** [[PCHA Player Archetype Analysis]]
**Previous:** [[Clustering - PCHA - 2]]
**Next:** [[Clustering - PCHA - 4]]

---

## 🎯 4.1 Core Idea 

In PCHA, every player is represented as a **weighted mixture of archetypes**. These weights are called **membership weights** (or **soft assignments**), and they must form a **convex combination** — meaning they are non-negative and sum to 1.

This is the mechanism that makes PCHA fundamentally different from k-means: instead of "this player belongs to Archetype 3", you get "this player is 60% Rusher, 30% Support, 10% Camper."

---

## 📐 4.2 Mathematical Definition

### 4.2.1 Convex Combination

- A point $\mathbf{x}$ is a **convex combination** of points $\mathbf{a}_1, \mathbf{a}_2, \dots, \mathbf{a}_k$ if:

> $$\mathbf{x} = \sum_{j=1}^{k} s_j \, \mathbf{a}_j$$


- subject to:

> $$s_j \geq 0 \quad \forall j, \qquad \sum_{j=1}^{k} s_j = 1$$
> where $\forall j$ means "for all j"

These constraints define the **simplex** — the set of all valid weight vectors.

---

### 4.2.2 The Membership Weight Matrix $\mathbf{S}$

- For $n$ players and $k$ archetypes, we define an $n \times k$ matrix:

$$\mathbf{S} \in \mathbb{R}^{n \times k}$$

where entry $s_{ij}$ is the weight of player $i$ on archetype $j$.

- **Constraints (applied row-wise):**

$$s_{ij} \geq 0 \quad \forall i, j\text{(for all i,j)}$$

$$\sum_{j=1}^{k} s_{ij} = 1 \quad \forall i \text{(for all i)}$$

So each **row** of $\mathbf{S}$ lives on the $(k-1)$-dimensional probability simplex $\Delta^{k-1}$.

---

### 4.2.3 Reconstructing Players from Archetypes

- Let $\mathbf{C} \in \mathbb{R}^{k \times d}$ be the **archetype matrix** (k archetypes, each a d-dimensional feature vector).
- The **reconstruction** of the data matrix $\mathbf{X} \in \mathbb{R}^{n \times d}$ is:
$$\hat{\mathbf{X}} = \mathbf{S} \mathbf{C}$$
- Each player $i$ is approximated as:
$$\hat{\mathbf{x}}_i = \sum_{j=1}^{k} s_{ij} \, \mathbf{c}_j$$
This is the **decoder** step: map from the simplex back to feature space.

---

## 🔑 4.3 Why the Simplex Constraint Matters

| Property | Effect |
|---|---|
| $s_{ij} \geq 0$ | No "negative archetype" — weights are interpretable |
| $\sum_j s_{ij} = 1$ | Weights are proportions — directly readable as percentages |
| Combined | Player lives **inside** the convex hull of archetypes |

- Without the sum-to-1 constraint you'd have a **conic combination** — still non-negative, but weights could be arbitrarily large. Without non-negativity you'd recover PCA-style projections. The simplex is the intersection of both constraints.

---

## 🕹️ 4.4 Battlefield 6 Intuition

- Imagine your three archetypes are:
	- $\mathbf{c}_1$ = **Aggressive Rusher** (high K/D, high damage, low survival time)
	- $\mathbf{c}_2$ = **Support Anchor** (high revives, high heal output, low kills)
	- $\mathbf{c}_3$ = **Recon Camper** (high spotting assists, low deaths, low movement)

- A player with $\mathbf{s}_i = [0.6, 0.3, 0.1]$ is:
	- 60% Aggressive Rusher
	- 30% Support Anchor
	- 10% Recon Camper

This is immediately actionable for matchmaking, content recommendations, or churn prediction — far richer than a hard cluster label.

---

## 📊 4.5 The Geometry of Membership Weights

- For $k = 3$ archetypes, the valid weight vectors form a **triangle** (2-simplex):
	- Vertices = pure archetypes → $[1, 0, 0]$, $[0, 1, 0]$, $[0, 0, 1]$
	- Edge midpoints = 50/50 blends
	- Interior = mixed players

- The **position on the simplex** encodes behavioral identity. Players near a vertex are "pure types"; players near the centroid $[\frac{1}{k}, \dots, \frac{1}{k}]$ are "generalists."

---

## ⚠️ 4.6 Contrast with Other Models

### K-Means
K-means assigns each player a **one-hot** weight vector: $\mathbf{s}_i \in \{0, 1\}^k$, $\sum_j s_{ij} = 1$. This is a degenerate simplex — only vertices are valid.

### NMF (Non-negative Matrix Factorization)
NMF enforces $s_{ij} \geq 0$ but **not** $\sum_j s_{ij} = 1$. Weights are non-negative but not proportions — harder to interpret as percentages.

### PCHA
Full simplex: both constraints active. Weights are proportions, non-negative, and geometrically meaningful.

---

## 🔁 4.7 Dual Role: Encoder and Decoder

PCHA learns two things simultaneously:

**Encoder:** Given player features $\mathbf{x}_i$, find the simplex weights $\mathbf{s}_i$:

$$\mathbf{s}_i = \text{argmin}_{s \in \Delta^{k-1}} \|\mathbf{x}_i - \mathbf{S}_i \mathbf{C}\|^2$$

**Decoder:** Reconstruct players from archetypes:

$$\hat{\mathbf{x}}_i = \mathbf{S}_i \mathbf{C} = \sum_{j=1}^{k} s_{ij} \mathbf{c}_j$$

This is analogous to an autoencoder where the **bottleneck is a simplex** rather than a Gaussian latent space.

---

## 📝 Summary

| Symbol | Meaning | Constraints |
|---|---|---|
| $\mathbf{S} \in \mathbb{R}^{n \times k}$ | Membership weight matrix | Rows sum to 1, all entries $\geq 0$ |
| $\mathbf{C} \in \mathbb{R}^{k \times d}$ | Archetype matrix | Each row is an archetype in feature space |
| $s_{ij}$ | Weight of player $i$ on archetype $j$ | $s_{ij} \in [0, 1]$ |
| $\hat{\mathbf{X}} = \mathbf{S}\mathbf{C}$ | Reconstructed data | Approximates original $\mathbf{X}$ |

---