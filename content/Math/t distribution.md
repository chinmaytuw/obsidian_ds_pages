---
Created: 2025-04-10T16:05
---
# T Distribution Motivation

- If we assume **population** distribution is normal distribution with mean $\mu$ and SD as $\sigma$ then the **sample** distribution can also be modelled as a normal distribution but with mean \mu and SD as $\sigma$ / number of samples. This works fine for known values of$\mu$ and $\sigma$ but not for unknown.

![[image 34.png|image.png]]

- For known values, we can always standardize the distribution by the equation in the top half of the red line.

- For unknown $\sigma$ though, we can substitute $\sigma$ with S, which is an estimate. This resulting estimate will be called #t_statistic  . It won’t follow a standard normal distribution though!

![[image 1 10.png|image 1.png]]

- The t distribution looks **almost** similar to normal distribution but with heavier tails.

![[image 2 10.png|image 2.png]]

- T distribution has only ==**1 parameter i.e. degrees of freedom.**==

- As degrees of freedom or the # samples are small, the tails are heavier. As degrees of freedom approaches 30, the t distribution starts looking like a normal distribution. <span class ='orange'>That is why we like having at least 30 samples.</span>

![[image 3 10.png|image 3.png]]

![[image 4 10.png|image 4.png]]

  

# T tests

- If we didn't know the SD in the previous heights example, we can use the #t_test s to test hypothesis. Here, the t distribution is plotted with mean 0 instead of the 66.7 as earlier. Since we have 10 samples, the degrees of freedom will be 9.

![[image 5 9.png|image 5.png]]

- In this case, the value of the estimate s comes to be 3.1. When we plug in the values in the formula for t statistic, the value comes to be 1.7 which has the AUC of ~0.055. Meaning we can reject null hypothesis which is opposite of what we concluded earlier. <span class='orange'>This has to do with the uncertainty added due to not knowing the population variance.</span>

![[image 6 9.png|image 6.png]]

- For a 2 tailed test, the p value will be twice.

![[image 7 9.png|image 7.png]]

- For a left tailed test, with the below x and s values, we can reject H0.

![[image 8 9.png|image 8.png]]

  

# **Two Sample t-Test**

- Two sample hypothesis testing is going to tell us how to compare samples on different populations. Consider the below example where we compare population heights between US and Argentina.

![[image 9 9.png|image 9.png]]

- Here’s the config for a right, left and two tailed test in this scenario. We are looking for difference in means.

![[image 10 7.png|image 10.png]]

- A few assumptions. Here, the difference too will be normally distributed but the mean will be difference of population mean while the SD will be difference in sample SDs.

![[image 11 7.png|image 11.png]]

- To normalize this distribution we use the below equations. DoF are difficult to calc by hand and software packages can easily do it. It comes out to be 16.8 in this case.

![[image 12 7.png|image 12.png]]

- The t statistic comes out to be ~1.7 which results in the p value of 0.049 which means we can reject null hypo.

![[image 13 6.png|image 13.png]]