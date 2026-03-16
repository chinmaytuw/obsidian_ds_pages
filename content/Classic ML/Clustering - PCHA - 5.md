---
tags:
  - clustering
  - "#pcha"
---


# PCHA Series — Section 6: The Optimization Algorithm — Alternating Simplex-Projected Least Squares

**Series:** [[PCHA Player Archetype Analysis]]
**Previous:** [[Clustering - PCHA - 4]]
**Next:** [[Clustering - PCHA - 6]]

---

## 🎯 Core Idea

PCHA's objective is bi-convex — hard to solve jointly, but easy to solve for one matrix when the other is fixed. The algorithm exploits this by **alternating** between two constrained least-squares subproblems, each of which projects a solution onto a simplex. This is coordinate descent on the simplex.

---

## 🔄 The Alternating Algorithm — High Level

**Initialize:** Set $\mathbf{B}^{(0)}$ randomly (row-stochastic i.e. row sums up to 1) → compute $\mathbf{C}^{(0)} = \mathbf{B}^{(0)}\mathbf{X}$

**Repeat until convergence:**

1. **Update $\mathbf{S}$** — fix $\mathbf{C}$, solve for membership weights
2. **Update $\mathbf{B}$** — fix $\mathbf{S}$, solve for archetype weights
3. **Recompute $\mathbf{C} = \mathbf{B}\mathbf{X}$**
4. **Check convergence** — if $\|\mathcal{L}^{(t)} - \mathcal{L}^{(t-1)}\| < \epsilon$, stop

---

## ⚙️ Step 1 — Updating $\mathbf{S}$ (Membership Weights)

Fix $\mathbf{C} = \mathbf{B}\mathbf{X}$. We need to solve, **for each player $i$ independently:**

$$\min_{s_i \in \Delta^{k-1}} \|\mathbf{x}_i - \mathbf{C}^\top s_i\|_2^2$$

where $\Delta^{k-1}$ is the $(k-1)$-simplex: $s_i \geq 0$, $\sum_j s_{ij} = 1$.

This is a **Quadratic Program (QP) with simplex constraints** — one per player. Because players are independent given $\mathbf{C}$, all $n$ problems can be solved in parallel.

### The Frank-Wolfe approach (used in original PCHA)

Rather than a full QP solver, PCHA uses **Frank-Wolfe (conditional gradient)** iteration per player:

1. Compute gradient: $\nabla_{s_i} \mathcal{L} = -2\,\mathbf{C}(\mathbf{x}_i - \mathbf{C}^\top s_i) = -2\,\mathbf{C}\,\mathbf{r}_i$, where $\mathbf{r}_i = \mathbf{x}_i - \hat{\mathbf{x}}_i$ is the residual
2. Find the vertex of the simplex that minimizes the linear approximation:
$$j^* = \arg\min_j \; [\mathbf{C}\,\mathbf{r}_i]_j$$
3. Move toward that vertex: $s_i \leftarrow (1 - \alpha)\,s_i + \alpha\,\mathbf{e}_{j^*}$, where $\mathbf{e}_{j^*}$ is the one-hot vector and $\alpha \in (0,1]$ is a step size

Frank-Wolfe is attractive here because **each step automatically stays on the simplex** — no projection required. The iterate is always a convex combination of the current point and a vertex.

---

## ⚙️ Step 2 — Updating $\mathbf{B}$ (Archetype Weights)

Fix $\mathbf{S}$. For each archetype $j$ independently:

$$\min_{b_j \in \Delta^{n-1}} \|\mathbf{S}(:,j) - \mathbf{X}\,b_j\|_2^2$$

Wait — let's be precise. Substituting $\mathbf{C} = \mathbf{B}\mathbf{X}$ into the objective:

$$\mathcal{L} = \|\mathbf{X} - \mathbf{S}\mathbf{B}\mathbf{X}\|_F^2$$

The gradient w.r.t. row $b_j$ of $\mathbf{B}$:

$$\nabla_{b_j} \mathcal{L} = -2\,(\mathbf{S}^\top \mathbf{R})_j \cdot \mathbf{X}^\top$$

where $\mathbf{R} = \mathbf{X} - \mathbf{S}\mathbf{B}\mathbf{X}$ is the full residual matrix.

Again, Frank-Wolfe is used:

1. Compute linear minimization over the simplex → find which actual player $i^*$ minimizes $[\mathbf{S}^\top \mathbf{R} \cdot \mathbf{X}^\top]_j$
2. Step toward $\mathbf{e}_{i^*}$: $b_j \leftarrow (1 - \alpha)\,b_j + \alpha\,\mathbf{e}_{i^*}$

This means each update moves each archetype slightly toward the **single most informative data point** for that archetype — exactly the "pull toward extremes" behavior we want.

---

## 📉 Convergence Behavior

The loss $\mathcal{L}^{(t)} = \|\mathbf{X} - \mathbf{S}^{(t)}\mathbf{B}^{(t)}\mathbf{X}\|_F^2$ is **monotonically non-increasing** at each alternating step (each subproblem is solved to at least as good a solution as the current iterate).

However, because the overall problem is non-convex:

- **Global optimum is not guaranteed** — the algorithm converges to a local minimum or saddle point
- **Multiple restarts** with different $\mathbf{B}^{(0)}$ initializations are standard practice
- In practice, PCHA tends to find stable, interpretable solutions across restarts when $k$ is well-chosen

A typical convergence curve looks like rapid early descent followed by a long plateau — most of the structure is captured in the first few dozen iterations.

---

## 🧮 Step Size $\alpha$ — The Line Search

