---
tags:
  - classic_ml
  - regression
---
continuation of [[Regression 2- Simple LR]]
# 1. MLR - Objective

- For MLR, the model becomes:
    - ==$Y = \beta_0 + \beta_1 X_1 + \beta_2 X_2 + \cdots + \beta_p X_p + \epsilon$==
    
- We interpret βⱼ as the <mark style="background: #FFF3A3A6;">average effect on Y of a one unit increase in Xⱼ, holding all other predictors fixed</mark>. In the advertising example, the model becomes
    - ==$sales = β₀ + β₁ × TV + β₂ × radio + β₃ × newspaper + ε$==
- <mark style="background: #FFB86CA6;">We need to calculate the F statistic and examine associated p values for this.</mark>
# **2. Interpreting regression coefficients**

- The **ideal** scenario is when the <mark style="background: #ADCCFFA6;">predictors are uncorrelated</mark> — a _balanced design_:
    
    - Each coefficient can be estimated and tested separately.
    - Interpretations such as "_a unit change in Xⱼ is associated with a βⱼ change in Y, while all the other variables stay fixed_", are possible.

- <mark style="background: #FFF3A3A6;">Correlations amongst predictors cause problems around multicollinearity</mark>. [[Correlation]]
    - <mark style="background: #D2B3FFA6;">Coefficient Instability</mark>: The regression coefficients become unreliable and can change dramatically with small changes in the data. Coefficients may even have unexpected signs (positive when you'd expect negative, or vice versa).
    - <mark style="background: #D2B3FFA6;">Inflated Standard Errors</mark>: The standard errors of the coefficients increase substantially, making it harder to detect statistically significant relationships even when they exist. This reduces the power of your statistical tests.
    - <mark style="background: #D2B3FFA6;">Interpretation Problems</mark> :Individual coefficients become difficult to interpret meaningfully. When predictors are highly correlated, it's hard to separate their individual effects on the outcome variable.
    

- **Detection and Solutions**
    
    - You can detect multicollinearity using #variance inflation factors (VIF > 10 suggests problems) or condition indices. Common solutions include removing redundant variables, combining correlated predictors into composite scores, using ridge regression or other regularization methods, or employing principal components regression.
    - The severity depends on the degree of correlation - moderate correlation (r = 0.3-0.7) may be manageable, while high correlation (r > 0.8) typically requires intervention.
  

# **3. Estimation and Prediction for Multiple Regression**

- Given estimates β̂₀, β̂₁, . . . , β̂ₚ, we can make predictions using the formula:
    - ==$\hat{y} = \hat{\beta}_0 + \hat{\beta}_1 x_1 + \hat{\beta}_2 x_2 + \cdots + \hat{\beta}_p x_p$==

- We estimate β₀, β₁, . . . , βₚ as the values that minimize the sum of squared residuals
    - ==$RSS = \sum_{i=1}^{n} (y_i - \hat{y}_i)^2$==
        ==$= \sum_{i=1}^{n} (y_i - \hat{\beta}_0 - \hat{\beta}1 x{i1} - \hat{\beta}2 x{i2} - \cdots - \hat{\beta}p x{ip})^2$==
        

- This is done using standard statistical software. The values β̂₀, β̂₁, . . . , β̂ₚ that minimize RSS are the multiple least squares regression coefficient estimates.

# 4. **Extensions of the Linear Model**

## 4.1 Interactions

- Removing the additive assumption: _**interactions**_ **and** _**nonlinearity**_

_**Interactions:**_
- In our previous analysis of the Advertising data, we assumed that the effect on sales of increasing one advertising medium is independent of the amount spent on the other media.

- For example, the linear model:
    - ==$sales = β₀ + β₁ × TV + β₂ × radio + β₃ × newspaper$==
    
- This states that <mark style="background: #FFB86CA6;">the average effect on sales of a one-unit increase in TV is always β₁, regardless of the amount spent on radio</mark>.
- But suppose that spending money on radio advertising actually increases the effectiveness of TV advertising, so that the slope term for TV should increase as radio increases.
- In this situation, given a fixed budget of $100,000, spending half on radio and half on TV may increase sales more than allocating the entire amount to either TV or to radio.
- In marketing, this is known as a _synergy_ _effect_, and in statistics it is referred to as an _interaction_ effect.

### **4.1.1. Modelling interactions — Advertising data**

- **Model takes the form:**
    - ==$sales = β₀ + β₁ × TV + β₂ × radio + β₃ × (radio × TV) + ε$==
	    ==$= β₀ + (β₁ + β₃ × radio) × TV + β₂ × radio + ε$==
    
- **Results:**

|Variable|Coefficient|Std. Error|t-statistic|p-value|
|---|---|---|---|---|
|Intercept|6.7502|0.248|27.23|< 0.0001|
|TV|0.0191|0.002|12.70|< 0.0001|
|radio|0.0289|0.009|3.24|0.0014|
|TV×radio|0.0011|0.000|20.73|< 0.0001|

### 4.1.2 Interpretation
- The results in this table suggests that interactions are important.
- The #p_value for the interaction term TV×radio is extremely low, indicating that there is strong evidence for H_A : β₃ ≠ 0.
- The #r_squared for the interaction model is 96.8%, compared to only 89.7% for the model that predicts sales using TV and radio without an interaction term.
- This means that (96.8 − 89.7)/(100 − 89.7) = 69% of the variability in sales that remains after fitting the additive model has been explained by the interaction term.
- The coefficient estimates in the table suggest that an increase in TV advertising of $1,000 is associated with increased sales of (β̂₁ + β̂₃ × radio) × 1000 = 19 + 1.1 × radio units.
- An increase in radio advertising of $1,000 will be associated with an increase in sales of (β̂₂ + β̂₃ × TV) × 1000 = 29 + 1.1 × TV units.


## 4.2 Non Linear effects - Polynomial regression

- Below we have a linear fit and a degree 2 and 5 fit as well. The degree 2 fit looks better.
![[Classic ML/Images/image 9.png|image.png]]

- The figure suggests that
    - ==$mpg = β₀ + β₁ × horsepower + β₂ × horsepower² + ε$==

- This may provide a better fit.

| Variable    | Coefficient | Std. Error | t-statistic | p-value  |
| ----------- | ----------- | ---------- | ----------- | -------- |
| Intercept   | 56.9001     | 1.8004     | 31.6        | < 0.0001 |
| horsepower  | -0.4662     | 0.0311     | -15.0       | < 0.0001 |
| horsepower² | 0.0012      | 0.0001     | 10.1        | < 0.0001 |

- This shows a polynomial regression model where the relationship between mpg and horsepower is nonlinear. The negative coefficient for horsepower and positive coefficient for horsepower² suggests a U-shaped or curved relationship, where mpg initially decreases with horsepower but the rate of decrease slows down (or potentially reverses) at higher horsepower values. All coefficients are highly significant with p-values < 0.0001.\


