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

# K Nearest Neighbor

## Algorithm

The (k) nearest neighbor algorithm is a method of predicting or classifying values 
based on the data values closest to them. In practice, this can be used to estimate
values for missing variables in a dataset, or to assign new data to existing groups.


Basic Implementation:
1. Choose an arbitrary value for k.
2. Fit the data to the regression or classification model.
3. Using the k nearest training data points, categorize or predict the value of the new data point.
4. Optimize k value from the training data and created model.


In Python, the scikit-learn package contains multiple modules to implement the k 
nearest neighbor algorithm in Python. The example below uses:

`model_selection`, `neighbors`, and `metrics`


## Classification Example

```{code-cell} ipython3
import pandas as pd

url = \
'https://raw.githubusercontent.com/statds/ids-s22/main/notes/data/nyc_DobJobApp_2021.csv'
    
dob_job = pd.read_csv(url)

dob_job.info()
```

In the below example, we will be using a subset of the DOB Job Application dataset,
for plumbing jobs that occurred in family homes.

```{code-cell} ipython3
plumbing_job = dob_job[(dob_job['Plumbing'] == 'X') & 
                       (dob_job['Building Type'] == '1-2-3 FAMILY')]

plumbing_job = plumbing_job[['Total Est. Fee', 'Borough', 'Job Type', 'Job Status', 
                    'Building Type','Latest Action Date', 'GIS_LATITUDE', 
                    'GIS_LONGITUDE']]

plumbing_job = plumbing_job.dropna()

plumbing_job.head()
```

We want to try to predict the borough based on the estimated cost of the job.
First we check for an association between variables.

```{code-cell} ipython3
import scipy.stats as ss

plumbing_job['Fee Est.'] = [float(val.strip('$')) for val in plumbing_job['Total Est. Fee']]
est_fee = pd.crosstab(plumbing_job['Fee Est.'], plumbing_job['Borough'])
est_fee

chi2, p, dof, ex = ss.chi2_contingency(est_fee)
p
```

```{code-cell} ipython3
import seaborn as sns

sns.boxplot(plumbing_job['Fee Est.'], plumbing_job['Borough'])
```

```{code-cell} ipython3
name_dict = {'BROOKLYN':0, 'QUEENS':0, 'MANHATTAN':0, 'STATEN ISLAND':0, 'BRONX':0}

for key, value in name_dict.items():
    new_value = plumbing_job.value_counts(plumbing_job['Borough'] == key)
    name_dict[key] = new_value
    
name_dict
```

Since there is a signifcant difference in the number of observations for each borough,
and much of the data appears to overlap, we will create two classifiers, weighted and 
unweighted. This will hopefully help reduce boroughs with fewer observations from being
hidden by larger boroughs.

```{code-cell} ipython3
import matplotlib.pyplot as plt
from sklearn import neighbors
from sklearn.model_selection import train_test_split

x = plumbing_job[['Fee Est.']]
y = plumbing_job['Borough']

xtrain, xtest, ytrain, ytest = train_test_split(x, y, random_state = 100)

# train_test_split = separates data into test and training subsets 

knn = neighbors.KNeighborsClassifier()
knn.fit(x, y)
print(knn.predict([[750]]))

knn_weight = neighbors.KNeighborsClassifier(weights='distance')
knn_weight.fit(x, y)
print(knn_weight.predict([[750]]))
```

```{code-cell} ipython3
from sklearn.metrics import accuracy_score

predictions1 = knn.predict(xtest)
predictions2 = knn_weight.predict(xtest)
print(accuracy_score(ytest, predictions1))
print(accuracy_score(ytest, predictions2))
```

This gives the prediction accuracy from the test data. Factoring in weights for
the data points seems to have greatly improved the accuracy of the classification.


GridSearchCV is used to find the best k value from a dictionary of potential values.
It is the alternative to calculating the validation error (RMSE) for all k.
The value of k must be chosen carefully to avoid overfitting or weakened performance.

```{code-cell} ipython3
from sklearn.model_selection import GridSearchCV
pars = {'n_neighbors':[1,2,3,4,5,6,7,8,9,10]}

model = GridSearchCV(knn, pars)
model.fit(xtrain, ytrain)
model.best_params_
```

[More about nearest neighbor](https://scikit-learn.org/stable/modules/neighbors.html)  
[More about GridSearchCV](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.GridSearchCV.html)
