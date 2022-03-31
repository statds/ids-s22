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

(GMM)=

# Gaussian Mixture Models

The Gaussian Mixture Models (GMM) can be viewed as an extension of K-means. Instead of 
using a hard clustering method to assign each data point to one and only one cluster, 
the Gaussian Mixture Models estimates the probability of a data point coming from each cluster.

## Mathematics

Suppose that we have $N$ observations with $D$ dimensions. 
The model is a mixture of $K$ different Gaussian distributions, each with its own
mean $\boldsymbol{\mu}_k$ and variance $\boldsymbol{\Sigma}_k$ such that within each cluster,
the probability of observing $\boldsymbol{x}_i$ is

\begin{equation*}
N( \boldsymbol{x}_i|\boldsymbol{\mu}_k, \boldsymbol{\Sigma}_k)=
\frac{1}{(2\pi)^{D/2}|\boldsymbol{\Sigma}_k|^{1/2}}
\left( 
-\frac{1}{2}(\boldsymbol{x}_i-\boldsymbol{\mu}_k)^T(\boldsymbol{\Sigma}_k)^{-1}
(\boldsymbol{x}_i-\boldsymbol{\mu}_k)
\right).
\end{equation*}

We define a latent variable $\boldsymbol{z}=(z_{1},z_{2},\dots z_{K})$, 
where $z_{k}$ is 1 if a data point of interest comes from Gaussian $k$, and 0 otherwise. 
Now the overall probability of observing a point that comes from Gaussian $k$ is

\begin{equation*}
P(z_{k}=1) = \pi_k.
\end{equation*}

Thus, each Gaussian in the model will have the following parameters: $\pi_k$, 
$\boldsymbol{\mu}_k$, $\boldsymbol{\Sigma}_k$.



## Expectation - Maximization Algorithm (EM)

1. Initialize $\theta=(\pi_k, 
\boldsymbol{\mu}_k, \boldsymbol{\Sigma}_k)$ randomly
1. Alternate:
    - E-step: based on $\theta$, estimate $\gamma(z_{ik})$, the poterior probability that 
      observation $\boldsymbol{x}_i$ comes from Gaussian $k$
    - M-step: update $\theta$ by maximizing expectation of the log-likelihood 
      based on $\gamma(z_{ik})$
1. When the algorithm converges or when `iter = max_iter`, terminate.

For more details about mathematics and the EM algorithm: 
<https://towardsdatascience.com/gaussian-mixture-models-explained-6986aaf5a95>, 
<https://towardsdatascience.com/gaussian-mixture-models-vs-k-means-which-one-to-choose-62f2736025f0>.

## Implementation

GMM can also be implemented by importing the `scikit-learn` package.

## Iris data

```{code-cell} ipython3
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
```

```{code-cell} ipython3
url = 'https://raw.githubusercontent.com/statds/ids-s22/main/notes/data/IRIS.csv'
iris = pd.read_csv(url)
iris.info()
```

```{code-cell} ipython3
sns.FacetGrid(iris,hue="species", height=3).map(sns.histplot,"petal_length").add_legend()
sns.FacetGrid(iris,hue="species", height=3).map(sns.histplot,"petal_width").add_legend()
sns.FacetGrid(iris,hue="species", height=3).map(sns.histplot,"sepal_length").add_legend()
plt.show()
```

For a more concise visualization later, we just select `sepal_length` and `sepal_width` as the input variables.

```{code-cell} ipython3
X = iris.iloc[:, [0, 1]].values
y = iris.iloc[:, 4]
```

### Training GMM

```{code-cell} ipython3
from sklearn.mixture import GaussianMixture

GMM = GaussianMixture(n_components = 3)
GMM.fit(X)
```

### Results
We can plot the Gaussians by using a `plot_gmm` function defined on 
<https://jakevdp.github.io/PythonDataScienceHandbook/05.12-gaussian-mixtures.html>.

```{code-cell} ipython3
from matplotlib.patches import Ellipse

def draw_ellipse(position, covariance, ax=None, **kwargs):
    """Draw an ellipse with a given position and covariance"""
    ax = ax or plt.gca()
    
    # Convert covariance to principal axes
    if covariance.shape == (2, 2):
        U, s, Vt = np.linalg.svd(covariance)
        angle = np.degrees(np.arctan2(U[1, 0], U[0, 0]))
        width, height = 2 * np.sqrt(s)
    else:
        angle = 0
        width, height = 2 * np.sqrt(covariance)
    
    # Draw the Ellipse
    for nsig in range(1, 4):
        ax.add_patch(Ellipse(position, nsig * width, nsig * height,
                             angle, **kwargs))
        
def plot_gmm(gmm, X, label=True, ax=None):
    ax = ax or plt.gca()
    labels = gmm.predict(X)
    if label:
        ax.scatter(X[:, 0], X[:, 1], c=labels, s=40, cmap='viridis', zorder=2)
    else:
        ax.scatter(X[:, 0], X[:, 1], s=40, zorder=2)
    ax.axis('equal')
    
    w_factor = 0.2 / gmm.weights_.max()
    for pos, covar, w in zip(gmm.means_, gmm.covariances_, gmm.weights_):
        draw_ellipse(pos, covar, alpha=w * w_factor)
```

```{code-cell} ipython3
plot_gmm(GMM, X)
```

```{code-cell} ipython3
# print the converged log-likelihood value
print(GMM.lower_bound_)
 
# print the number of iterations needed
# for the log-likelihood value to converge
print(GMM.n_iter_)
```

### Prediction

If we have some new data, we can use `GMM.predict` to predict which Gaussian they belong to.

```{code-cell} ipython3
sample_test=np.array([[5.0, 3.0],[7.0,4.0]])

GMM.predict(sample_test)
```

## Comparison to K-means

Both GMM and K-means are non-parametric clustering models, but GMM seems to be more robust 
as it introduces probabilities. However, GMM is generally slower than K-Means because 
it takes more iterations to converge. GMM can also quickly converge to a local minimum, 
not the optimal solution.

In practice, GMM can be initialized by K-Means centroids to speed up the convergence.

```{code-cell} ipython3

```
