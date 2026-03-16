---
tags:
  - clustering
  - pcha
---

# 3. The Geometry Behind PCHA — Convex Hulls and Simplices

## 3.1 Convex Sets

A set of points is **convex** if, for any two points in the set, the straight line connecting them lies entirely within the set — "no dents or caves."

A circle — convex. A crescent — not convex. Your player data cloud in feature space — not convex, but we can wrap a convex shape around it.

---

## 3.2 Convex, concave, con what???
- This bummed me. Why convex? why not concave? For reference, here is convex vs concave. They are just opposites. 
![[convex_vs_concave.png]]
>[!Trick]
>The trick to remember this is concave is like ceiling of a cave. So quite literally think cave.

- **For PCHA specifically:** a convex combination `t·A + (1−t)·B` with `t ∈ [0,1]` is just any point on the _line segment_ between A and B — it stays inside, never outside. When PCHA says each player is a convex combination of archetypes, <mark style="background: #FFB86CA6;">it means every player is a weighted blend of the extreme types where the weights are non-negative and sum to 1</mark>. The archetypes themselves live on the convex hull — the outermost "skin" wrapping your player data cloud — which is why they're interpretable as pure extreme player types rather than abstract directions like PCA components.
- A set is called convex if, for any two points in the set, the line segment connecting them also lies entirely within the set. The reason it's called "convex" is that the set of all such combinations of weights forms exactly the **convex hull** of your points — the smallest convex set that contains all your data.
- Think of it <mark style="background: #ADCCFFA6;">geometrically</mark>: If you have a cloud of data points, their convex hull is like a "shrink-wrapped" boundary around them. Any convex combination of those points lands somewhere inside (or on the boundary of) that shrink-wrap. You literally cannot escape the hull if your weights are non-negative and sum to 1.
- <mark style="background: #ADCCFFA6;">There's no such thing as a concave combination</mark>. The contrast is:
	- **Convex combination** → weights ≥ 0, sum to 1 → stays _inside_ the convex hull
	- **Affine combination** → weights sum to 1, but can be negative → can go anywhere on the line
	- **Linear combination** → weights unrestricted → can go anywhere in space.

---

## 3.3 The Convex Hull

The **convex hull** of a dataset is the smallest convex shape containing all points. Geometrically: stretch a rubber band around your data and let it snap tight.

Formally, the convex hull of $\mathbf{X} = \{\mathbf{x}_1, \dots, \mathbf{x}_N\}$ is all points writable as:

$$\mathbf{x} = \sum_{i=1}^{N} \lambda_i \mathbf{x}_i, \quad \lambda_i \geq 0, \quad \sum_{i=1}^{N} \lambda_i = 1$$

These are exactly the simplex constraints from Section 2. **Every point inside a convex hull is a convex combination of its boundary points.** This is the geometric foundation of PCHA.

---

## 3.4 What is a Simplex?

A **simplex** is the simplest possible convex shape in a given number of dimensions:

| Dimensions | Simplex shape | Vertices |
|---|---|---|
| 1D | Line segment | 2 |
| 2D | Triangle | 3 |
| 3D | Tetrahedron | 4 |
| $K$D | $K$-simplex | $K+1$ |
>[!Caution]
>The dimension here has nothing to do with the actual number of features in the data. This is more around the number of archetypes
>

A $K$-archetype PCHA model fits a simplex with $K$ vertices to your data. The vertices **are the archetypes**. Every player sits somewhere inside this simplex, described by their **barycentric coordinates** — which are exactly the archetype weights $\mathbf{s}_i$.

---

## 3.5 Barycentric Coordinates — The Bridge to Weights

![[barycentric.png]]

- In a triangle (2-simplex) with archetype vertices $\mathbf{z}_1, \mathbf{z}_2, \mathbf{z}_3$, any point $\mathbf{p}$ inside can be written as:

$$\mathbf{p} = s_1 \mathbf{z}_1 + s_2 \mathbf{z}_2 + s_3 \mathbf{z}_3, \quad s_1 + s_2 + s_3 = 1, \quad s_k \geq 0$$

- These $s_1, s_2, s_3$ are the **barycentric coordinates** — and exactly the PCHA membership weights. Instead of saying "this player is at (8, 6) in feature space", barycentric says "this player is 0.5·A1 + 0.3·A2 + 0.2·A3". Those three numbers _are_ the barycentric coordinates. They're more interpretable for PCHA because they directly tell you the archetype mixture rather than a position in raw feature space.
- Instead of describing a point's position using fixed axes (x=5, y=3), you describe it as _how much of each corner_ it is.

