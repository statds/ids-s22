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

## Support Vector Machines

>Support vector machines (SVM) is a supervised machine learning technique. And, even though it’s mostly used in classification, it can also be applied to regression problems.

>SVMs define a decision boundary along with a maximal margin that separates almost all the points into two classes. While also leaving some room for misclassifications.

## Install scikit-learn

to install this library, run this pip command:

>pip install scikit-learn

## Advantages and Disadvantages

The advantages of support vector machines are:
- Effective in high dimensional spaces.
- Still effective in cases where number of dimensions is greater than the number of samples.
- Uses a subset of training points in the decision function (called support vectors), so it is also memory efficient.
- Versatile: different Kernel functions can be specified for the decision function. Common kernels are provided, but it is also possible to specify custom kernels.

The disadvantages of support vector machines include:
- If the number of features is much greater than the number of samples, avoid over-fitting in choosing Kernel functions and regularization term is crucial.
- SVMs do not directly provide probability estimates, these are calculated using an expensive five-fold cross-validation (see Scores and probabilities, below).

Source: https://scikit-learn.org/stable/modules/svm.html#svm

## Exectution within python

To start off, it is helpful to just dive into the code to show prediction power and then dive into the algorthims that make it possible.

```{code-cell}
#import the package

from sklearn import svm

#set up some data points
x = [[0,0], [1,1]]
y = [0,1]
clf = svm.SVC()

#fit the model

clf.fit(x,y)

#now predict on some new values

clf.predict([[2., 2.]])
```

## Support Vectors

Support vectors are data points that are closer to the hyperplane and influence the position and orientation of the hyperplane. Using these support vectors, we maximize the margin of the classifier. Deleting the support vectors will change the position of the hyperplane.

```{code-cell}
#get support vectors
clf.support_vectors_

#get indices of support vectors
clf.support_

#get number of support vectors for each class
clf.n_support_
```

## Other Support Vector Machines

There are also other SVMs that can serve other purposes. Another main subset of SVMs is called SVR(Support Vector Regression). It uses the same method as SVC, however it solves regression problems instead of classification.

```{code-cell}
from sklearn import svm
X = [[0, 0], [2, 2]]
y = [0.5, 2.5]
regr = svm.SVR()
regr.fit(X, y)

regr.predict([[1, 1]])
```
>>> In this case the fit method will still take in argument vectors X and y, but now y will be expected to have floating point values instead of integer values.

## Misclassification

SVM allows for misclassifcation. This means that it is acceptable to have some vectors fall inside the margin and on the wrong side of the decision boundary. This is done so the model can do a better job at classifying most vectors in the testing set. 

Slack Variable: Tells us if a test observation is misclassified and where the observation is relative to the decision boundary and the margin. If the value is 0, it means the point is correctly classified. If the slack variable is greater than zero and less than one, then the point is on the wrong side of the margin. Lastly if the variable is greater than 1, then the point is on the wrong side of the hyperplane.

## In Practice

```{code-cell}
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.model_selection import train_test_split 
from sklearn.svm import SVC
from sklearn.metrics import classification_report, confusion_matrix


job_app_data = pd.read_csv("nyc_DobJobApp_2021.csv")

job_app_data = job_app_data[['Doc #', 'Borough', 'Job Status']]

job_app_data['Borough'].replace(['MANHATTAN', 'BRONX', 'BROOKLYN', 'QUEENS', 'STATEN ISLAND'], [1,2,3,4,5], inplace=True)


X = job_app_data.drop('Job Status', axis=1)
y = job_app_data['Job Status']

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.20)

svclassifier = SVC(kernel='linear')
svclassifier.fit(X_train, y_train)

y_pred = svclassifier.predict(X_test)

print(confusion_matrix(y_test, y_pred))
print(classification_report(y_test))

```

## For Additional Info

Visit:
https://scikit-learn.org/stable/modules/svm.html#svm
https://towardsdatascience.com/support-vector-machines-explained-with-python-examples-cb65e8172c85
