---
Created: 2024-10-24T15:33
Source: https://www.coursera.org/specializations/mathematics-for-machine-learning-and-data-science
---
# [[Random Variables]]

1. Flipping a coin once is an event. Next, a random variable is a mathematical formalization of a quantity or object which depends on random events. It has nothing to do with random or variable but using the ==**formula**== to map values to the intended outcome.  
      
    

![[image 21.png|image 21.png]]

  

1. Why random variable?

![[image 1 2.png|image 1 2.png]]

1. discrete vs continuous

![[image 2 2.png|image 2 2.png]]

  

  

# [[Distributions]]

1. ==**PMF**==: Mass function for discrete and density for continuous random variable. [[PMF]]

![[image 3 2.png|image 3 2.png]]

  

# [[Binomial Distribution]]

1. Number of ways of getting 2 heads if we flip a coin 5 times. The order doesn’t matter here.

![[image 4 2.png|image 4 2.png]]

1. As a side note, this is the formula for combination.

![[Math/images/image 5 1.png|image 5 1.png]]

1. In general the coeff is defined as below. Notice that the distribution itself isn’t just 2 events on x axis. The binomial distribution gives the discrete probability distribution of obtaining exactly successes out of Bernoulli trials (where the result of each Bernoulli trial is true with probability  and false with probability ). 

![[Math/images/image 6 1.png|image 6 1.png]]

  

1. So the generalized way of looking at this is as below.

![[Math/images/image 7 1.png|image 7 1.png]]

![[image 8 1.png|image 8 1.png]]

![[image 9 1.png|image 9 1.png]]

  

# [[Bernoulli Distribution]]

  

![[image 10 1.png|image 10 1.png]]

  

# Probability Density Function [[PDF]]

1. We saw discrete distributions up until now but we also have continuous distributions where an event doesn’t have discrete possibilities. So think of amount of rain in an area. It could be 100mm, 100.1mm, 100.0001mm. But never a discrete event. Hence p(100mm)=0

![[image 11 1.png|image 11 1.png]]

![[image 12 1.png|image 12 1.png]]

  

1. Further,

![[image 13 1.png|image 13 1.png]]

  

1. Instead of defining the probability of someone having an age of 7.05 years, you define a probability density function, which you can use to calculate the probability of someone having an age within a given range. You can't find the probability of someone being aged 7.05 years, but you can find the probability that they're aged between 7.04 and 7.06.

  

# Cumulative Distribution Function [[CDF]] 

1. A general idea for discrete variables

![[image 14 1.png|image 14 1.png]]

  

1. For continuous variables, you add the area under the curve as opposed to raw probabilities. For PDF you have to calc area but for CDF you can look at height.

![[image 15 1.png|image 15 1.png]]

  

# [[Uniform Distribution]]

1. Definition of uniform distribution

![[image 16 1.png|image 16 1.png]]

  

1. CDF

![[image 17 1.png|image 17 1.png]]

  

# [[Normal Distribution]] aka Gaussian Distribution

1. Consider a binomial distribution where we are counting the number of heads for n = 100 coin tosses. It looks like a bell curve.

![[image 18 1.png|image 18 1.png]]

1. Say we want to fit the given data to a curve. The curve below looks like a good starting point. But it’s a little off centered. We can center it by subtracting 2 which is the mean on the data in this case.

![[image 19 1.png|image 19 1.png]]

![[image 20 1.png|image 20 1.png]]

1. Further, the curve above is much slimmer than the data. We can divide the exponent by the SD (3 in this case) to get the right thickness.

![[image 21 1.png]]

1. Next, the height of the data is smaller than the curve. The area under the curve is not 1 either. So we divide the function with the area under the curve to arrive at area = 1 which also decreases the height.

![[image 22.png]]

1. A more generalized equation would be like below.

![[image 23.png]]

![[image 24.png]]

  

1. A Standard normal distribution is one with mean 0 and SD 1. We like to standardize normal distributions of different scales to a standard form so we can compare regardless of scales and use the known properties.

![[image 25.png]]

![[image 26.png]]

  

# [[Chi-Squared Distribution]]

1. The chi-squared distribution with degrees of freedom is the distribution of a sum of the squares of independent standard normal random variables. With higher degrees of freedoms, it will start approaching a normal distribution.

[https://www.youtube.com/watch?v=dXB3cUGnaxQ&t=1s](https://www.youtube.com/watch?v=dXB3cUGnaxQ&t=1s)

  

Slides from Week 1: [https://drive.google.com/file/d/1hnjGy7xLin3Hjc8sofjfe7u7bcEG1IR5/view?usp=drive_link](https://drive.google.com/file/d/1hnjGy7xLin3Hjc8sofjfe7u7bcEG1IR5/view?usp=drive_link)