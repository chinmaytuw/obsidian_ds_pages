---
tags:
  - classic_ml
  - regression
  - hypothesis_testing
---
continuation of [[Regression 1- Intro, Parametric-Non-Parametric ,Bias Variance]]
# Simple Linear Regression

## 1. Setup

- We are trying to solve the classic advertising problem here. So we have 3 mediums and sales. Solving it via regression boils down to:

![[image 8.png|image.png]]

- _==Meaning we are trying to solve for the equation of the line or it’s derivative in multivariable.==_

## 2. Estimating using least squares

- Let $\hat{y}_i = \hat{\beta}_0 + \hat{\beta}_1 x_i$ be the prediction for $Y$ based on the $i$th value of $X$. Then $e_i = y_i - \hat{y}_i$ represents the $i$th *residual*
- We define the *<mark style="background: #BBFABBA6;">residual sum of squares</mark>* ( #RSS ) as
$$\text{RSS} = e_1^2 + e_2^2 + \cdots + e_n^2,$$
or equivalently as
$$\text{RSS} = (y_1 - \hat{\beta}_0 - \hat{\beta}_1 x_1)^2 + (y_2 - \hat{\beta}_0 - \hat{\beta}_1 x_2)^2 + \ldots + (y_n - \hat{\beta}_0 - \hat{\beta}_1 x_n)^2.$$
- The least squares approach chooses $\hat{\beta}_0$ and $\hat{\beta}_1$ to minimize the RSS. The minimizing values can be shown to be
$$\hat{\beta}_1 = \frac{\sum_{i=1}^{n}(x_i - \bar{x})(y_i - \bar{y})}{\sum_{i=1}^{n}(x_i - \bar{x})^2},$$
$$\hat{\beta}_0 = \bar{y} - \hat{\beta}_1\bar{x},$$
where $\bar{y} \equiv \frac{1}{n}\sum_{i=1}^{n} y_i$ and $\bar{x} \equiv \frac{1}{n}\sum_{i=1}^{n} x_i$ are the sample means.

- <mark style="background: #D2B3FFA6;">In the</mark> ==$\beta1$== <mark style="background: #D2B3FFA6;">formula, you cannot negate the </mark>==$(x-\bar{x})$== <mark style="background: #D2B3FFA6;">and the</mark> $(x-\bar{x})^2$<mark style="background: #D2B3FFA6;"> as they are the in summation</mark>. Also, this more spread out x from the $\bar{x}$, the lower the $\beta1$

>[!Note]
>RSS is the objective function and OLS is the solver. So RSS tells WHAT needs to be achieved i.e. reduce the residual sum of squares and OLS is the HOW we do it.

## 3. Assessing accuracy of estimates.

- The obvious question is how do we know our estimates are correct?
- We calc #standard_error SE to measure accuracy of estimates. #RSE 

![[image 3 1.png|image 3.png]]

- <mark style="background: #ABF7F7A6;">Standard error in linear regression measures the precision of coefficient estimates - essentially, how much uncertainty there is around each estimated parameter. It tells us how much the coefficient estimates would vary if we repeated the regression analysis on different samples from the same population. Roughly speaking, the standard error tells us the average amount that this estimate ˆµ differs from the actual value of µ.</mark>

### 3.1 What Standard Error Represents

- In linear regression, we estimate coefficients (like the slope and intercept) from sample data. The standard error quantifies the sampling variability of these estimates. _==A smaller standard error indicates a more precise estimate==_, while a larger standard error suggests more uncertainty.

## 4. Worked Example

- Let’s look at a worked example. We have x as area of house and y as price. We calc the subsequent columns. $\bar{x}$ and $\bar{y}$ being means of x and y.

|   |   |   |   |   |   |
|---|---|---|---|---|---|
|area (x)|price (y)|$(x-\bar{x})$|$(y-\bar{y})$|$(x-\bar{x})^2$|$(x-\bar{x})*(y-\bar{y})$|
|1500|200,000|-750|(112,500)|562500|84,375,000|
|2000|280,000|-250|(32,500)|62500|8,125,000|
|2500|350,000|250|37,500|62500|9,375,000|
|3000|420,000|750|107,500|562500|80,625,000|
|||||1250000|182,500,000|

- $\beta1$ is defined as $\beta_1 = \frac{\sum_{i=1}^{n}(x_i - \bar{x})(y_i - \bar{y})}{\sum_{i=1}^{n}(x_i - \bar{x})^2}$. Upon substituting these values we get 146.
- $\beta0$ is defined as $\beta_0 = \bar{y} - \beta_1\bar{x}$. Upon substituting, we get $\beta0$ as -16000.
- This means the equation of our line is $\hat{y}=-16000+146*x$.
- Substituting these values, we get:

| $\hat{y} = -16000 + (146x)$ | $\text{residual } (y - \hat{y})$ | $\text{residual}^2$ |
| --------------------------- | --------------------------------- | ------------------- |
| 203,000                     | (3,000)                           | 9,000,000           |
| 276,000                     | 4,000                             | 16,000,000          |
| 349,000                     | 1,000                             | 1,000,000           |
| 422,000                     | (2,000)                           | 4,000,000           |
|                             |                                   | **30,000,000**      |

- To calc $SE(\beta1)$, The standard error of β₁ is:
    - $SE(β₁) = \hat{\sigma}/ √Σ(xᵢ - x̄)²$
    - Where $\hat{\sigma}$ is the residual standard error defined as: (4 is degrees of freedom since we have those many samples and we subtract 2 from it)
        - $\hat{\sigma}= √[30,000,000 / (4-2)] = √[30,000,000 / 2] = √15,000,000 ≈ 3873$
    - So $SE(β₁) = 3,873 / √1,250,000 = 3,873 / 1,118 ≈ 3.46$


- Also, $SE(\beta0) = \sigma \sqrt{\frac{1}{n} + \frac{\bar{x}^2}{\sum_{i=1}^{n}(x_i - \bar{x})^2}}$
    - σ is the residual standard error (estimated as $\hat{\sigma}$)
    - $SE(\beta_0) = 3873 \sqrt{\frac{1}{4} + \frac{2250^2}{1,250,000}}$
    - $SE(\beta_0) = 3873 \sqrt{0.25 + 4.05} = 3873 \sqrt{4.3} \approx 3873 \times 2.07 \approx 8,017$
    - So $SE(β₀) ≈ 8,017$, which is much larger than $SE(β₁) ≈ 3.46$. This makes sense because the intercept represents the predicted value when x = 0, which is far from our data range (1,500-3,000 sq ft), leading to higher uncertainty.

- Based on this, we can also calculate [[confidence interval]]s i.e. $\approx [\beta1-2*SE(\beta1),\beta1+2*SE(\beta1)]$. #confidence_interval
    - In our example, this would be $\approx [146-2*3.5,146+2*3.5] i.e. [139,153].$  

## 5. [[Confidence Interval]]s

- Based on the formula like before, we can calc a range for our confidence interval. The whole idea is: <mark style="background: #FFB86CA6;">if we repeat the process with multiple samples and calc ranges, 95% of the times we will have the true population value within that interval</mark>. Notice how in the below diagram, a couple of ranges do not contain the true value. The key here is repetition of the process.

![[image 4 1.png|image 4.png]]

- <mark style="background: #ABF7F7A6;">Under the classical linear regression assumptions, the coefficient estimate</mark> $\beta₁$ <mark style="background: #ABF7F7A6;">follows a</mark> [[t distribution]]:
    >- $\frac{b_1 - \beta_1}{SE(b_1)} \sim t_{n-2}$
        - b₁ is our sample estimate
        - β₁ is the true population parameter
        - SE(b₁) is the standard error
        - n-2 are the degrees of freedom

## 6. Hypothesis testing

- <mark style="background: #FFB86CA6;">Standard errors can also be used to perform hypothesis tests</mark> ( #AB_Testing) on the coefficients. The most common hypothesis test involves testing the null hypothesis of
    - H0 : There is no relationship between X and Y versus the alternative hypothesis
    - HA : There is some relationship between X and Y .

- Mathematically, this corresponds to testing
    - <mark style="background: #D2B3FFA6;">H0 : β1 = 0 versus HA : β1 != 0</mark>, meaning $\beta_1$ is 0 and has no impact in predicting target.
    - since if β1 = 0 then the model reduces to Y = β0 + , and X is not associated with Y .

- To test the #null_hypothesis , we compute a #t_statistic, given by

> $t = \frac{\hat{\beta}_1 - 0}{\text{SE}(\hat{\beta}_1)}$

- This will have a _t-_distribution with n − 2 degrees of freedom, assuming β₁ = 0.
- Using statistical software, it is easy to compute the probability of observing any value equal to |t| or larger. We call this probability the _p-value_.
- For our advertising data, t stat for TV is extremely large resulting in a very low p value.
    ![[image 5 1.png|image 5.png]]
  
## 7. Assessing the Overall Accuracy of the Model
- Slide from ISLR:
    ![[image 6 1.png|image 6.png]]

- RSE: associated with each observation is an error term ϵ. Due to the presence of these error terms, even if we knew the true regression line (i.e. even if β0 and β1 were known), we would not be able to perfectly predict Y from X. <mark style="background: #FFB86CA6;">The RSE is an estimate of the standard deviation of ϵ</mark>. Roughly speaking, it is the average amount that the response will deviate from the true regression line.
- In ISLR example, the case of the advertising data, we see from the linear regression output that the RSE is 3.26. In other words, actual sales in each market deviate from the true regression line by approximately 3,260 units, on average. Another way to think about this is that even if the model were correct and the true values of the unknown coefficients β0 and β1 were known exactly, any prediction of sales on the basis of TV advertising would still be off by about 3,260 units on average.

### 7.1 TSS, RSS,ESS,R-squared

- **TSS Is the total sum of squares.** The sum of squared differences between actual values and the mean. TSS represents the ==**total variation in the response variable before fitting any model**==. It's the ==**baseline**== against which we compare our model's performance.
    - $TSS = \sum_{i=1}^{n} (y_i - \bar{y})^2$
    
- RSS Is residual sum of squares. So the ==**difference actual and fitted values**==.
    - $RSS = \sum_{i=1}^{n} (y_i - \hat{y}i)^2 = \sum{i=1}^{n} e_i^2$
    
- RSE is Residual standard error which is measuring the ==standard deviation of the error==. Below n is the number of observations and p is the number of predictors. RSE provides a measure of the typical size of the residuals and is expressed in the same units as the response variable.
    - $RSE = \sqrt{\frac{RSS}{n-p-1}}$
    
- ESS is the sum of squared differences between ==**predicted values and the mean**== of the response variable. ESS measures the ==amount of variation== in the response variable that **is** ==**explained by the regression model**==. It quantifies **how much better the model's predictions are compared to simply using the mean of y**.
    - $ESS = \sum_{i=1}^{n} (\hat{y}_i - \bar{y})^2$
    
- Here’s a reference image to sum it up:

![[image 7 1.png|image 7.png]]

- R² ( #r_squared / Coefficient of Determination): The proportion of #variance in the response variable explained by the model.
    - $R^2 = \frac{TSS - RSS}{TSS} = 1 - \frac{RSS}{TSS} = \frac{ESS}{TSS}$
	- TSS = RSS + ESS



> [!note]
> 
> <mark style="background: #FF5582A6;">$R^2$ is quite literally the square of the correlation of the response and the variable.</mark> #correlation 
> 	For **any** linear regression (simple or multiple):
> 		**R² = Cor(Y, Ŷ)²**
> 	This is always true. R² is the square of the correlation between the actual response values and the fitted values from your model.
> 	Why this matters
> 		What's beautiful here is that your linear model is specifically constructed to **maximize** this correlation among all possible linear models. So:
> 		- You're finding the linear combination of predictors that correlates most strongly with Y
> 		- R² tells you the square of that maximum achievable correlation
> > ## The simple regression special case
> > In simple linear regression with one predictor X:
> > - Ŷ = a + bX (just a linear transformation of X)
> > - So Cor(Y, Ŷ) = Cor(Y, X) = r
> > - Therefore R² = r²
> > This is the special case where the correlation between Y and the fitted values happens to equal the correlation between Y and the single predictor.
> > ## In multiple regression
> > With predictors X₁, X₂, ..., Xₚ:
> > - Ŷ is a linear combination of all predictors
> > - Cor(Y, Ŷ) ≠ any single Cor(Y, Xᵢ)
> > - But R² still = Cor(Y, Ŷ)²
> > - There's no single "r" to square anymore

### Example for RSS
Here's a quick example with 5 points:
**Assumed values:**
- y₁ = 10, y₂ = 12, y₃ = 15, y₄ = 18, y₅ = 20
- ŷ₁ = 11, ŷ₂ = 13, ŷ₃ = 15, ŷ₄ = 17, ŷ₅ = 19 (predicted values)

**Step 1: Calculate ȳ (mean of actual values)** ȳ = (10 + 12 + 15 + 18 + 20) / 5 = 15

**Step 2: Calculate TSS (Total Sum of Squares)** TSS = Σ(yᵢ - ȳ)² TSS = (10-15)² + (12-15)² + (15-15)² + (18-15)² + (20-15)² TSS = 25 + 9 + 0 + 9 + 25 = 68

**Step 3: Calculate RSS (Residual Sum of Squares)** RSS = Σ(yᵢ - ŷᵢ)² RSS = (10-11)² + (12-13)² + (15-15)² + (18-17)² + (20-19)² RSS = 1 + 1 + 0 + 1 + 1 = 4

**Step 4: Calculate R²** R² = (TSS - RSS) / TSS = (68 - 4) / 68 = 64/68 ≈ **0.94**

This means 94% of the #variance in y is explained by the model. That's it—just sum across all points for TSS and RSS, then plug into the formula.