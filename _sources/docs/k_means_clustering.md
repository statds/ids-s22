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

(k_means_clustering)=

# K-means Clustering


So far, we have learned a lot of supervised learning algorithms (eg., Decision Tree, Random Forest), 
in which labelled or known outcomes are given. In contrast, unsupervised learning uses unlabeled data 
to discover patterns that help solve for clustering or association problems, and K-means clustering 
is one of the simplest and most popular unsupervised learning algorithms.

From wiki: 
> Given a set of observations $(\boldsymbol{x}_1, \boldsymbol{x}_2, \dots, \boldsymbol{x}_n)$, 
> where each observation is a d-dimensional real vector, k-means clustering aims to 
> partition the n observations into k (≤ n) sets $\boldsymbol{S} = \{S_1, S_2, \dots, S_k\}$ 
> so as to minimize the within-cluster sum of squares (WCSS) (i.e. variance). 
> Formally, the objective is to find:
> \begin{equation*}
\underset{S}{\mathrm{argmin}} 
\sum_{i=1}^{k}\sum_{\boldsymbol{x}\in S_i} \left\|\boldsymbol{x}-\boldsymbol{\mu}_i \right\|^2
\end{equation*}
> where $\boldsymbol{\mu}_i$ is the mean of points in $S_i$.

## Lloyd's algorithm

1. Initialize $\boldsymbol{\mu}_i$ randomly
1. Alternate:
    - assignment: $S_i$ <- $\underset{S}{\mathrm{argmin}}
      \sum_{i=1}^{k}\sum_{\boldsymbol{x}\in S_i} \left\|\boldsymbol{x}-\boldsymbol{\mu}_i \right\|^2$ 
      for all i
    - update: $\mu_i$ <- $\frac{1}{|S_i|}\sum_{\boldsymbol{x}_j\in S_i}{\boldsymbol{x}_j}$
1. When `iter = max_iter` or when the assignments do not change, algorithm terminates

Source: <https://en.wikipedia.org/wiki/K-means_clustering#Gaussian_mixture_model>

## Implementation

Like many other popular algorithms, K-means can also be implemented by importing the `scikit-learn` package

## NYC crash data


```{code-cell} ipython3
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
```


```{code-cell} ipython3
url = 'https://raw.githubusercontent.com/statds/ids-s22/main/notes/data/nyc_mv_collisions_202201.csv'
nyc_crash = pd.read_csv(url)
nyc_crash.head()
```

### Data Cleaning

For simplicity, we can consider "longitude" and "latitude" as the covariates, 
so it is reasonable to omit those data points without "location" values. Note that 
the "borough" variable is also concluded in the subset, but this is only for comparison, 
and we are still in the unsupervised framework.


```{code-cell} ipython3
# change data points with latitude and longitude (0,0) into (nan,nan)
nyc_crash.loc[nyc_crash["LONGITUDE"] == 0, ["LONGITUDE","LATITUDE"]] = np.nan

# create a subset
my_nyc = nyc_crash.loc[nyc_crash["LONGITUDE"].notna(), 
                       ["LONGITUDE","LATITUDE","BOROUGH"]]
my_nyc.columns = ["longitude","latitude","borough"]
my_nyc["borough"] = my_nyc["borough"].astype("category")

# make sure bad latitude values are all removed now
print(my_nyc["longitude"].isna().sum())
print(my_nyc["latitude"].isna().sum())
```

We first plot the locations by their true borough.


```{code-cell} ipython3
fig = plt.figure(figsize=(14, 14))

sns.scatterplot(x="longitude", y="latitude", data=my_nyc,
                hue = 'borough', alpha = 0.5)
```

### Training K-means

Now let's pretend we do not know the true borough of the data, 
and try to predict it with their longitude and latitude. 
Several parameters can be modified in the algorithm, see
https://scikit-learn.org/stable/modules/generated/sklearn.cluster.KMeans.html


```{code-cell} ipython3
from sklearn.cluster import KMeans

Kmean = KMeans(n_clusters = 5)
Kmean.fit(my_nyc.loc[:,["longitude","latitude"]])

```

### Results

The final predicted centroids can be obtained by `Kmean.cluster_centers_`.


```{code-cell} ipython3
print(Kmean.cluster_centers_)

fig = plt.figure()

plt.scatter(Kmean.cluster_centers_[:,0], Kmean.cluster_centers_[:,1], s=200, c=range(5), marker='s')
```

The trained labels can be obtained by `Kmean.labels_`, and now we can compare them to the true values.


```{code-cell} ipython3
my_nyc["pred_label"] = Kmean.labels_
my_nyc["pred_label"] = my_nyc["pred_label"].astype("category")

fig, axs = plt.subplots(ncols=2, nrows=1, figsize=(10, 5),
                        constrained_layout=True)

sns.scatterplot(x="longitude", y="latitude", data=my_nyc,
                hue = 'borough', alpha = 0.1, ax=axs[0])

plt.scatter(Kmean.cluster_centers_[:,0], Kmean.cluster_centers_[:,1], 
            marker = "+", c = "black", s = 200, alpha = 1)
sns.scatterplot(x="longitude", y="latitude", data=my_nyc,
                hue = 'pred_label', alpha = 0.1, ax=axs[1])
```

The algorithm seems to be good yet there are several "misclassified" data, mainly in cluster #4 ("Manhatten"). 
This is reasonable since K-means only minimizes within-cluster variances (squared Euclidean distances).

### Prediction

If we have some new data, we can use `Kmean.predict` to predict which cluster they belong to.


```{code-cell} ipython3
sample_test=np.array([[-74, 40.75], [-73.7, 40.8]])
new_data=pd.DataFrame(sample_test.reshape(2, -1), columns = ["longitude","latitude"])

Kmean.predict(new_data)
```

## Comment

K-means is a powerful unsupervised clustering algorithm that can be easily understood and implemented, 
but there are still some disadvantages. 

- The algorithm is extremly sensitive to the choice of initial cluster centroids. 
  In the above problem, we use default parameters `init='k-means++'` and `n_init = 10`
  to avoid local minimums. See 
  <https://www.analyticsvidhya.com/blog/2019/08/comprehensive-guide-k-means-clustering/#h2_9.>
  
- The number K is another important parameter in the algorithm. In our problem we just use `k=5` because 
  there are 5 boroughs in the New York city, but in pratice this can be more complicated.
  A plot of "elbow curve" can be used to determine K. Also see
  <https://www.analyticsvidhya.com/blog/2019/08/comprehensive-guide-k-means-clustering/#h2_9.>
  