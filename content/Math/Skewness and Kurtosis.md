---
Created: 2025-01-30T16:18
Source: https://www.coursera.org/specializations/mathematics-for-machine-learning-and-data-science
---
# Moments of a distribution

- Imagine a distribution like below with 3 outcomes with varying probabilities. The expected value is $E[X]$. This is called the first moment of a distribution. $E[X^2]$ is where we square the value and so on. This is the second moment and so on.

![[image 28.png|image 28.png]]

- In general, we can extend this to kth moment

![[image 1 4.png|image 1 4.png]]

# [[Skewness]]

- Imagine the following games. They are exact opposite yet look very similar.

![[image 2 4.png|image 2 4.png]]

- If we calc the expected value of both. They are the same.

![[image 3 4.png|image 3 4.png]]

  

- And so is the second moment i.e. variance

![[image 4 4.png|image 4 4.png]]

  

- If we look at the third moment though, they are opposites in their sign. Which is beginning to tell us something about the difference.

![[image 5 3.png|image 5 3.png]]

  

- ==**The standardized version of that third moment is called skewness.**==

![[image 6 3.png|image 6 3.png]]

  

# [[Kurtosis]]

- Now imagine another game as below

![[image 7 3.png|image 7 3.png]]

  

- The first moment i.e. EV is same at 0.

![[image 8 3.png|image 8 3.png]]

  

- So is the second moment

![[image 9 3.png|image 9 3.png]]

  

- And so is the third moment, skewness.

![[image 10 3.png|image 10 3.png]]

![[image 11 3.png|image 11 3.png]]

  

- The 4th moment looks like this

![[image 12 3.png|image 12 3.png]]

  

- ==**The standardized version of the 4th moment is Kurtosis and it is describing how many outliers exist in the data**==

![[image 13 3.png|image 13 3.png]]

  

- Here’s a summary:

![[image 14 3.png|image 14 3.png]]