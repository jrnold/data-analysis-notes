
# Collinearity and Multicollinearity


```r
library("tidyverse")
library("carData")
```

## (Perfect) collinearity

In order to estimate unique $\hat{\beta}$ OLS requires the that the columns of the design matrix $\Vec{X}$ are linearly independent.

Common examples of groups of variables that are not linearly independent:

-   Categorical variables in which there is no excluded category.
    You can also include all categories of a categorical variable if you exclude the intercept.
    Note that although they are not (often) used in political science, there are other methods of transforming categorical variables to ensure the columns in the design matrix are independent.

-   A constant variable. This can happen in practice with dichotomous
    variables of rare events; if you drop some observations for whatever
    reason, you may end up dropping all the 1's in the data. So although the
    variable is not constant in the population, in your sample it is constant
    and cannot be included in the regression.

-   A variable that is a multiple of another variable. E.g. you cannot include $\log(\text{GDP in millions USD})$ and $\log({GDP in USD})$ since $\log(\text{GDP in millions USD}) = \log({GDP in USD}) / 1,000,000$.

-   A variable that is the sum of two other variables. E.g. you cannot include $\log(population)$, $\log(GDP)$, $\log(GDP per capita)$ in a regression since
$$\log(\text{GDP per capita}) = \log(\text{GDP} / \text{population}) = \log(\text{GDP}) - \log(\text{population})$$.

## What to do about it?

R and most statistical programs will run regressions with collinear variables, but will drop variables until only linearly independent columns in $\Mat{X}$ remain.

For example, consider the following code. The variable `type` is a categorical variable with categories "bc", "wc", and "prof".


```r
data(Duncan, package = "carData")
# Create dummy variables for each category
Duncan <- mutate(Duncan,
                 bc = type == "bc",
                 wc = type == "wc",
                 prof = type == "prof")
lm(prestige ~ bc + wc + prof, data = Duncan)
```

```
## 
## Call:
## lm(formula = prestige ~ bc + wc + prof, data = Duncan)
## 
## Coefficients:
## (Intercept)       bcTRUE       wcTRUE     profTRUE  
##       80.44       -57.68       -43.78           NA
```
R runs the regression, but coefficient and standard errors for `prof` are set to `NA`.

You should not rely on the software to fix this for you; once you (or the software) notices the problem check the reasons it occurred. The rewrite your regression to remove whatever was creating linearly dependent variables in $\Mat{X}$.

## Multicollinearity

Multicollinearity is the (poor) name for less-than-perfect collinearity.
Even though there is enough variation in $\Mat{X}$ to estimate OLS coefficients, if some set of variables in $\Mat{X}$ is highly correlated it will result in large, but unbiased, standard errors on the estimates.

What happens if variables are not linearly dependent, but nevertheless highly correlated?
If $\Cor(\Vec{x}_1, vec{x}_2) = 1$, then they are linearly dependent and the regression cannot be estimated (see above).
But if $\Cor(\Vec{x}_1, vec{x}_2) = 0.99$, the OLS can estimate unique values of of $\hat\beta$. However, it everything was fine with OLS estimates until, suddenly, when there is linearly independence everything breaks. The answer is yes, and no.
As $|\Cor(\Vec{x}_1, \Vec{x}_2)| \to 1$ the standard errors on the coefficients of these variables increase, but OLS as an estimator works correctly; $\hat\beta$ and $\se{\hat\beta}$ are unbiased.
With multicollinearity, OLS gives you the "right" answer, but it cannot say much with certainty.

For a bivariate regression, the distribution of the slope coefficient has variance,
$$
\Var(\hat{\beta}_1) = \frac{\sigma_u^2}{\sum_{i = 1} (x_i - \bar{x})^2} .
$$

What affects the standard error of $\hat{\beta}$?

-   The error variance ($\sigma_u^2$). The higher the variance of the residuals, the higher the variance of the coefficients.
-   The variance of $\Vec{x}$. The lower variation in $\Mat{x}$, the bigger the standard errors of the slope.

Now consider a multiple regression,
$$
\Vec{y} = \beta_0 + \beta_1 \Vec{x}_1 + \beta_2 \Vec{x}_2 + u
$$

this becomes,
$$
\Var(\hat{\beta}_1) = \frac{\sigma_u^2}{(1 - R^2_1) \sum_{i = 1}^n (x_i - \bar{x})^2}
$$
where $R^2_1$ is the $R^2$ from the regression of $\Vec{x}_1$ on $\Vec{x}_2$,
$$
\Vec{x} = \hat{\delta}_0 + \hat{\delta}_1 \Vec{x}_2 .
$$

The factors affecting standard errors are

1.  Error variance: higher residuals leads to higher standard errors.
1.  Variance of $\Vec{x}_1$: lower variation in $\Vec{x}_2$ leads to higher standard errors.
1.  The strength of the relationship between $x_1$ and $x_2$. Stronger relationship between $x_1$ and $x_2$ (higher $R^2$ of the regression of $x_1$ on $x_2$) leads to higher standard errors.

These arguments generalize to more than two predictors.

## What do do about it?

Multicollinearity is not an "error" in the model.
All you can do is:

1.  Get more data
1.  Find more conditional variation in the predictor of interest

What it means depends on what you are doing.

1.  Prediction: then you are interested in $\hat{\Vec{y}}$ and not $\hat{\beta}}$ (or its standard errors).
    In this case, multicollinearity is irrelevant.

1.  Causal inference: in this case you are interested in $\hat{\Vec{\beta}}$.
    Multicollinearity does not bias $\hat{\beta}$.
    You should include all regressors to achieve balance, and include all relevant pre-treatment variables and not include post-treatment variables.
    Multicollinearity is not directly relevant in this choice.
    All multicollinearity means is that the variation in the treatment after accounting for selection effects is very low, making it hard to say anything about the treatment effect with that observational data.
    More sophisticated methods may trade off some bias for a lower variance (e.g. shrinkage methods), but that must be done systematically, and not ad-hoc dropping relevant pre-treatment variables that simply correlate highly with your treatment variable.
