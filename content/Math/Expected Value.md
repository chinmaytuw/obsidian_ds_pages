---
Created: 2025-01-29T16:11
Source: https://www.coursera.org/specializations/mathematics-for-machine-learning-and-data-science
---
# [[Expected value]]

- Think of it as the mean. An example:

![[image 27.png|image 27.png]]

- In case you flip 3 coins and get paid $1 for each heads, the EV would be:

![[image 1 3.png|image 1 3.png]]

- A more formal notation in case of discrete random variables:

![[image 2 3.png|image 2 3.png]]

  

- For continuous random variables:

![[image 3 3.png|image 3 3.png]]

  

## [[Expected Value]] of a function

- Here’s the expected value of a function. In the first equation, you just multiply the value with the probability. In case of a function, you multiply the output of the function to the probability.

![[image 4 3.png|image 4 3.png]]

  

- Imagine a scenario where we are getting paid the square of number we get of die roll. What is the expected value here?

![[image 5 2.png|image 5 2.png]]

- We just have to calc the mean. The LHS is how we would typically do it. But if you think about it, on the RHS, we have another representation. Here, we just take the square of the value and divide by 6 which is the probability (1/6). So in a way we are calculating the value of $E[x^2]$.

![[image 6 2.png|image 6 2.png]]

- Now, imagine if the payout was twice the amount of the roll. And the entry fee is $5. What is the expected value now?

![[image 7 2.png|image 7 2.png]]

- The expected value here would be:

![[image 8 2.png|image 8 2.png]]

- One way to think about this is as below.

![[image 9 2.png|image 9 2.png]]

- This can be restructured as below. This also results into some fundamental derivations.

![[image 10 2.png|image 10 2.png]]

  

## Sum of expectations

- Now imagine the below game.

![[image 11 2.png|image 11 2.png]]

- Here, we will have to sum up expectations.

![[image 12 2.png|image 12 2.png]]

  

- Further, imagine a game where we have 3 names and want to randomly assign it to the people. what is the EV we will get it right?

![[image 13 2.png|image 13 2.png]]

- As shown above, the EV is 1.
- In general, even if we expand the above concept to all the people if the world, the EV remains 1. This also lets us derive a fundamental concept.

![[image 14 2.png|image 14 2.png]]