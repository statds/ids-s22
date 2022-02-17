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

(patsy)=

# Describing Models with Patsy

From [`patsy` documentation](https://patsy.readthedocs.io/en/latest/overview.html):

`patsy` is a Python package for describing statistical models (especially linear models, or models that have a linear component) and building design matrices. It is closely inspired by and compatible with the formula mini-language used in R and S.

Patsy’s goal is to become the standard high-level interface to describing statistical models in Python, regardless of what particular model or library is being used underneath.

Pros of `patsy`:
+ Allows data transformations to be specified using arbitrary Python code: instead of x, we can write log(x) or (x > 0)
+ Gives a range of convenient options for coding categorical variables
+ Provides basic built-in transformations
+ Provides a language for easy-to-read specification of linear constraints
+ Features a simple API for integration into statistical packages. Specifically used in `statsmodels`, which we will be discussing further in the course.

Cons of `patsy`:
+ Will not do statistics. Simply allows you to describe models in general terms

+++

## Installing Patsy

To install `patsy`, use a simple pip command.

    pip install patsy
   
Then, you should be able to import the package. The dmatrices and dmatrix objects are the most important.

```{code-cell} ipython3
from patsy import dmatrices, dmatrix
```

## Basic example

First let's read in our NYC job data to have something to work with. We'll first take the `Initial Cost` column, which has string values in the form of "$XXXX" and turn them into integer values. Then, we'll calculate the difference in the proposed zoning square feet and the existing zoning square feet.

```{code-cell} ipython3
import pandas as pd
import numpy as np

nyc_jobs = pd.read_csv("../data/nyc_DobJobApp_2021.csv")

nyc_jobs["cost"] = [x.split("$")[1] for x in nyc_jobs["Initial Cost"]]
nyc_jobs["cost"] = [int(float(x)) for x in nyc_jobs["cost"]]

nyc_jobs["zoning_diff"] = nyc_jobs["Proposed Zoning Sqft"] - nyc_jobs["Existing Zoning Sqft"]

nyc_jobs_zoning = nyc_jobs.drop(nyc_jobs[nyc_jobs.zoning_diff <= 0].index)
nyc_jobs_zoning = nyc_jobs_zoning.drop(nyc_jobs_zoning[nyc_jobs_zoning.cost == 0].index)
```

Now we can create a new matrix with `patsy`'s `dmatrices`.

```{code-cell} ipython3
dmatrices("cost ~ zoning_diff", nyc_jobs_zoning)
```

The return value is a Python tuple containing two DesignMatrix objects. The first dmatrix is the left-hand side of the formula, and the second is the right-hand side. They're just oridinary numpy arrays with extra metadata. We can pass these to a regression function to perform some analysis.

```{code-cell} ipython3
outcome, predictors = dmatrices("cost ~ zoning_diff", nyc_jobs_zoning)
betas = np.linalg.lstsq(predictors, outcome)[0]
betas
```

The metadata is stored in an extra attribute, `.design_info`, which has valuable information.

```{code-cell} ipython3
predictors.design_info.column_names
```

## Transforming Code

You can transform code with `patsy`. For instance, you can use simple Python code.

```{code-cell} ipython3
dmatrix("np.log(zoning_diff)", nyc_jobs_zoning)
```

There are also various built-in transformation functions from `patsy`, such as `center` and `standardize`. The documentation for these and other functions are in the [`patsy.builtins` API reference](https://patsy.readthedocs.io/en/latest/builtins-reference.html#module-patsy.builtins).

```{code-cell} ipython3
dmatrix("center(zoning_diff) + standardize(zoning_diff)", nyc_jobs_zoning)
```

You can define your own transformation functions if you'd like as well.

```{code-cell} ipython3
def square(x):
    return(x*2)

dmatrix("zoning_diff + square(zoning_diff)", nyc_jobs_zoning)
```

## Special Transformations

There are other special built-in transformations that `patsy` provides which are particularly useful when dealing with difficult characters. Even though `patsy` uses a "+" in the syntax, the code still needs to be Python code. This means that something like "Existing Zoning Sqft" will produce an error. The `Q()` transformation however can fix that.

```{code-cell} ipython3
# dmatrix("Initial Cost", nyc_jobs)
# Produces error
```

```{code-cell} ipython3
dmatrix("Q('Existing Zoning Sqft')", nyc_jobs_zoning)
```

Though the `Q()` function makes this possible, it is still not recommended. The best option here would be to rename "Initial Cost" to something with better style, such as "initial_cost."

+++

Another useful transformation is `I()`. In the case where you actually want to use addition, instead of writing your ownn function, you can use `I()` to tell `patsy` you want to use addition (or subtraction in this case).

```{code-cell} ipython3
dmatrix("I(Q('Proposed Zoning Sqft') - Q('Existing Zoning Sqft'))", nyc_jobs_zoning)
```

## Categorical Data

`patsy` handles categorical data well. If you include string or boolean type data, it will automatically turn them categorical. In this instance, the T.Borough shows that the data has been transformed into treatment code. You can also add the `return_type = dataframe` to have the return value be a `pandas.dataframe` instead.

```{code-cell} ipython3
dmatrices("cost ~ Borough", nyc_jobs_zoning, return_type = "dataframe")
```

## Interaction Terms

Interaction terms are a strong reason to use `patsy`. You can easily apply interaction with `:`.

```{code-cell} ipython3
dmatrix("0 + Borough:Q('Building Type')", nyc_jobs_zoning, return_type = "dataframe")
```

With both main effects and interaction effects in the model, `patsy` goes from lower-order effects to higher-order effects. This means that each set of columns is measures the additional contribution of this effect. This removes redundancy. The short-hand is `*`. Here, `*` means the same as `x1 + x2 + x1:x2`.

```{code-cell} ipython3
dmatrix("Borough*Q('Building Type')", nyc_jobs_zoning, return_type = "dataframe")
```

You can also write interaction terms between categorical and numerical variables.

```{code-cell} ipython3
dmatrix("Borough:zoning_diff", nyc_jobs_zoning, return_type = "dataframe")
```

The code below removes some redundancy, similar to earlier. This will have one slope for the Bronx, and then each other slope is difference between the specific borough and the Bronx.

```{code-cell} ipython3
dmatrix("zoning_diff + Borough:zoning_diff", nyc_jobs_zoning, return_type = "dataframe")
```
