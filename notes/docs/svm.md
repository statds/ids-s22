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
