---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.13.7
kernelspec:
  display_name: Anaconda (base)
  language: python
  name: anaconda-base
---

# Random Forests

Random forests are an ensemble learning method that can be used for
classification or regression by constructing several individual 
decision trees as opposed to just one. 

For classification:

- Each decision tree in the forest gives a class prediction, and whichever class is selected most often is the output of the random forest.

For regression:

- The output of the random forest is the average prediction of the individual trees.

Advantages of Random Forests:

- Reduces overfitting that sometimes occurs in decision trees and improves accuracy
- Can handle large datasets as well as missing data
- Can perform both classification and regression tasks
- Produces good predictions that can be easily understood

Disadvantages of Random Forests:

- Requires more computational power and resources
- Consumes more time compared to a decision tree algorithm

## Installing Packages

To utilize random forests in Python, we first need to install the `scikit-learn` package.

- pip: `pip install scikit-learn`
- conda: `conda install scikit-learn`

We'll also want to install the `graphviz` package to improve the visualizations, which can be 
done using `conda install python-graphviz`.

## Simple Example

```{code-cell} ipython3
## Configure the inline figures to svg format
%config InlineBackend.figure_formats = ['svg']

from sklearn.datasets import load_wine
from sklearn.ensemble import RandomForestClassifier
from sklearn import tree
from sklearn.tree import export_graphviz
import graphviz

wine = load_wine()
X, y = wine.data, wine.target
## Build a random forest with 50 trees in it
clf = RandomForestClassifier(n_estimators = 50, random_state = 99)
clf = clf.fit(X, y)

## Select a single tree from the forest to visualize
estimator = clf.estimators_[5]
dot_data = tree.export_graphviz(estimator, out_file = None, 
                                feature_names = wine.feature_names,  
                                class_names = wine.target_names,
                                filled = True, rounded = True,  
                                proportion = False, precision = 2, 
                                special_characters=True)

# Draw graph
graph = graphviz.Source(dot_data, format = "svg") 
graph
```

## Random Forest for Classification Using NYC Crash Data

Using the NYC Motor Vehicle Crash data, we'll create a random forest model in order to try and 
predict whether a crash results in an injury and/or death to any person. Before fitting the model 
however, we first need to decide which features we'll want to include in our model. For this example, 
we'll include the following features and outcome variable to predict:

- `injured_dead_status` (outcome): indicates whether at least person was injured or killed in a crash (1 = yes, 0 = no)
- `timeframe`: the time of day in which the crash occured, split into 6-hour intervals over 24 hours
- (1 = 12AM-5:59AM, 2 = 6AM-11:59AM, 3 = 12PM-5:59PM, 4 = 6PM-11:59PM)
- `borough`: which borough the crash took place in
- `num_vehicles_involved`: number of vehicles involved in a crash
- `time_of_week`: day of week of the crash, which we'll split into weekdays and weekends

```{code-cell} ipython3
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

from sklearn.model_selection import train_test_split
from sklearn import metrics
from sklearn.metrics import classification_report, confusion_matrix

## Read in NYC crash data
nyc_crash = pd.read_csv("../data/nyc_mv_collisions_202201.csv")
```

```{code-cell} ipython3
## Create new variables relating to people injured and/or killed
## in a crash
nyc_crash['num_injured_dead'] = nyc_crash.apply(lambda row: 
                                                   row['NUMBER OF PERSONS KILLED'] +
                                                   row['NUMBER OF PERSONS INJURED'],
                                                   axis = 1)

nyc_crash['injured_dead_status'] = nyc_crash.apply(lambda row:
                                                   1 if row['num_injured_dead'] > 0
                                                   else 0, axis = 1)

nyc_crash['injured_dead_status'] = nyc_crash['injured_dead_status'].astype('category')
```

```{code-cell} ipython3
## Split crash times by hour
nyc_crash["hour"] = [x.split(":")[0] for x in nyc_crash["CRASH TIME"]]
nyc_crash["hour"] = [int(x) for x in nyc_crash["hour"]]

def timeframes(x):
    if x <= 5:
        return 1
    elif x > 5 and x <= 11:
        return 2
    elif x > 11 and x <= 17:
        return 3
    else:
        return 4

## Take crash hours and put them into specifc intervals
nyc_crash['timeframe'] = nyc_crash['hour'].apply(timeframes)
nyc_crash['timeframe'] = nyc_crash['timeframe'].astype('category')

contributing_factors = ['CONTRIBUTING FACTOR VEHICLE 1', 'CONTRIBUTING FACTOR VEHICLE 2',
                        'CONTRIBUTING FACTOR VEHICLE 3', 'CONTRIBUTING FACTOR VEHICLE 4',
                        'CONTRIBUTING FACTOR VEHICLE 5']

## Number of vehicles involved in any crash
nyc_crash['num_vehicles_involved'] = len(nyc_crash[contributing_factors].columns) - nyc_crash[
                                         contributing_factors].isnull().sum(axis = 1)
```

```{code-cell} ipython3
## Convert 'CRASH DATE' to datetime format
nyc_crash['CRASH DATE'] = nyc_crash['CRASH DATE'].astype('datetime64[ns]')

## Column to indicate day of the week
nyc_crash['day_of_week'] = nyc_crash['CRASH DATE'].dt.day_name()

def is_weekend(x):
    if x == 'Saturday' or x == 'Sunday':
        return 'weekend'
    else:
        return 'weekday'
    
## Categorize days of week to weekday or weekend  
nyc_crash['time_of_week'] = nyc_crash['day_of_week'].apply(is_weekend)
nyc_crash.head()
```

```{code-cell} ipython3
nyc_crash.rename(columns = {'BOROUGH': 'borough'}, inplace = True)
features = nyc_crash[['borough', 'timeframe', 'num_vehicles_involved', 'time_of_week']]
```

We now have all of the features we want to include in our model. Before we set up our 
random forest, we'll want to take our caterogical features and transform them into binary 
data for each category without any arbitrary ordering, a process known as one-hot encoding 
of data. This can be done very easily in pandas using the `pd.get_dummies()` function, where 
we pass in our features of interest.

```{code-cell} ipython3
labels = np.array(nyc_crash['injured_dead_status'])
features = pd.get_dummies(features)
features.head()
```
