---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.11.5
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

(ridge)=

## Ridge Regression

Ridge regression is a method of linear regression that helps prevent overfitting a 
model in the case of high correlation between variables. It is a regularization 
method - a method specifically designed to reduce overfitting a model.

While OLS regression minimizes the Residual Sum of Squares, Ridge regression is the 
Residual Sum Squares + Shrinkage Penalty: $λΣβj^2$

A larger λ means a harsher penalty and smaller coefficients, but at a certain point, 
the coefficients will become underestimated, greatly increasing bias in the model.
Following the variance-bias tradeoff, the λ chosen must be one that introduces some 
bias while minimizing the variance and MSE.

Basic Steps (for models with known multicollinearity):
1. Standardize each predictor variable
2. Fit model and choose λ (either through ridge trace plot or MSE of each λ)
3. Test model accuracy

Biggest Drawback: no variable selection = final model includes all predictors
- makes coefficients very close to 0 if not significant

Better predictions, but harder to interpret
- good for models where most/all variables significant

### In Python

The `Ridge` and `RidgeCV` () are availble through `sklearn.linear_model`

* `RidgeCV`:
- Ridge with a cross-validation option

```{code-cell} ipython3
import pandas as pd
import seaborn as sns

flowers = sns.load_dataset('iris')
flowers = flowers.drop(['species'], axis = 1)
flowers.head()
```

```{code-cell} ipython3
import matplotlib.pyplot as plt

flowers_corr = flowers.corr()
sns.heatmap(flowers_corr, annot = True)
plt.show

flowers_corr
```

```{code-cell} ipython3
from sklearn.linear_model import Ridge
from sklearn.preprocessing import StandardScaler
from sklearn.model_selection import train_test_split
```

```{code-cell} ipython3
X = flowers[['sepal_length', 'petal_width', 'petal_length']]
y = flowers['sepal_width']

scaler = StandardScaler()
Xs = scaler.fit_transform(X)

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.25, random_state = 0)

model = Ridge()

model.fit(X_train, y_train)

y_pred = model.predict(X_test)
```

```{code-cell} ipython3
from sklearn.metrics import r2_score

print(r2_score(y_test, y_pred))
```

Then we run the model again using regular OLS regression.

```{code-cell} ipython3
from statsmodels.formula.api import ols
model2 = ols('sepal_width ~ sepal_length + petal_width + petal_length', data = flowers)

fit2 = model2.fit()
fit2.summary()
```

The R-squared value is 0.524, so for this particular model, multicollinearity does not appear to have a significant effect.

+++

More on [Ridge](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.Ridge.html?highlight=ridge#sklearn.linear_model.Ridge) and [RidgeCV](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.RidgeCV.html?highlight=ridgecv#sklearn.linear_model.RidgeCV)
