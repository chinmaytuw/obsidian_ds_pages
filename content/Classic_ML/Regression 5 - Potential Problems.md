
Continuation from [[Regression 4 - Some important questions]]
# 1. Potential Problems with Regression
- When we fit a linear regression model to a particular data set, many problems may occur. Most common among these are the following: 
	1. Non-linearity of the response-predictor relationships.
	2. Correlation of error terms. 
	3. Non-constant variance of error terms. 
	4. Outliers. 
	5. High-leverage points.
	6. Collinearity.

### 1.1 Non-linearity of the Data
- The linear regression model assumes that there is a straight-line relationship between the predictors and the response. If the true relationship is far from linear, then virtually all of the conclusions that we draw from the fit are suspect.
![[residual_plot.png]]

- The left panel of Figure 3.9 displays a residual plot from the linear regression of mpg onto horsepower on the Auto data set that was illustrated in Figure 3.8. The red line is a smooth fit to the residuals, which is displayed in order to make it easier to identify any trends. <mark style="background: #D2B3FFA6;">The residuals exhibit a clear U-shape, which provides a strong indication of non-linearity in the data</mark>. In contrast, the right-hand panel of Figure 3.9 displays the residual plot that results from the model (3.36), which contains a quadratic term. There appears to be little pattern in the residuals, suggesting that the quadratic term improves the fit to the data. 
- If the residual plot indicates that there are non-linear associations in the data, then a simple approach is to use non-linear transformations of the predictors, such as logX, √X, and X2, in the regression model.

### 1.2 Correlation of Error Terms
- An important <mark style="background: #FF5582A6;">assumption</mark> of the linear regression model is that the <mark style="background: #BBFABBA6;">error terms, ϵ1,ϵ2,...,ϵn, are uncorrelated</mark>. 
	- What does this mean? For instance, if the errors are uncorrelated, then the fact that <mark style="background: #ADCCFFA6;">ϵi is positive provides little or no information about the sign of ϵi+1</mark>. 
	- The standard errors that are computed for the estimated regression coefficients or the fitted values are based on the assumption of uncorrelated error terms. If in fact there is correlation among the error terms, then the<mark style="background: #ADCCFFA6;"> estimated standard errors will tend to underestimate the true standard errors</mark>. #standard_error 
	- As a result, confidence and prediction intervals will be narrower than they should be. For example, a 95% confidence interval may in reality have a much lower probability than 0.95 of containing the true value of the parameter. #confidence_interval 
	- In addition, p values associated with the model will be lower than they should be; this could cause us to erroneously conclude that a parameter is statistically significant. #p_value 
	- <mark style="background: #BBFABBA6;">In short, if the error terms are correlated, we may have an unwarranted sense of confidence in our model.</mark>
- graph:
![[auto_correlation_error_terms.png]]

