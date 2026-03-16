
# Motivation to derive derivatives

- In ML you have different functions that fit the data. To find the min and max of these functions, we use [[derivatives]].
- The derivative tells you the rate of change of the function. A lot of when rate of change is 0, it’s some sort of min or max of the function.

# [[derivatives]]

1. Say we want to calc the instantaneous velocity at t=12.5 in the below example, we can take a point to the right, find the change in distance over change in time i.e. rise over run i.e. the slope and come up with an average velocity between those 2 points.
	![[1_dp1.png]]
    
2. If we keep pushing the point on the right on t=12.5 closer and closer, we will reach a point where that line is tangent to the point.
    
    
    
3. This point where the 2 points are so close that the line is tangent is called the **limit**. Now this measure of how fast the distance is changing over time is called **instantaneous rate of change** and corresponds to the **slope of the tangent.**
    ![[2_dp1.png]]
    
    **RESOURCE**: [Here’s](https://www.coursera.org/learn/machine-learning-calculus/supplement/AOCfq/approximation-of-derivatives) **a great plugin to visualize how changing the distance between the points impacts the approximation vs the real function. As you move the 2 points closer and closer, the approximation converges to the real function.**
    
    # Notations
    
    1. There are 2 notations for derivatives. They mean the same, just expressed differently.
    ![[3_dp1.png]]
    
    # **Some common derivative**
    ![[4_dp1.png]]

![[7_dp1.png]]

![[6_dp1.png]]

![[5_dp1.png]]


#PowerRule 
# Power Rule

![[8_dp1.png]]

# **Resource:**

[**Here’s](https://www.coursera.org/learn/machine-learning-calculus/ungradedWidget/fwPaZ/common-derivatives) a resource to visualize functions and their derivatives**


# Inverse Function
# Inverse function

1. An inverse function is one which undoes what the function x does

![[9_dp1.png]]

2. This is just saying that if f and g are inverse functions, the derivative of g is the inverse of the derivative of f.
![[10_dp1.png]]