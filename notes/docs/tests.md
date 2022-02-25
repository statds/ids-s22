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

(statmodels)=

# Hypothesis Tests

## Zero-Correlation Tests

This is the same as independence test.


Correlation tests
`scipy.stats.pearsonr()`
`scipy.stats.spearmanr()`
`scipy.stats.kendalltau()`

```{code-cell} ipython3
import scipy.stats as ss
import numpy as np
nobs = 100
x1 = np.random.random(nobs)
x2 = np.random.random(nobs)
x3 = np.random.random(nobs)
y1 = x1 + x2
y2 = x1 + x3

ss.pearsonr(y1, y2)
ss.spearmanr(x1, x2)
ss.kendalltau(y1, x2)
```

```{code-cell} ipython3
ss.pearsonr(x1, x2)
```

## Association Test for Categorical Variables

Pearson's Chi-squared test
`scipy.stats.chi2_contingency()`

Fisher's exact tests
`scipy.stats.fisher_exact()`

Let's create categorical variables by cutting continuous variables.
```{code-cell} ipython3
import pandas as pd # for pd.cut
## y's are dependent
ybins = [0, .5, 1, 1.5, 2]
labels = ['A', 'B', 'C', 'D']
y1b = pd.cut(y1, bins = ybins, labels = labels)
y2b = pd.cut(y2, bins = ybins, labels = labels)
```

We know `y1` and `y2` are dependent. Let's test it.
```{code-cell}
ytab = pd.crosstab(y1b, y2b)
chi2, p, dof, ex = ss.chi2_contingency(ytab)
print(p)
```

We know `x1` and `x2` are independent. Let's test it by dichotomizing
them.
```{code-cell}
## x's are independent
xbins = [0, 0.5, 1]
x1b = pd.cut(x1, bins = xbins, labels = ['A', 'B'])
x2b = pd.cut(x2, bins = xbins, labels = ['A', 'B'])
xtab = pd.crosstab(x1b, x2b)
chi2, p, dof, ex = ss.chi2_contingency(xtab)
```

Since this is a $2\times 2$ table, we can use Fisher's exact test.
```{code-cell}
## only for 2x2 tables
oddsratio, p = ss.fisher_exact(xtab)
print(p)
```
