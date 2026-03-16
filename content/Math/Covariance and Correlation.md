---
Created: 2025-02-02T17:50
Source: https://www.coursera.org/specializations/mathematics-for-machine-learning-and-data-science
tags:
  - correlation
  - covariance
---

# [[Covariance]]

- Assume we have the below data points

![[image 31.png|image 31.png]]

  

- Assume they look like this

![[image 1 7.png|image 1 7.png]]

  

- Next, we can center the graph around the mean and divide by SD to get variance=1.

![[image 2 7.png|image 2 7.png]]

  

- We can then multiply the 2 variables and sum them up to get a relation like this

![[image 3 7.png|image 3 7.png]]

  

- ==**This brings us close the formula for covariance. It is the average of sum of products after the data has been**== ==**centered**== ==**(not standardized)**==. Below is the example for age vs height.

![[image 4 7.png|image 4 7.png]]

![[image 5 6.png|image 5 6.png]]

  

# Covariance of probability distribution - Equal probabilities

- Assume you have the three games like below. The question we would like to answer is how similar are the three games for X and Y

![[image 6 6.png|image 6 6.png]]

- We can calc the EV for X and Y using this logic

![[image 7 6.png|image 7 6.png]]

- And we can calc the Var using this

![[image 8 6.png|image 8 6.png]]

  

- So both EV and var look the same

![[image 9 6.png|image 9 6.png]]

  

- Now the covariance looks like this. The data is already centered so subtracting mean of 0 makes no difference.

![[image 10 5.png|image 10 5.png]]

  

- So the covariances for the three games is as follows. For game 1, it’s positive. So if X wins, Y wins as well. For game 2, its the opposite. For game 3, it’s 0 meaning there’s no pattern.

![[image 11 5.png|image 11 5.png]]

  

# Covariance of probability distribution - Unequal probabilities

- Now, assume game 4 with unequal probabilities. The EV here is not 0 but the weighted sum i.e. 1/6.

![[image 12 5.png|image 12 5.png]]

- The variance is like below. We have to multiply by the probabilities.

![[image 13 5.png|image 13 5.png]]

  

- Covariance when probabilities are unequal will look like this. Note the third line, it’s similar to where we look at E[XY] i.e. EV of the product.

![[image 14 5.png|image 14 5.png]]

- To summarize,

![[image 15 3.png|image 15 3.png]]

  

- For continuous distributions, COV looks like this

![[image 16 3.png|image 16 3.png]]

  

# **Covariance Matrix**

- The covariance matrix looks like this. We have variances on the diagonal running from left to right and covariances in the rest of the cells.

![[image 17 3.png|image 17 3.png]]

- For a dataset with 5 features it would look like this. The matrix is called sigma.

![[image 18 2.png|image 18 2.png]]

  

  

# [[Correlation]] Coefficient

- The covariances are centered around the mean but they can be still vastly different scales which can make it difficult to compare across variables. This is why we need something that is standardized. Correlation coefficient solves for this problem by dividing covariances with the product of [SDs.](http://SDs.It) It is always a number between -1 and 1.

![[image 19 2.png|image 19 2.png]]

![[image 20 2.png|image 20 2.png]]