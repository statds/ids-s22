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

(scipy)=

# SciPy for Data Science

From SciPy User Guide:

> SciPy is a collection of mathematical algorithms and convenience
> functions built on the NumPy extension of Python. It adds
> significant power to the interactive Python session by providing the
> user with high-level commands and classes for manipulating and
> visualizing data. With SciPy, an interactive Python session becomes
> a data-processing and system-prototyping environment rivaling
> systems, such as MATLAB, IDL, Octave, R-Lab, and SciLab.


> The additional benefit of basing SciPy on Python is that this also
> makes a powerful programming language available for use in
> developing sophisticated programs and specialized
> applications. Scientific applications using SciPy benefit from the
> development of additional modules in numerous niches of the software
> landscape by developers across the world. Everything from parallel
> programming to web and data-base subroutines and classes have been
> made available to the Python programmer. All of this power is
> available in addition to the mathematical libraries in SciPy.

SciPy contains the [following
modules](https://scipy.github.io/devdocs/tutorial/index.html#user-guide):


+ Special functions (`scipy.special`)
+ Integration (`scipy.integrate`)
+ Optimization (`scipy.optimize`)
+ Interpolation (`scipy.interpolate`)
+ Fourier Transforms (`scipy.fft`)
+ Signal Processing (`scipy.signal`)
+ Linear Algebra (`scipy.linalg`)
+ Sparse eigenvalue problems with `ARPACK`
+ Compressed Sparse Graph Routines (`scipy.sparse.csgraph`)
+ Spatial data structures and algorithms (`scipy.spatial`)
+ Statistics (`scipy.stats`)
+ Multidimensional image processing (`scipy.ndimage`)
+ File IO (`scipy.io`)

## Statistics

The `scipy.stats` module contains functionalities on probability
distributions. For those who are familiar with R, the density,
distribution, quantile, and sampling functions are implemented for a
large number of distributions.


For example, the generalized extreme value (GEV) distribution is
available as `scipy.stats.genextreme`. See
[Wiki](https://en.wikipedia.org/wiki/Generalized_extreme_value_distribution).

```{code-cell} ipython3
from scipy.stats import genextreme

xi = -0.2
n  = 10 
## a random sample of size n
x = genextreme.rvs(xi, size = n)
## density
genextreme.pdf(x, c = xi)
genextreme.logpdf(x, c = xi) # for loglikelihood
## distribution
genextreme.cdf(x, c = xi)
genextreme.logcdf(x, c = xi) # for loglikelihood
## quantile
genextreme.ppf(genextreme.cdf(x, c = xi), c = xi) == x
```

```{code-cell} ipython3
genextreme.logpdf(x, c = xi)
```

See
[documentation](https://scipy.github.io/devdocs/reference/stats.html#statsrefmanual)
for more.

## Optimization

The `scipy.optimize` module provides unconstrained and constrained
minimizations with the function `minimize()`.

For illustration, consider the $n$-variate [Rosenbrook function](https://en.wikipedia.org/wiki/Rosenbrock_function).

```{code-cell} ipython3
def rosen(x, a = 1, b = 100):
    """The Rosenbrock function"""
    return sum(b*(x[1:]- x[:-1]**2.0)**2.0 + (a-x[:-1])**2.0)
```

The important arguments of `minimize()` are:

+ `fun`: objective function
+ `x0`: initial gues
+ `args`: extra arguments to the objective function (and its
  derivatives)
+ `method`: type of solver, including `Nelder-Mead`, `BFGS`, etc.

Derivatives can be supplied via `jac` and `hess`.

The returned object is of type `scipy.optimize.OptimizeResult`. We
need these attributes:

+ `x`: the solution to the optimization
+ `fun`: the value of the minimized objective function (in a
  likelihood estimation problem, negative loglikelihood)
+ `hess_inv`: inverse of the Hessian matrix (in a likelihood
  estimation problem it is the variance matrix of the MLE)

```{code-cell} ipython3
import numpy as np
from scipy.optimize import minimize

x0 = np.array([1.3, 0.7]) # , 0.8, 1.9, 1.2])
res = minimize(rosen, x0,
               method='Nelder-Mead', 
               options={'xatol': 1e-8, 'disp': True})
res.x
```

The Hessian matrix is not available from the Nelder-Mead method, but
from the BFGS method. Now we use `res.x` as initial value and apply
the BFGS method to get `hess_inv`.


```{code-cell} ipython3
res2 = minimize(rosen, res.x, method='BFGS',
                options={'maxiter': 0})
res2.x
```

```{code-cell} ipython3
res2.hess_inv
```

Let's change the value of `a` and `b`.
```{code-cell} ipython3
res2 = minimize(rosen, res.x, args(3, 100),
                method='Nelder-Mead')
res2.x
```
