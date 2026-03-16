---
Created: 2025-01-31T09:08
Source: https://www.coursera.org/specializations/mathematics-for-machine-learning-and-data-science
---
# [[Marginal Distribution]]

- Marginal Distribution is where we sum over one variable while ignoring the other.

![[image 30.png|image 30.png]]

  

- So for height, it would look like this

![[image 1 6.png|image 1 6.png]]

- A complete view would be like this

![[image 2 6.png|image 2 6.png]]

  

- Another example could be looking at 2 dependent discrete events

![[image 3 6.png|image 3 6.png]]

  

  

# [[Conditional Distribution]]

- In conditional distribution, we fix the value of one variable and then calc the prob. This results in reduction of our sample size. In this example, X is fixed at 9.

![[image 4 6.png|image 4 6.png]]

- We need to normalize the numbers to get the final number

![[image 5 5.png|image 5 5.png]]

- The general formula for discrete conditional distribution looks like this.

![[image 6 5.png|image 6 5.png]]

  

- For a continuous conditional distribution, we need to take a similar slice of the data.

![[image 7 5.png|image 7 5.png]]

![[image 8 5.png|image 8 5.png]]

  

- The formula for continuous conditional distribution looks like. We just switched PMF to PDF.

![[image 9 5.png|image 9 5.png]]