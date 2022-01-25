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
title: Homework 100
author: Jun Yan
date: February, 1, 2022
---

# Problem 1: Gamma MLE Simulation Study

First, we define the negative loglikelihood function give the gamma parameters `theta` and data vector `x`.

```{code-cell} ipython3
import numpy as np
from scipy.stats import gamma
from scipy.optimize import minimize

def nllk_gamma(theta, x):
    return(- np.sum(gamma.logpdf(x, a = theta[0], scale = theta[1])))
```

Let's test it out.

```{code-cell} ipython3
n = 100
theta = [2., 2.]
x = gamma.rvs(a = theta[0], scale = theta[1], size = n)

nllk_gamma(theta, x)
```

Now let's write a function to find the MLE by minimizing the negative loglikelihood function.

```{code-cell} ipython3
def mle_gamma(x):
    ## moment estimator as initial value
    m = x.mean()
    v = x.var()
    init = np.array([m * m / v, v / m])
    ## optimize
    fit = minimize(
        nllk_gamma,
        x0  = init,
        args = (x),
        method = "BFGS")
    mle = fit.x                         # MLE
    se = np.sqrt(np.diag(fit.hess_inv)) # standard error
    return mle, se
```

Let's see if this works.

```{code-cell} ipython3
mle, se = mle_gamma(x)
```

```{code-cell} ipython3
mle
```

```{code-cell} ipython3
se
```

Now we can design a simulation study with number of replications `nrep`, true parameter vector `theta`, and sample size `n`.

```{code-cell} ipython3
def mysim(nrep, theta, n):
    mle = np.empty((nrep, len(theta)))
    se  = np.empty((nrep, len(theta)))
    for i in range(nrep):
        x = gamma.rvs(a = theta[0], scale = theta[1], size = n)
        mle[i,], se[i,] = mle_gamma(x)
    return {'mle': mle, 'se': se}
```

It works as expected:

```{code-cell} ipython3
sim = mysim(5, theta, 100)
```

```{code-cell} ipython3
sim
```

To summarize the results of a simulation study, we investigate: 1) whether the mean of the estimates match the true parameters; and 2) whether the mean of the standard errors match the empirical standard errors.

```{code-cell} ipython3
def sumsim(sim):
    avg = sim['mle'].mean(axis = 0)
    ase = sim['se'].mean(axis = 0)
    ese = sim['mle'].std(axis = 0)
    return [avg, ase, ese]
```

```{code-cell} ipython3
sumsim(sim)
```

We are ready to perform a few simulation studies and summarize the results.

```{code-cell} ipython3
nrep = 1000
sim_50 = mysim(nrep, theta, 50)
sumsim(sim_50)
```

```{code-cell} ipython3
sim_100 = mysim(nrep, theta, 100)
sumsim(sim_100)
```

```{code-cell} ipython3
sim_200 = mysim(nrep, theta, 200)
sumsim(sim_200)
```

The results suggest that the MLEs recover the true parameter values and that the standard errors of the MLEs mathch their empirical standard errors. The agreement improves as sample size increases.

__The title and author field did not show. I haven't figured this out. Some help would be appreciated.__
