---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.11.5
kernelspec:
  display_name: Python 3 (ipykernel)
  language: python
  name: python3
---

(models)=

# Linear and Generalized Linear Models

Let's simulate some data for illustrations.

```{code-cell} ipython3
import numpy as np
import statsmodels.api as sm

nobs = 200
ncov = 5
np.random.seed(123)
x = np.random.random((nobs, ncov)) # Uniform over [0, 1)
beta = np.repeat(1, ncov)
y = 2 + np.dot(x, beta) + np.random.normal(size = nobs)
```

```{code-cell} ipython3
y.shape
```

```{code-cell} ipython3
x.shape
```

That is, the true linear regression model is
```{math}
y = 2 + x_1 + x_2 + x_3 + x_4 + x_5 + \epsilon.
```

A regression model for the observed data can be fitted as

```{code-cell} ipython3
xmat = sm.add_constant(x)
mymod = sm.OLS(y, xmat)
myfit = mymod.fit()
myfit.summary()
```

How are the regression coefficients interpreted? Intercept?

Why does it make sense to center the covariates?

Now we form a data frame with the variables

```{code-cell} ipython3
import pandas as pd
df = np.concatenate((y.reshape((nobs, 1)), x), axis = 1)
df = pd.DataFrame(data = df,
                  columns = ["y"] + ["x" + str(i) for i in range(1,
				  ncov + 1)])
df.info()
```

Let's use a formula to specify the regression model as in R, and fit
a robust linear model (`rlm`) instead of OLS.

```{code-cell} ipython3
import statsmodels.formula.api as smf
mymod = smf.rlm(formula = "y ~ x1 + x2 + x3 + x4 + x5", data = df)
myfit = mymod.fit()
myfit.summary()
```

For model diagnostics, one can check residual plots.

```{code-cell} ipython3
import matplotlib.pyplot as plt

myOlsFit = smf.ols(formula = "y ~ x1 + x2 + x3 + x4 + x5", data = df).fit()
fig = plt.figure(figsize = (12, 8))
## residual versus x1; can do the same for other covariates
fig = sm.graphics.plot_regress_exog(myOlsFit, 'x1', fig=fig)
```

See more on [residual diagnostics and specification
tests](https://www.statsmodels.org/stable/stats.html#residual-diagnostics-and-specification-tests).


# Generalized Linear Regression

A linear regression model cannot be applied to presence/absence or
count data. 


Binary or count data need to be modeled under a generlized
framework. Consider a binary or count variable $Y$ with possible
covariates $X$.  A generalized model describes a transformation $g$
of the conditional mean $E[Y | X]$ by a linear predictor
$X^{\top}\beta$. That is
\[
g( E[Y | X] ) = X^{\top} \beta.
\]
The transformation $g$ is known as the link function.


For logistic regression with binary outcomes, the link function is
the logit function
```{math}
g(u) = \log \frac{u}{1 - u}, \quad u \in (0, 1)
```

What is the interpretation of the regression coefficients in a
logistic regression? Intercept?

A logistic regression can be fit with `statsmodels.api.glm`.

Let's generate some binary data first by dichotomizing existing variables.

```{code-cell} ipython3
df['yb' ] = np.where(df['y' ] > 2.5, 1, 0)
df['x1b'] = np.where(df['x1'] > 0.5, 1, 0)
```

Fit a logistic regression for `y1b`.

```{code-cell} ipython3
mylogistic = smf.glm(formula = 'yb ~ x1b + x2 + x3 + x4 + x5', data = df)
mylfit = mylogistic.fit()
mylfit.summary()
```

