---
tags:
  - clustering
  - "#pcha"
---
# 1. Why Archetype Analysis? 

## 1.1 Traditional Methods
- When product says _"we want to understand our players better"_, what they usually mean underneath is:
	- Who are the distinct _types_ of people playing our game?
	- What does each type care about (PvP, progression, cosmetics, social play)?
	- How do we make content decisions that serve the right types?
This sounds like a clustering problem — and it is — but the _kind_ of clustering matters a lot.
- K-Means partitions players into groups by finding cluster **centroids** — the average of each group. This works okay for separating players into rough buckets, but it has a fundamental weakness for persona work:
> **The centroid of a cluster often represents nobody.**
- Imagine your two player extremes are a _hardcore ranked grinder_ who plays 4 hours a day and never touches cosmetics, and a _casual social player_ who plays 30 minutes occasionally and spends heavily on skins. The K-Means centroid between them would be a medium-session, moderate-spender who grinds a little and buys a few skins. That player profile might not exist in your data at all. It's a mathematical average, not a real human.
When product asks _"who is this segment?"_, you want to be able to point at actual, real, extreme examples of player behavior — not a statistical ghost.

## 1.2 What Archetype Analysis Does Differently

- Archetype analysis flips the philosophy:
> Instead of finding the **center** of groups, find the **extremes** that best explain the full range of behavior.

- These extremes are called **archetypes** — pure, idealized player types that sit at the boundary of your data. Every real player is then described as a **mixture** of these archetypes. A player might be 70% Hardcore Grinder, 20% Cosmetic Collector, and 10% Social Player. That's a much richer and more interpretable description than "Cluster 3."

### 1.2.2 Two big practical advantages:

1. <mark style="background: #FFF3A3A6;">Archetypes are interpretable</mark>. Because they're extreme, their defining features are loud and clear. The Hardcore Grinder archetype will have near-maximum values on session length, match count, ranked play — it's obvious what they are.
2. <mark style="background: #FFF3A3A6;">Membership is continuous, not binary.</mark> K-Means assigns each player to exactly one cluster. Archetype analysis gives every player a _weight vector_ across all archetypes. This is more honest — most players aren't pure anything — and more useful for personalization and targeting.

---

# 2. How PCHA Differs from K-Means & PCA

## 2.1 Quick Recap

<mark style="background: #FFF3A3A6;">K-Means</mark> partitions data by minimizing within-cluster variance. Each point belongs to exactly one cluster, defined by its centroid — the mean of all assigned points. #k_means

**<mark style="background: #FFF3A3A6;">PCA</mark>** finds a lower-dimensional linear subspace capturing maximum variance. Points are represented as linear combinations of principal components, but coefficients can be negative and don't sum to 1. It's about compression, not interpretability of extremes. #pca

---
## 2.2 The Fundamental Difference from K-Means

K-Means solves: $$\min_{\{\boldsymbol{\mu}_k\}, \{z_i\}} \sum_{i=1}^{N} \| \mathbf{x}_i - \boldsymbol{\mu}_{z_i} \|^2$$ where $\boldsymbol{\mu}_k$ is the centroid of cluster $k$ and $z_i \in \{1, \dots, K\}$ is a hard assignment. 

PCHA solves: $$\min_{\mathbf{A}, \mathbf{S}} \sum_{i=1}^{N} \| \mathbf{x}_i - \mathbf{X}\mathbf{A}\mathbf{s}_i \|^2$$ **Two critical differences:** 
1. **Archetypes must be made from real data points.** The term $\mathbf{X}\mathbf{A}$ means archetypes are convex combinations of actual data points — no centroid can live in empty space. 
2. **Every player is a soft mixture.** The weights $\mathbf{s}_i$ are non-negative and sum to 1, so every player is a probabilistic blend of archetypes, not a hard assignment.

---
## 2.3 The Fundamental Difference from PCA
PCA decomposes data as: $$\mathbf{X} \approx \mathbf{Z}\mathbf{W}$$where $\mathbf{Z}$ are scores and $\mathbf{W}$ are principal component loadings. **No constraints** on signs or sums — a player can have a negative score on a component, which has no intuitive meaning. PCHA enforces the **simplex constraint**: $$s_{ik} \geq 0, \quad \sum_{k=1}^{K} s_{ik} = 1$$ This is what makes output interpretable as *"percentage of each archetype"* rather than *"projection along an abstract axis."* It also means archetypes must lie within the convex hull of your data — they have to be plausible player profiles. 

---
## 2.4 Concrete Player Analytics 
Example Say players are described by two features: **session length** and **spend per month**. Your data forms a rough triangle with three extremes: 
- **Top-left:** long sessions, no spend → *Hardcore F2P* 
- **Top-right:** long sessions, high spend → *Whale Grinder* 
- **Bottom-right:** short sessions, high spend → *Casual Spender* 


- **K-Means (k=3):** Three blob centroids sitting in the middle of each region — averages, not extremes. 
- **PCA (2 components):** Two axes like "overall engagement" and "spend vs. playtime tradeoff" — useful for compression, not directly nameable as player types. 
- **PCHA (k=3):** Points near the three corners. Archetypes *are* the extremes. Every player described by how much they lean toward each corner. ---
---

**Series:** [[PCHA Player Archetype Analysis]]
*Next: [[Clustering - PCHA - 2]]*