| Player position                                   | Weights                                   |
| ------------------------------------------------- | ----------------------------------------- |
| At vertex $\mathbf{z}_1$                          | $[1, 0, 0]$                               |
| At centroid of triangle                           | $[\frac{1}{3}, \frac{1}{3}, \frac{1}{3}]$ |
| Halfway between $\mathbf{z}_1$ and $\mathbf{z}_2$ | $[0.5, 0.5, 0]$                           |

- Proximity to a vertex *is* membership — the geometry makes weights immediately interpretable.

---

## 3.6 What PCHA is Actually Doing Geometrically

> **Find the $K$-vertex simplex that best approximates the convex hull of the data, where the vertices must themselves lie within the data cloud.**

Two simultaneous goals:

**1. Fit the simplex to the data.**
- Push vertices toward the extremes to <mark style="background: #BBFABBA6;">minimize reconstruction error</mark> — most players should be well-approximated by their archetype mixture.
- To best approximate the convex hull, the vertices want to be pushed as far out as possible — ideally _on_ the actual convex hull boundary. The further out the vertices, the more of the data cloud they "span," and the smaller the reconstruction error.
- If this Goal had full control, it would just <mark style="background: #ADCCFFA6;">place archetypes exactly on the extreme boundary points of your data</mark>.

**2. Keep vertices inside the data.**
- Archetypes must be <mark style="background: #BBFABBA6;">convex combinations of real data points</mark> (the $\mathbf{X}\mathbf{A}$ term). No extrapolation beyond actual players.
- The $XA$ constraint says each archetype must be a convex combination of *all* data points. This sounds permissive, but here's the catch — it's a *weighted average* of real players. <mark style="background: #ADCCFFA6;">Weighted averages pull toward the center of mass</mark>. The more players you average in, the more the result gets dragged toward the interior of the data cloud.
- A convex combination of many points rarely lands exactly at an extreme. To get a vertex near a true extreme point, you'd need to put almost all weight on that one extreme player — but that's brittle and the optimizer has to fight hard for it.

### 3.6.2 **Where the tension actually lives:**

- Imagine your data is a cloud shaped like a rough triangle. Goal 1 says "put an archetype at each pointy corner." Goal 2 says "but each archetype must be a weighted average of real players." 
- The corners of the actual convex hull might only have _one or two_ real players sitting there. A convex combination heavily weighted toward those sparse extreme players is geometrically valid, but the optimization landscape makes it hard to achieve — and if those extreme players are slightly noisy outliers, the solution becomes unstable.
- So in practice, the archetypes end up slightly _pulled inward_ from the true convex hull boundary. The algorithm is constantly negotiating: push out to explain more variance, pull back to stay representable as real player mixtures. That negotiation is the non-trivial optimization.

---

### 3.6.3 Why $K$ Vertices and Not the Full Convex Hull?

- The true convex hull of your player data might have thousands of vertices — one per boundary player. That's not useful. PCHA approximates the convex hull with just $K$ vertices, where $K$ is small enough to be interpretable (typically 3–10 for player segmentation).

You're finding the $K$-point summary that best captures the extremal structure of the data — <mark style="background: #ADCCFFA6;">fitting a clean triangle to a roughly-triangular cloud rather than tracing every jagged edge</mark>.

---

## 3.7 Visualizing the 3-Archetype Case

- Players plotted in 2D (e.g. after PCA). PCHA with $K=3$ finds three points near the outer boundary — the three most extreme player types. Every other player is located inside the triangle, with their position determining archetype weights. Refer to the image above as well.

```
        z1 (Hardcore F2P)
       / \
      /   \
     /  p  \        p = [0.5, 0.3, 0.2]
    /       \
   z2 ------z3
(Whale)  (Casual Spender)
```

	- Close to vertex → mostly that archetype
	- Near edge midpoint → ~50/50 split between two archetypes
	- Near centroid → roughly equal mix of all three

---

## Key Takeaways

- The **convex hull** is the smallest convex wrapper around your data — PCHA approximates it
- A **simplex** is a triangle (2D), tetrahedron (3D), etc. — PCHA fits one with $K$ vertices
- Archetype weights $\mathbf{s}_i$ are **barycentric coordinates** inside the simplex — geometrically grounded, not arbitrary
- PCHA simultaneously **pushes vertices to extremes** and **keeps them grounded in real data**
- The full convex hull has too many vertices to be useful — PCHA finds the best $K$-point approximation

---


**Series:** [[PCHA Player Archetype Analysis]]
*Previous: [[Clustering - PCHA - 1]]*
*Next: [[Clustering - PCHA - 3]]*

