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
