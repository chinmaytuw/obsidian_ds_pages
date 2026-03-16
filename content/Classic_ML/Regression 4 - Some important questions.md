---
tags:
  - regression
---

Continuation from [[Regression 3- Multiple LR]]
# 5. Some Important Questions

## 5.1 Is at least one predictor useful?
- In the simple linear regression setting, in order to determine whether there is a relationship b/w the response and the predictor we can simply check whether $\beta_1=0$. 
- In the multiple regression setting with $p$ predictors, we need to ask whether all of the regression coefficients are zero, i.e. whether $\beta_1 = \beta_2 = \cdots = \beta_p = 0$. As in the simple linear regression setting, we use a hypothesis test to answer this question. We test the null hypothesis,
		$H_0 : \beta_1 = \beta_2 = \cdots = \beta_p = 0$

versus the alternative
		$H_a : \text{ at least one } \beta_j \text{ is non-zero.}$

This hypothesis test is performed by computing the #F_statistic,

| Quantity | Value |
|----------|-------|
| Residual standard error | 1.69 |
| $R^2$ | 0.897 |
| F-statistic | 570 |
Formula for F statistic is as follows:

		$F = \frac{(\text{TSS} - \text{RSS})/p}{\text{RSS}/(n - p - 1)},\tag{3.23}$

where, as with simple linear regression, $\text{TSS} = \sum(y_i - \bar{y})^2$ and $\text{RSS} = \sum(y_i - \hat{y}_i)^2$. If the linear model assumptions are correct, one can show that
		$E\{\text{RSS}/(n - p - 1)\} = \sigma^2$

and that, provided $H_0$ is true,
		$E\{(\text{TSS} - \text{RSS})/p\} = \sigma^2.$

Hence, **<mark style="background: #FFB86CA6;">when there is no relationship between the response and predictors, one would expect the F-statistic to take on a value close to 1</mark>**. On the other hand, if $H_a$ is true, then $E\{(\text{TSS} - \text{RSS})/p\} > \sigma^2$, so we expect $F$ to be greater than 1.

The F-statistic for the multiple linear regression model obtained by regressing **sales** onto **radio**, **TV**, and **newspaper** is shown in Table. In this example the F-statistic is 570. Since this is far larger than 1, it provides compelling evidence against the null hypothesis $H_0$.

#### How large does F statistic need to be?
- How large does the F-statistic need to be before we can reject H0 and conclude that there is a relationship? It turns out that the answer depends on the values of n and p.
- When n is large, an F-statistic that is just a little larger than 1 might still provide evidence against H0. In contrast, a larger F-statistic is needed to reject H0 if n is small. When H0 is true and the errors ϵi have a normal distribution, the F-statistic follows an F-distribution.

#### Given these individual p-values for each variable, why do we need to look at the overall F-statistic? 
- After all, it seems likely that if any one of the p-values for the individual variables is very small, then at least one of the predictors is related to the response. 
- However, this logic is flawed, especially when the number of predictors p is large. 
- For instance, consider an example in which p = 100 and H0 : β1 = β2 = ···= βp = 0 is true, so no variable is truly associated with the response. In this situation, about 5% of the p-values associated with each variable will be below 0.05 by chance. In other words, we expect to see approximately five small p-values even in the absence of any true association between the predictors and the response. 
- In fact, <mark style="background: #ADCCFFA6;">it is likely that we will observe at least one p-value below 0.05 by chance</mark>! Hence, if we use the individual t-statistics and associated p-values in order to decide whether or not there is any association between the variables and the response, there is a very high chance that we will incorrectly conclude that there is a relationship. 
- However, the <mark style="background: #ADCCFFA6;">F-statistic does not suffer from this problem</mark> because it adjusts for the number of predictors. Hence, if H0 is true, there is only a 5% chance that the F-statistic will result in a p value below 0.05, regardless of the number of predictors or the number of observations.

## 5.2 Deciding on Important Variables
- The first step in a multiple regression analysis is to compute the F-statistic and to examine the associated p value. If we conclude on the basis of that p-value that at least one of the predictors is related to the response, then it is natural to wonder which are the guilty ones!
- It is possible that all of the predictors are associated with the response, but it is more often the case that the response is only associated with a subset of the predictors.
- <mark style="background: #FFF3A3A6;">We can use metrics such as Mallow’s Cp, Akaike information criterion (AIC), Bayesian information criterion (BIC), adjusted</mark> $R^2$. ([[Akaike information criterion]] (AIC), [[Bayesian information criterion]] (BIC))
- This is followed by either of these 3: 
	- <mark style="background: #FFB86CA6;">Forward selection:</mark> :We begin with the null model—a model that contains an intercept but no predictors. We then fit p simple linear regressions and add to the null model the variable that results in the lowest RSS. We then add to that model the variable that results in the lowest RSS for the new two-variable model. This approach is continued until some stopping rule is satisfied. 
	- <mark style="background: #FFB86CA6;">Backward selection.</mark> :We start with all variables in the model, and remove the variable with the largest p-value—that is, the variable that is the least statistically significant. The new (p − 1)-variable model is fit, and the variable with the largest p-value is removed. This procedure continues until a stopping rule is reached. For instance, we may stop when all remaining variables have a p-value below some threshold.
	- <mark style="background: #FFB86CA6;">Mixed selection</mark>: This is a combination of forward and backward selection. We start with no variables in the model, and as with forward selection, we add the variable that provides the best fit. We continue to add variables one-by-one. Of course, as we noted with the Advertising example, the p-values for variables can become larger as new predictors are added to the model. Hence, if at any point the p-value for one of the variables in the model rises above a certain threshold, then we remove that variable from the model. We continue to perform these forward and backward steps until all variables in the model have a sufficiently low p-value, and all variables outside the model would have a large p-value if added to the model.

## 5.3 Model Fit
- $R^2$ <mark style="background: #BBFABBA6;">is the square of the correlation of the response and the variable</mark>. In multiple linear regression, it turns out that it equals $Cor(Y, ˆY)^2$, the square of the correlation between the response and the fitted linear model; in fact one property of the fitted linear model is that it maximizes this correlation among all possible linear models.
- It turns out that $R^2$ <mark style="background: #ADCCFFA6;">will always increase when more variables are added to the model</mark>, <mark style="background: #ADCCFFA6;">even if those variables are only weakly associated with the response</mark>. This is due to the fact that adding another variable always results in a decrease in the #residual sum of squares on the training data (though not necessarily the testing data). Thus, the R2 statistic, which is also computed on the training data, must increase.
- Look at RSE along with $R^2$ for understanding fit. #r_squared #standard_error #RSE 