The step size $\alpha$ at each Frank-Wolfe step can be:

**Fixed:** $\alpha_t = \frac{2}{t+2}$ — provably convergent for convex problems, reasonable in practice here

**Line search:** Find $\alpha^*$ that minimizes the objective along the Frank-Wolfe direction:

$$\alpha^* = \arg\min_{\alpha \in [0,1]} \mathcal{L}(s_i + \alpha(\mathbf{e}_{j^*} - s_i))$$

Because the objective is quadratic in $\alpha$ along this direction, the line search has a **closed-form solution**:

$$\alpha^* = \min\left(1, \;\max\left(0, \;\frac{\mathbf{r}_i^\top \mathbf{c}_{j^*} - \mathbf{r}_i^\top \hat{\mathbf{x}}_i}{\|\mathbf{c}_{j^*} - \hat{\mathbf{x}}_i\|^2}\right)\right)$$

The original PCHA paper uses this closed-form line search — it costs little extra per step and speeds convergence significantly.

---

## 🖼️ Geometric Picture of One Iteration

Imagine you're updating player $i$'s membership weights $s_i$:

1. $s_i$ currently says "60% Rusher, 30% Support, 10% Camper" — a point inside the triangle
2. Compute residual $\mathbf{r}_i = \mathbf{x}_i - \hat{\mathbf{x}}_i$ — how far the reconstruction misses the true player
3. Find which archetype vertex "points most toward" correcting that residual → say Camper ($j^* = 3$)
4. Slide $s_i$ a bit toward the Camper vertex: new weights might be "57% Rusher, 28% Support, 15% Camper"
5. Repeat until the residual is zero or the step size collapses

The geometry: Frank-Wolfe traces a **zig-zag path inside the simplex**, each step toward a vertex, converging to the point on the simplex closest to the unconstrained optimum.

---

## 🕹️ Battlefield 6 Framing

**Outer loop iteration, what's happening:**

- **S-update:** For each of your 500K players, nudge their archetype blend toward whichever archetype would best explain the gap between their predicted and actual stats.
- **B-update:** For each of your 5 archetypes, nudge its definition toward whichever real player best exemplifies what's missing from that archetype's current profile.

After ~100–200 iterations, the archetypes stabilize into extreme-but-real behavioral profiles and the player weights settle into interpretable soft memberships.

---

## 🐍 Python Sketch — What the Loop Looks Like

```python
import numpy as np

def pcha(X, k, max_iter=200, tol=1e-6):
    n, d = X.shape
    
    # Initialize B randomly (row-stochastic)
    B = np.random.dirichlet(np.ones(n), size=k)  # shape (k, n)
    C = B @ X  # shape (k, d) — initial archetypes
    S = np.random.dirichlet(np.ones(k), size=n)  # shape (n, k)
    
    loss_prev = np.inf
    
    for t in range(max_iter):
        # --- Update S (membership weights) ---
        R = X - S @ C  # residual (n, d)
        for i in range(n):
            # Frank-Wolfe: find best vertex
            grad = -(C @ R[i])  # (k,) — negative because we minimize
            j_star = np.argmin(grad)
            # Closed-form line search
            diff = C[j_star] - S[i] @ C
            alpha = np.clip(R[i] @ diff / (diff @ diff + 1e-12), 0, 1)
            S[i] += alpha * (np.eye(k)[j_star] - S[i])
        
        # --- Update B (archetype weights) ---
        R = X - S @ B @ X  # recompute residual
        StR = S.T @ R  # (k, d)
        for j in range(k):
            grad = -(StR[j] @ X.T)  # (n,)
            i_star = np.argmin(grad)
            diff = X[i_star] - B[j] @ X
            alpha = np.clip(StR[j] @ diff / (diff @ diff + 1e-12), 0, 1)
            B[j] += alpha * (np.eye(n)[i_star] - B[j])
        
        C = B @ X
        
        # --- Check convergence ---
        loss = np.linalg.norm(X - S @ C, 'fro') ** 2
        if abs(loss_prev - loss) < tol:
            print(f"Converged at iteration {t}, loss={loss:.4f}")
            break
        loss_prev = loss
    
    return S, B, C
```

> **Note:** This is a pedagogical sketch. In production use `py_pcha` or a vectorized implementation — the inner loops over $n$ players are the bottleneck and should be batched or parallelized.

---

## ⚠️ Practical Notes

| Issue | What to do |
|---|---|
| Slow convergence | Increase Frank-Wolfe iterations per outer step; use warm starts |
| Different results each run | Run multiple random initializations; pick lowest final loss |
| Archetypes collapse (duplicates) | Increase $k$, or add a small diversity penalty |
| Loss plateaus early but archetypes look wrong | Check scaling — normalize features before running PCHA |
| Memory: $n$ is huge | Use a random subsample for the $\mathbf{B}$ update; archetypes still anchor to real points |

---

## 📝 Summary

| Step | What's fixed | What's updated | Method |
|---|---|---|---|
| S-update | $\mathbf{C} = \mathbf{B}\mathbf{X}$ | $\mathbf{S}$ (membership weights) | Frank-Wolfe per player |
| B-update | $\mathbf{S}$ | $\mathbf{B}$ (archetype weights) | Frank-Wolfe per archetype |
| Convergence | — | Monitor $\|\mathcal{L}^{(t)} - \mathcal{L}^{(t-1)}\|$ | Stop when $< \epsilon$ |

The algorithm is **simple, parallelizable, and geometrically intuitive** — each update is just a step toward the most helpful simplex vertex given the current residual.

---

## 🔗 Connected Concepts



---

