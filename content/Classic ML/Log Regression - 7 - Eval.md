---
tags:
  - classic_ml
  - classification
  - logistic_regression
---
Continued from [[Log Regression - 6 - Decision Boundaries]]

# Module 7 — Evaluation

## 7.1 The Confusion Matrix: A Richer View of Performance
[[Confusion Matrix]]
- For a binary classifier, every prediction falls into one of four buckets:

|  | **Predicted: Churn** | **Predicted: Retained** |
|---|---|---|
| **Actual: Churn** | True Positive (TP) | False Negative (FN) |
| **Actual: Retained** | False Positive (FP) | True Negative (TN) |

	- **TP:** Caught a real churner. 
	- **TN:** Correctly left a retained player alone. 
	- **FP:** Sent a retention offer to someone who wasn't leaving. (Wasted spend)
	- **FN:** Missed a churner entirely. (Lost player)

- <mark style="background: #ADCCFFA6;">The costs of FP and FN are asymmetric in most real problems</mark>. This asymmetry is why a single number like accuracy hides so much.

---

## 7.2 Accuracy — And Why It Misleads

$$\text{Accuracy} = \frac{TP + TN}{TP + TN + FP + FN}$$

**The class imbalance trap:**

- If 97% of players are retained and 3% churn, a model that predicts "retained" for everyone achieves **97% accuracy** and catches **zero churners**. 
- Accuracy conflates the majority class performance with overall performance. Whenever your classes are imbalanced — which they almost always are in player behavior data — accuracy is not your primary metric.

---

## 7.3 Precision and Recall — The Real Trade-off

### Precision
>- <mark style="background: #FFB86CA6;">Of all the players I flagged as churners, how many actually churned?</mark>
#precision
$$\text{Precision} = \frac{TP}{TP + FP}$$

	High precision → few false alarms. When you alert, you're usually right.
	Low precision → you're crying wolf. Your interventions are noisy.

### Recall (Sensitivity)
> <mark style="background: #FFB86CA6;">Of all the players who actually churned, how many did I catch?</mark>
#recall
$$\text{Recall} = \frac{TP}{TP + FN}$$

High recall → you catch most churners, but maybe with lots of false alarms.
Low recall → you're missing real churners. Silent failures.

### 7.4 The Fundamental Tension

- Precision and recall trade off against each other via the threshold $\tau$:
	- **Lower $\tau$:** Flag more players → recall goes up, precision goes down
	- **Higher $\tau$:** Flag fewer players → precision goes up, recall goes down

There is no free lunch. <mark style="background: #FFF3A3A6;">You have to decide what's costlier</mark> for your use case: missing a churner (FN) or wasting a retention offer (FP). #false_positive #false_negative

---

## 7.5 F1 Score — When You Want One Number

- The harmonic mean of precision and recall: #f1_score
$$F_1 = 2 \cdot \frac{\text{Precision} \cdot \text{Recall}}{\text{Precision} + \text{Recall}}$$

- The harmonic mean punishes extreme imbalance between the two.
- A model with precision = 1.0 and recall = 0.0 gets $F_1 = 0$ — as it should.
- Use F1 when you want a single scalar that respects both metrics.
- Use $F_\beta$ when you want to weight one over the other:
$$F_\beta = (1 + \beta^2) \cdot \frac{\text{Precision} \cdot \text{Recall}}{\beta^2 \cdot \text{Precision} + \text{Recall}}$$
- $\beta > 1$ weights recall more heavily. $\beta < 1$ weights precision.

---

## 7.6 The ROC Curve and AUC

- Rather than evaluating at a single threshold, the ROC curve sweeps across *all possible thresholds* and plots:
	- **X-axis:** False Positive Rate $= \frac{FP}{FP + TN}$ (how often we wrongly flag retained players)
	- **Y-axis:** True Positive Rate $= \frac{TP}{TP + FN}$ (recall — how often we catch churners)

