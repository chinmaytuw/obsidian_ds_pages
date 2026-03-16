---
tags:
  - classic_ml
  - regression
---
# **Introduction to Regression Models**

- Below we want to predict sales by advertising in 3 mediums: TV, Radio, Newspaper

![[image.png|image.png]]

- So we have 3 features and one target:

![[image 1.png|image 1.png]]

- The ideal regression function minimizes MSE #MSE . And despite that there will be reducible and irreducible error since there’s a distribution of Y values.

![[image 2.png|image 2.png]]

- Typically not every x value will have a data point. In that case we average over the neighborhood of X.

![[image 3.png|image 3.png]]

  

### There are 2 ways of estimating f:
    
    - Parametric methods
    
    - Non parametric methods
    

  

## Parametric models

Parametric methods involve a two-step model-based approach.

1. First, <mark style="background: #D2B3FFA6;">we make an assumption about the functional form, or shape, of f</mark>. For example, one very simple assumption is that f is linear in X:  
    f(X) = β0 + β1X1 + β2X2 + ··· + βpXp

2. Once we have assumed that f is linear, the problem of estimating f is greatly simplified. Instead of having to estimate an entirely arbitrary p-dimensional function f(X), one only needs to estimate the p + 1 coefficients β0, β1,..., β

![[image 4.png|image 4.png]]

1. The potential disadvantage of a parametric approach is that the model we choose will usually not match the true unknown form of f.

1. So think linear, log regression.

2. ==**A GOTCHA**====**: <mark style="background: #ADCCFFA6;">Remember that parametric and linear doesn’t mean a straight plane. It can be a quadratic and parametric</mark>.
    
    **Are quadratic models considered linear?**
    
    Quadratic models are generally not considered linear in terms of the variable x, because they include a squared term $x^2$ which produces a curve (a parabola) rather than a straight line. For example, a quadratic function is typically of the form:
    
    $y=ax^2+bx+c$
    
    and this is distinct from a linear function, which is of the form:
    
    $y=mx+b$
    
    However, <mark style="background: #FFB86CA6;">in the context of</mark> #regression <mark style="background: #FFB86CA6;">and modeling, quadratic models can be considered linear in the coefficients (parameters) a,b,c</mark>. This is because the equation is linear with respect to these coefficients even though it is nonlinear in x. The model can be rewritten as a linear regression model by treating $x^2, x$, and the constant term as separate independent variables:
    
    $y=a⋅(x^2)+b⋅x+c⋅1$
    
    Here, the #regression is linear in parameters a,b,c, enabling the use of linear regression techniques (like least squares) to estimate these coefficients. This is why you may see the term "linear regression" applied to quadratic models in statistics and machine learning, even though the relationship between y and x is quadratic (curved) when plotted.
    

![[image 5.png|image 5.png]]

### Trade Offs

Prediction accuracy versus interpretability:  
• Linear models are easy to interpret; thin-plate splines are not.

Good fit versus over-fit or under-fit:  
• How do we determine when the fit is just right?

Parsimony versus black-box approaches:  
• We typically prefer simpler models with fewer variables rather than complex black-box predictors that use all variables.

## Non Parametric Models

- Non-parametric methods **<mark style="background: #ADCCFFA6;">do not make explicit assumptions about the functional form of f</mark>**. Instead they seek an estimate of f that gets as close to the data points as possible without being too rough or wiggly. Such approaches can have a major advantage over parametric approaches: by avoiding the assumption of a particular functional form for f, they have the potential  
    to accurately fit a wider range of possible shapes for f.

- Any parametric approach brings with it the possibility that the functional form used to estimate f is very different from the true f, in which case the resulting model will not ft the data well. In contrast, non-parametric approaches completely avoid this danger, since essentially no assumption about the form of f is made. But non-parametric approaches do suffer from a major disadvantage: since they do not reduce the problem of estimating f to a small number of parameters, <mark style="background: #ADCCFFA6;">a very large number of observations (far more than is typically needed for a parametric approach) is required</mark> in order to obtain an accurate estimate for f.

- <span class='lavender'>So think tree based models, SVM.</span>

  

# Assessing Model Accuracy

- In the left we have simulated data. It’s clear that more flexibility does not necessarily mean higher test accuracy. It can mean higher training accuracy though which is not generalizable to unseen data. The classic problem that needs to solved is how to balance over fitting and underfitting. #over_fitting #underfitting

![[image 6.png|image 6.png]]

  

## [[Bias Variance Tradeoff]]

- It is possible to show that the expected test MSE, for a given value $x_0$, can always be <mark style="background: #FFB86CA6;">decomposed into the sum of three fundamental quantities</mark>:
    
    - the variance of $\hat f(x0)$
    - the squared bias of $\hat f(x0)$
    - the variance of the error terms $\epsilon$ i.e. irreducible error.
    

==$E(y_0 − \hat f(x_0))^2 = Var(\hat f(x_0)) + [Bias(\hat f(x_0))]^2 + Var(\epsilon)$==

- Here the expected MSE on the LHS is average test MSE that we would obtain if we repeatedly estimated f using a large number of training sets.

- ==<mark style="background: #FFB86CA6;">Variance refers to the amount by which</mark> ==$\hat f$ <mark style="background: #FFB86CA6;">would change if we estimated it using a different training data set</mark>. Since the training data are used to fit the statistical learning method, different training data sets will result in a different ˆf. But ideally the estimate for f should not vary too much between training sets. However, if a method has high variance then small changes in the training data can result in large changes in $\hat f$. In general, <mark style="background: #FF5582A6;">more flexible statistical methods have higher variance</mark>.

- On the other hand, <mark style="background: #FFB86CA6;">bias refers to the error that is introduced by approximating a real-life problem, which may be extremely complicated, by a much simpler model</mark>. For example, linear regression assumes that there is a linear relationship between Y and X1, X2,...,Xp. It is unlikely that any real-life problem truly has such a simple linear relationship, and so performing linear regression will undoubtedly result in some bias in the estimate of f.

- <mark style="background: #FF5582A6;">High variance comes from excessive complexity, while high bias comes from insufficient complexity. The anchor to think about this is your data first</mark>.

- In general, as flexibility goes up, variance increases and bias decreases. Ideally both bias and variance should be low.

![[image 7.png|image 7.png]]