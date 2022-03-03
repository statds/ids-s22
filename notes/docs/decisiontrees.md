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

(trees)=

# Decision Trees

Decision trees are a predictive modeling approach that uses probability trees to either predict a continous value or predict a classification that the data fits into. 

**Pros:**

* Easy to understand and visualize
* Easy to figure out why the model is making a certain prediction
* Doesn't need as much data preparation as other prediction methods

**Cons:**

* Sometimes decision trees can get too complex and overfit the data
* Small variations in the data could cause different trees to be created which can drastically change the model's output



## Concepts

The goal of a decision tree algorithm to create a model that predicts the value
of a target variable by learning simple decision rules inferred from the data
features. A tree can be seen as a piecewise constant approximation.

### Algorithm Formulation
At a given node, find the best split that minimizes some impurity or loss
measure $H$ after the split. Let $Q_m$ be the data at node $m$ with sample size
$n_m$. Let $\theta$ be a candidate split (which may consists of a candidate
threshold for candidate feature).  Suppose that after the split, $Q_{m,l}$ is
the left node data with sample size $n_{m,l}$ and $Q_{m,r}$ is the right node
data with sample size $n_{m,r}$. The quality of the split is measured by
\begin{equation*}
G(Q_m, \theta) = \frac{n_{m, l}}{n_m} H(Q_{m, l}(\theta))
 + \frac{n_{m,r}}{n_m} H(Q_{m, r}(\theta)).
\end{equation*}
The algorithm set
\begin{equation*}
\theta^* = \arg\min_{\theta} G(Q_m, \theta).
\end{equation*}

Recursively find the best split for each child node.

+ Stopping: until a the maximum tree depth is reached or all node sample size is
  below a preset threshold.
+ Pruning: reduces the complexity of the final classifier, and hence improves
  predictive accuracy by the reduction of overfitting.


### Metrics

