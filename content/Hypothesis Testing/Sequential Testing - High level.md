---
Source: https://claude.ai/share/838564e6-ab94-433f-bc22-aed722e9798f
tags:
  - AB_testing
---
#Sequential_Testing 
# Peeking
**<span class ='orange'>What's Peeking?</span>**
- Looking at significance tests in #AB_testing  with the <span class='lavender'>intention of stopping </span>the experiment if you see p < 0.05
- Making go/no-go decisions based on these interim looks
- Repeatedly checking "is it significant yet?" and acting on that
- <span class='gold'>This is a key distinction because typically, we expect the test to run for a fixed amount of time and then assess as opposed to stopping the test when the p value suits us. "Peeking" means looking at your test results and checking for statistical significance before your planned experiment end date.</span>
## Concrete Example

Let's say you're testing two website checkout button colors:

**Traditional Fixed-Horizon Approach:**

- You calculate you need 10,000 users per variant (20,000 total)
- You run the test for 2 weeks until you hit that sample size
- You analyze the data once at the end
- You make a decision based on that single analysis

**Peeking (the problematic behavior):**

- You plan to run until 10,000 users per variant
- But you check your dashboard after day 1: "Is it significant yet?" (p = 0.08)
- Day 3: "How about now?" (p = 0.04 - significant!)
- You stop the test and declare a winner

## Why This Is a Problem?

Each time you check for significance, you give yourself another chance to find a false positive. If you peek 10 times during an experiment, you're no longer running a 5% significance test - your actual false positive rate might be 20-30%.

It's like flipping a coin repeatedly until you get heads, then claiming "heads was destined to come up on this specific flip." The more chances you give yourself, the more likely you are to see a spurious "significant" result just by random chance.

<span class='lavender'>Even when there's no real effect, your p-value fluctuates randomly due to noise in the data. Early in the experiment, with small sample sizes, it bounces around more wildly.</span>

Think of it like this: you're giving yourself 5 chances to roll a "false alarm" instead of just 1 chance. Each peek is an independent opportunity to get unlucky and see a spurious significant result.

## What Sequential Testing Does

Sequential methods explicitly account for multiple looks. They adjust the significance thresholds (or use other decision rules) so that peeking becomes statistically valid. For example, with O'Brien-Fleming boundaries, your first peek might require p < 0.001, your second p < 0.01, and only your final analysis uses p < 0.05.

## It's a Family of Methods

Sequential testing encompasses several different approaches:

1. **Sequential Probability Ratio Test (SPRT)** - The foundational method developed by Abraham Wald in the 1940s. It tests simple hypotheses by comparing likelihood ratios as each observation arrives.

2. **Group Sequential Testing** - Analyzes data at predetermined intervals (e.g., weekly) rather than continuously. Uses adjusted significance thresholds at each "look" to control overall error rates. Common boundary approaches include Pocock, O'Brien-Fleming, and Haybittle-Peto boundaries.

3. **Always-Valid Inference** - Modern methods like mSPRT (mixture SPRT) and confidence sequences that maintain valid Type I error control no matter when or how often you look at the data. These are particularly popular in tech companies for continuous monitoring.

4. **Bayesian Sequential Methods** - Use posterior probabilities to make stopping decisions, often with expected loss calculations.

## Common Thread

What unites these methods is the need to account for **multiple testing** - if you peek at data multiple times, you inflate your false positive rate unless you adjust your decision rules accordingly. Each method handles this challenge differently, but all aim to let you stop experiments early when appropriate while maintaining statistical validity.

Sequential methods are particularly valuable when running costs are high, ethical considerations favor early stopping, or you want the flexibility to adapt quickly to clear results.