At $\tau = 0$: everything is flagged → TPR = 1, FPR = 1 (top-right)
At $\tau = 1$: nothing is flagged → TPR = 0, FPR = 0 (bottom-left)
As $\tau$ decreases from 1 to 0, we trace a curve between these extremes.
#roc #auc
### AUC — Area Under the Curve

$$\text{AUC} \in [0.5, 1.0]$$
- **AUC = 1.0:** Perfect classifier. Every churner ranked above every retained player.
- **AUC = 0.5:** Random chance. The curve is a diagonal line.
- **AUC = 0.7:** Decent. The model separates the classes better than chance.

**The probabilistic interpretation of AUC:**

> AUC = probability that the model ranks a randomly chosen churner *above* a randomly chosen retained player.

This makes AUC threshold-agnostic — it evaluates the quality of the model's *ranking*, not any single decision. This is why AUC is often preferred in recommendation and ranking contexts.

---

## 7.7 Precision-Recall Curves — Better for Imbalanced Data

- <mark style="background: #ADCCFFA6;">The ROC curve can be overly optimistic when classes are heavily imbalanced</mark>, because FPR is dominated by the large negative class (TN grows fast).
- The **PR curve** plots Precision vs. Recall directly across thresholds. It's harder to game with a dominant negative class.
- <mark style="background: #ADCCFFA6;">AUC-PR is the preferred summary metric when positive class prevalence is very low</mark> — typical in churn, fraud, rare event detection.
- A random classifier on imbalanced data with 3% positives achieves:
	- AUC-ROC ≈ 0.5
	- AUC-PR ≈ 0.03
- This makes AUC-PR much more informative in that regime.

---

## 7.8 Calibration — Does $\hat{p}$ Mean What It Says?

- A model is **well-calibrated** if, among all players it gives $\hat{p} = 0.7$, roughly 70% actually churn.
- Logistic regression tends to be relatively well-calibrated out of the box (MLE training encourages this). Tree-based models often aren't.
- Calibration matters when:
	- You use the raw probability score to make decisions (e.g., size of retention offer)
	- You threshold at values other than 0.5
	- You combine outputs from multiple models

- Visualize calibration with a **reliability diagram**: bucket predictions into bins (0–0.1, 0.1–0.2, ...) and plot mean predicted probability vs. actual fraction positive per bin. A perfectly calibrated model falls on the diagonal.

---

## 7.9 Choosing Your Metric: A Decision Framework

| Situation | Recommended Metric |
|---|---|
| Balanced classes, simple reporting | Accuracy |
| Imbalanced classes, single threshold | F1 or $F_\beta$ |
| Want threshold-agnostic ranking quality | AUC-ROC |
| Very imbalanced, positive class rare | AUC-PR |
| Probabilities used downstream | Calibration + Brier score |
| Business cost asymmetry is known | Custom cost-weighted metric |

---

## 7.10 Player Churn Example — Tying It Together

- Suppose after training we evaluate on a held-out set of 10,000 players where 300 actually churned (3% base rate):

|                     | Predicted Churn | Predicted Retained |
| ------------------- | --------------- | ------------------ |
| **Actual Churn**    | 180 (TP)        | 120 (FN)           |
| **Actual Retained** | 450 (FP)        | 9,250 (TN)         |

$$\text{Accuracy} = \frac{180 + 9250}{10000} = 94.3\%$$
Sounds great. But:
$$\text{Precision} = \frac{180}{180 + 450} = 28.6\%$$
$$\text{Recall} = \frac{180}{180 + 120} = 60\%$$
$$F_1 = 2 \cdot \frac{0.286 \cdot 0.6}{0.286 + 0.6} = 0.387$$

- Interpretation: We're catching 60% of churners, but 71% of our alerts are false alarms. Whether that's acceptable depends entirely on the cost of a retention offer vs. the value of a retained player.
- If a retention offer costs $2 and a lost player costs $50 in LTV, FNs are 25x more expensive than FPs — so you'd lower $\tau$ to increase recall, accepting more false alarms.