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

> Support vector machines (SVM) is a supervised machine learning technique. And, even though it’s mostly used in classification, it can also be applied to regression problems.

> SVMs define a decision boundary along with a maximal margin that separates almost all the points into two classes. While also leaving some room for misclassifications.

## Install scikit-learn

To install this library, run this pip command:
`pip install scikit-learn`.


## Concepts

Consider a two-class classification problem.
Suppose that the observed data are
\begin{equation*}
\{X_i, Y_i: i = 1, \ldots, n\},
\end{equation*}
where the labels $y_i \in \{1, -1\}$, indicating different classes,
and $X_i$'s are $p$-dimensional feature vectors.
Intuitively, a good separation is achieved by the hyper-plane that has the
largest distance to the nearest training data points of any class (so-called
functional margin), since in general the larger the margin the lower the
generalization error of the classifier. In other words,
we want to find a separation hyperplane that maximizes the margin
between the two classes, where the margin is defined as the mimum distance
between the data instances and the decision boundary.


A hyperplane in the $p$-dimension space is defined by a normal vector $w$ and
scalar constant $b$
\begin{equation*}
w^\top x- b = 0.
\end{equation*}
For identification, we assumed that $w$ is normalized (so that it has unit length).
Suppose for the moment that the two classes can be perfectly separated by a
hyperplane. One could use two boundaries
\begin{align*}
 w^\top x - b & =  1,\\
 w^\top x - b & = -1.
\end{align*}
Anything above the first boundary is labeled 1, and anything below the second
boundary is labeled $-1$.
Geometrically we want to maximize the distance between the boundaries,
which is $2 / \| w\|$; see [why](https://math.stackexchange.com/q/1305925/168764).


We also want to prevent points from falling into the margin, so we add the constraints:
$w^\top x_i-b\ge1, \ Y_i=1$ and $w^\top X_i-b\le-1, \ Y_i=-1$.
They can be written as 
\begin{equation*}
Y_i (w^\top X_i - b) \ge 1, \ i = 1, \ldots, n.
\end{equation*}


The optimization for linear SVM is:
\begin{align*}
\min_{w, b} \ &\frac{1}{2} \|w\|^2  \\
\text{s.t  } \ & y_i(w^\top x_i-b)\ge 1.
\end{align*}
The loss function is (known as hard-margin):
\begin{equation*}
\frac{1}{2}\|w\|^2+ C \sum_i {[1 -Y_i(w^\top X_i - b)]}.
\end{equation*}


Recall that we have assumed that the data is linearly separable. What if it’s
not? We can relax the constrains slightly by some $\zeta_i > 0$
(known as soft-margin):
\begin{align*}
\min_{w, b, \zeta} & \frac{1}{2}\|w\|^2 + C \sum_i \zeta_i\\
\text{s.t. }
&Y_i (w^\top X_i - b) \ge 1 - \zeta_i,\\
&\zeta_i \ge 0, \ i = 1, \ldots, n.
\end{align*}

<!-- The lagrangian dual of the above is: -->
<!-- $$ -->
<!-- \max f(c_1,...,c_n)=\sum_i{c_i}-\frac{1}{2}\sum_i\sum_j {y_ic_i(x_i^Tx_j)y_jc_j}\\ -->
<!-- \text{s.t  } \sum_i{c_iy_i}=0, 0\le c_i\le1/2n\lambda -->
<!-- $$ -->

Why are the support vectors called support vectors?
See [this
blog](https://towardsdatascience.com/so-why-the-heck-are-they-called-support-vector-machines-52fc72c990a1).


## Kernel SVM

Sometimes the data cannot be separate by a hyperplane. A nonlinear classifier
can be created by applying the kernel trick. In particular, we replace $X_i$ by
a transformation of it; and replace the inner product by a Kernel defined on the
transformed space. The classification vector in the transformed space is
$$\sum{c_i Y_i \phi(X_i)}. $$


Check the different SVM methods with different decision functions [here](https://scikit-learn.org/stable/modules/svm.html#mathematical-formulation)!

### Kernel
Kernels are symmetric functions in the form $K(x,y)$.
Examples of kernels:

+ Linear kernel: $K(x,y)=x^Ty$
+ Gaussian kernel (RBF):
  $K(x,y)=e^{-\frac{||x-y||^2}{2\sigma^2}}=e^{-\gamma||x-y||^2}$
+ Laplacian kernel: $K(x,y)=e^{-\alpha||x-y||}$

In `sklearn`, we can apply kernelized SVM by changing linear kernel to RBF
kernel easily. There are two parameters in RBF kernel: $c$ and $\gamma$. They
are related to the smooth of decision surface and influence of single training
sample. A [gridsearch
cross-validation](https://scikit-learn.org/stable/modules/svm.html#kernel-functions) can be
used to decide these parameters.



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
## get support vectors
clf.support_vectors_

## get indices of support vectors
clf.support_

## get number of support vectors for each class
clf.n_support_
```

## Other Support Vector Machines

There are also other SVMs that can serve other purposes. Another main subset of
SVMs is called SVR (Support Vector Regression). It uses the same method as SVC,
however it solves regression problems instead of classification.

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

SVM allows for misclassifcation. This means that it is acceptable to have some
vectors fall inside the margin and on the wrong side of the decision
boundary. This is done so the model can do a better job at classifying most
vectors in the testing set. 


Slack Variable: Tells us if a test observation is misclassified and where the
observation is relative to the decision boundary and the margin. If the value is
0, it means the point is correctly classified. If the slack variable is greater
than zero and less than one, then the point is on the wrong side of the
margin. Lastly if the variable is greater than 1, then the point is on the wrong
side of the hyperplane.

## In Practice

```{code-cell}
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.model_selection import train_test_split 
from sklearn.svm import SVC
from sklearn.metrics import classification_report, confusion_matrix


job_app_data = pd.read_csv("../data/nyc_DobJobApp_2021.csv", dtype = "unicode")

job_app_data = job_app_data[['Doc #', 'Borough', 'Job Status']]

job_app_data['Borough'].replace(['MANHATTAN', 'BRONX', 'BROOKLYN', 'QUEENS', 'STATEN ISLAND'], [1,2,3,4,5], inplace=True)


X = job_app_data.drop('Job Status', axis=1)
y = job_app_data['Job Status']

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.20)

svclassifier = SVC(kernel='linear')
svclassifier.fit(X_train, y_train)

y_pred = svclassifier.predict(X_test)

print(confusion_matrix(y_test, y_pred))
## print(classification_report(y_test))
```

## For Additional Info

Visit:
<https://scikit-learn.org/stable/modules/svm.html#svm>;
<https://towardsdatascience.com/support-vector-machines-explained-with-python-examples-cb65e8172c85>