## 1.3 Non-constant Variance of Error Term
- Another important assumption of the linear regression model is that the <mark style="background: #FFB86CA6;">error terms have a constant variance</mark>, $Var(ϵi)=σ^2$. The #standard_error s, #confidence_interval s, and #hypothesis_testing associated with the linear model rely upon this assumption. #regression #variance 
- - <mark style="background: #FF5582A6;">Heteroscedasticity</mark> is when the variance of the errors ( #residual ) in a regression model isn't constant across all levels of the independent variable(s). In simpler terms, the spread of data points around the regression line changes as you move along the line. #heteroscedasticity
- In an ideal regression model, you want homoscedasticity—where the residuals have roughly the same #variance everywhere. With heteroscedasticity, you might see patterns like the residuals fanning out (getting more spread out) or fanning in (getting tighter) as the predicted values increase.
	**Why it matters:**
		- While heteroscedasticity doesn't bias your coefficient estimates, it does affect their #standard_error s. This means your hypothesis tests and confidence intervals can be unreliable—you might think a predictor is statistically significant when it isn't, or vice versa.
- **Solutions:**
	Common fixes include transforming variables (like taking logs), using weighted least squares regression, or using robust standard errors that adjust for heteroscedasticity without changing the coefficient estimates themselves.

![[hetroscedasticity.png]]


## 1.4 Outliers
- An outlier is a point for which $y_i$ is far from the value predicted by the outlier model.
- A graph to help understand this:
![[outliers_lr.png]]
- In this case, removing the outlier has little effect on the least squares line: it leads to almost no change in the slope, and a miniscule reduction in the intercept. However, even if an outlier does not have much effect on the least squares fit, it can cause other problems. 
	- For instance, in this example, the RSE is 1.09 when the outlier is included in the regression, but it is only 0.77 when the outlier is removed. Since the #RSE is used to compute all confidence intervals and p-values, <mark style="background: #FFB86CA6;">such a dramatic increase caused by a single data point can have implications for the interpretation of the fit.</mark> 
	- Similarly, inclusion of the outlier causes the $R^2$ to decline from 0.892 to 0.805. #r_squared 
### 1.4.1 Treatment
- Residual plots can be used to identify outliers. In this example, the outlier is clearly visible in the residual plot illustrated in the center panel of Figure 3.12. But in practice, it can be difficult to decide how large a residual needs to be before we consider the point to be an outlier. 
- To address this problem, instead of plotting the residuals, we can plot the studentized residuals, computed by dividing each residual $e_i$ by its estimated standard studentized error. Observations whose studentized residuals are greater than 3 in absolute value are possible outliers. In the right-hand panel of Figure 3.12, the outlier’s studentized residual exceeds 6, while all other observations have studentized residuals between −2 and 2.


## 1.5 Collinearity
- Collinearity refers to the situation in which two or more predictor variables are closely related to one another. #collinear
- We see limit and age have no #correlation but limit and rating are highly correlated. The presence of collinearity can pose problems in the regression context, since it can be difficult to separate out the individual effects of collinear variables on the response. In other words, since limit and rating tend to increase or decrease together, it can be difficult to determine how each one separately is associated with the response, $balance$.
![[collinear.png]]

- Looking at 3.15 above, the contours on the right run along a narrow valley; there is a broad range of values for the coefficient estimates that result in equal values for RSS. Hence a small change in the data could cause the pair of coefficient values that yield the smallest RSS—that is, the least squares estimates—to move anywhere along this valley. This results in a great deal of uncertainty in the coefficient estimates.

### 1.5.1 Why this matters?
- Since collinearity reduces the accuracy of the estimates of the regression coefficients, it causes the standard error for ˆβj to grow. Recall that the t-statistic for each predictor is calculated by dividing ˆβj by its standard error. Consequently, collinearity results in a decline in the t-statistic. As a result, in the presence of collinearity, we may fail to reject H0 : βj = 0.<mark style="background: #FFB86CA6;"> This means that the power of the hypothesis test</mark>—the probability of correctly detecting a non-zero coefficient—<mark style="background: #FFB86CA6;">is reduced by collinearity</mark>.
- The results get messed up. look at the p value in the model 2. #p_value 
![[collinear_2.png]]

### 1.5.2 What to do?
- A simple way to detect collinearity is to look at the <mark style="background: #FFB86CA6;">correlation matrix</mark> of the predictors. An element of this matrix that is large in absolute value indicates a pair of highly correlated variables, and therefore a collinearity problem in the data.
- Unfortunately, not all collinearity problems can be detected by inspection of the correlation matrix: it is possible for collinearity to exist between three or more variables even if no pair of variables has a particularly high correlation. We call this situation multicollinearity.
- Instead of inspecting the correlation matrix, a better way to assess multi collinearity is to compute the variance inflation factor (VIF). The VIF is the ratio of the variance of ˆβj when fitting the full model divided by the variance of ˆβj if fit on its own. The smallest possible value for VIF is 1, which indicates the complete absence of collinearity. a VIF value that exceeds 5 or 10 indicates a problematic amount of collinearity.