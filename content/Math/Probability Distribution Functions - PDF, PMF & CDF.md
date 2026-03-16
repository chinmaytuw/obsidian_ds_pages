### Resource:
https://www.youtube.com/watch?v=C_QAURbgBqY&list=PLKnIA16_RmvbYFaaeLY28cWeqV-3vADST&index=4 
## Definition of Probability Distribution
- List of all possible values of a random variable along with their probability values.

## [[PDF]]  (Probability Distribution Function)

- a **probability distribution** is the mathematical function that gives the probabilities of occurrence of possible **outcomes** for an experiment.
- Problems like die roll, coin toss are relatively easy to solve. You know the outcomes will be a fixed set of values. Moreover they are discrete. For a continuous value, it could get very tricky to calculate the exact value and it's probability. 
- Say we want to measure the probability that a person's height is 173.67934 cm. That's a very specific number and we might not have THAT exact number. So we need some sort of distribution that would help us approximate the probability. A "function" is usually a helpful way to think about it. Given an x, what is f(x). This is the basic motivation for a PDF.
- There are different types of commonly occurring probability distributions functions. 
![[PDFs.png]]- Each of these distributions is governed by different types of parameters. So for example, normal distribution has the parameters $\mu$ and $\sigma$ . The mean and the variance. The determine the shape and characteristics of the distribution. Think of them as tuning knobs.

# Probability Density Function and Probability Mass function
![[PDF_PMF.png]]- Note that for PMF, the y axis is the actual probability. For PDF though, y axis is ==**probability density==**.  
- The reason is the same argument for height above. If we want to measure the probability that a person's height is 173.67934 it's very difficult because we might not have any sample of that exact height. There are basically infinite values on the x axis if the variable is continuous. 

# Density Estimation
- The question then becomes how to measure probability density?
![[density_estimation.JPG]]


## Kernel Density Estimate
https://en.wikipedia.org/wiki/Kernel_density_estimation

![[KDE.png]]


# Probability Density to Probablity
Probability density becomes actual probability when you multiply it by a width:
**Probability ≈ density × width**
More precisely: P(a < X < b) = ∫[a to b] f(x)dx
Think of it geometrically: the PDF f(x) gives you a height, and probability is the _area_ under that curve. To get area, you need height × width.
Claude chat [link](https://claude.ai/share/11c3a0d6-e4a5-47c3-8e9f-d65793934d08).