See `sklearn` documentation for [details](https://scikit-learn.org/stable/modules/tree.html#classification-criteria}.

+ Classification
    - Gini  $$H(Q_m) = \sum_{k=1} p_{mk} (1 - p_{mk})$$
    - Entropy $$H(Q_m) = - \sum p_{mk} \log p_{mk}$$
    - Misclassification $$H(Q_m) = 1 - \max_k p_{mk}$$

+ Regression
    - Mean squared error
    - Half Poisson deviance (for count targets)
    - Mean absolute error (slower than MSE; more robust)
	
### Confusion Matrix

See `sklearn` example for
[details](https://scikit-learn.org/stable/auto_examples/model_selection/plot_confusion_matrix.html?highlight=confusion).

See definitions on [Wiki](https://en.wikipedia.org/wiki/Confusion_matrix).

A confusion matrix is a matrix layout of the results of a classification
algorithm, where each row of the matrix represents the instances in an actual
class while each column represents the instances in a predicted class, or vice
versa.

+ True positive
+ False positive
+ True negative
+ False negative

## Simple Classification Example

```{code-cell} ipython3
## configure the inline figures to of svg format
%config InlineBackend.figure_formats = ['svg']

from sklearn.datasets import load_iris
from sklearn import tree
iris = load_iris()
X, y = iris.data, iris.target
clf = tree.DecisionTreeClassifier()
clf = clf.fit(X, y)
tree.plot_tree(clf)
```

## Data Cleaning

Import data and add a binary column for if a person was injured or not

Can't use strings in decision trees so to fix this we can change each borough to a number
EX: Bronx = 1, Brooklyn = 2

```{code-cell} ipython3
import pandas as pd
import numpy as np

nyc_collisions = pd.read_csv("../data/nyc_mv_collisions_202201.csv")

nyc_collisions["time"] = [x.split(":")[0] for x in nyc_collisions["CRASH TIME"]]
nyc_collisions["time"] = [int(x) for x in nyc_collisions["time"]]

nyc_collisions["injury_binary"] = nyc_collisions["NUMBER OF PERSONS INJURED"].map(lambda x: 1 if x>0 else 0)
nyc_collisions["num_borough"] = nyc_collisions["BOROUGH"].map(lambda x: 1 if x=="BRONX" else 0)
nyc_collisions["num_borough"] = nyc_collisions["BOROUGH"].map(lambda x: 2 if x=="BROOKLYN" else 0)
nyc_collisions["num_borough"] = nyc_collisions["BOROUGH"].map(lambda x: 3 if x=="QUEENS" else 0)
nyc_collisions["num_borough"] = nyc_collisions["BOROUGH"].map(lambda x: 4 if x=="MANHATTAN" else 0)
nyc_collisions["num_borough"] = nyc_collisions["BOROUGH"].map(lambda x: 5 if x=="STATEN ISLAND" else 0)

nyc_collisions.rename(columns={"NUMBER OF PERSONS KILLED": "num_ppl_killed"}, inplace=True)
nyc_collisions.rename(columns={"NUMBER OF PERSONS INJURED": "num_ppl_injured"}, inplace=True)



nyc_collisions['injury_binary'].value_counts()
```

## Classification Decision Tree Model
Select columns to use in our decision tree model. 

Don't want to use columns such as number of pedestrians injured because that heavily affects our target variable

We also split our dataset into a training set and a test set to avoid overfitting the data

We are trying to predict if someone was injured or not in a crash using all of the columns in `feature_cols`

```{code-cell} ipython3
from sklearn.model_selection import train_test_split # Import train_test_split function
from sklearn import tree
from sklearn import metrics
from sklearn.metrics import classification_report, confusion_matrix

feature_cols = ['time', 'num_borough', 'NUMBER OF MOTORIST KILLED','NUMBER OF CYCLIST KILLED',
               'NUMBER OF PEDESTRIANS KILLED']
x = nyc_collisions[feature_cols] # Features
y = nyc_collisions.injury_binary # Target variable

x_train, x_test, y_train, y_test = train_test_split(x, y, test_size=0.2, random_state=12) # 80% training and 20% test
```

We then set up our decision tree and use it to predict on the test set. This example is a classification
example so it will predict a 0 or 1 based on if someone was injured or not in the crash (1=injured)

We then can print out the accuracy of our model and a confusion matrix to see its predictions

```{code-cell} ipython3
clf = tree.DecisionTreeClassifier()

# Train Decision Tree Classifer
clf = clf.fit(x_train,y_train)

#Predict the response for test dataset
y_pred = clf.predict(x_test)

print("Accuracy:",metrics.accuracy_score(y_test, y_pred))

print(confusion_matrix(y_test, y_pred))
```

We can plot this model using the sklearn plotting function

```{code-cell} ipython3
tree.plot_tree(clf)
```

We can plot this same exact model using the graphviz package and we see it looks much better

```{code-cell} ipython3
import graphviz
dot_data = tree.export_graphviz(clf, out_file=None, 
                                feature_names=feature_cols,  
                                class_names=None,
                                filled=True, rounded=True,  
                      special_characters=True)

# Draw graph
graph = graphviz.Source(dot_data, format="svg") 
graph
```

We can run this exact same decision tree but try to optimize its performance by changing parameters
such as max_depth to set how many layers our tree is able to go until

```{code-cell} ipython3
from sklearn.model_selection import train_test_split # Import train_test_split function
from sklearn import tree
from sklearn import metrics
from sklearn.metrics import classification_report, confusion_matrix

feature_cols = ['time', 'num_borough', 'NUMBER OF MOTORIST KILLED','NUMBER OF CYCLIST KILLED',
               'NUMBER OF PEDESTRIANS KILLED']
x = nyc_collisions[feature_cols] # Features
y = nyc_collisions.injury_binary # Target variable

x_train, x_test, y_train, y_test = train_test_split(x, y, test_size=0.2, random_state=12) # 80% training and 20% test

clf = tree.DecisionTreeClassifier(max_depth=3)

# Train Decision Tree Classifer
clf = clf.fit(x_train,y_train)

#Predict the response for test dataset
y_pred = clf.predict(x_test)

print("Accuracy:",metrics.accuracy_score(y_test, y_pred))

print(confusion_matrix(y_test, y_pred))
```

We can see this change visually by re-running our code to graph the tree

```{code-cell} ipython3
dot_data = tree.export_graphviz(clf, out_file=None, 
                                feature_names=feature_cols,  
                                class_names=None,
                                filled=True, rounded=True,  
                      special_characters=True)

# Draw graph
graph = graphviz.Source(dot_data, format="svg") 
graph
```

## Regression Decision Tree Model

In this example instead of classying a target variable into 0 or 1 we will use a decision tree for regression

The set-up is exactly the same as classification where we select feature columns, a target variable
and split the data into a training and test data set

```{code-cell} ipython3
from sklearn.model_selection import train_test_split # Import train_test_split function
from sklearn import tree
from sklearn import metrics

feature_cols2 = ['time', 'num_borough', 'NUMBER OF MOTORIST KILLED','NUMBER OF CYCLIST KILLED',
               'NUMBER OF PEDESTRIANS KILLED']
x = nyc_collisions[feature_cols2] # Features
y = nyc_collisions.num_ppl_injured  #Target variable

x_train, x_test, y_train, y_test = train_test_split(x, y, test_size=0.2, random_state=12) # 80% training and 20% test
```

In a classification problem we can use accuracy and a confusion matrix to gauge a model's effectiveness but 
that is not the case in a regression problem. 

Here we use mean absolute error to judge our model

To show what the model is doing, we create another variable for the absolute value of the difference between
the actual and predicted values.

```{code-cell} ipython3
rgr = tree.DecisionTreeRegressor()

# Train Decision Tree Regressor
rgr = rgr.fit(x_train,y_train)

#Predict the response for test dataset
y_pred = rgr.predict(x_test)

df=pd.DataFrame({'Actual':y_test, 'Predicted':y_pred})
df["diff"] = abs(df["Actual"] - df["Predicted"])
df.sort_values(by=['diff'], inplace=True)

print('Mean Absolute Error:', metrics.mean_absolute_error(y_test, y_pred))

df.head()
```

Here we can sort by descending order to show which of our predictions were the worst.

```{code-cell} ipython3
df.sort_values(by=['diff'], ascending=False,inplace=True)
df.head()
```

Similarly to a classification problem we can still plot the resulting decision tree

```{code-cell} ipython3
dot_data = tree.export_graphviz(rgr, out_file=None, 
                                feature_names=feature_cols2,  
                                class_names=None,
                                filled=True, rounded=True,  
                      special_characters=True)

# Draw graph
graph = graphviz.Source(dot_data, format="svg") 
graph
```
