---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.10.3
kernelspec:
  display_name: Python 3 (ipykernel)
  language: python
  name: python3
---

# Bagging versus Boosting

An ensemble method combines multiple individual models in order to
produce predictions with better properties. It has been reported that
an ensemble is often more accurate than any of the single member
in the ensemble {cite:ps}`opitz1999popular, buhlmann2012bagging`.


The bias-variance tradeoff is the key motivation. Each single model
(learner) may have high bias or higher variance or both. An ensemble
method seeks to reduce bias and/or variance of such weak learners by
combining several of them together in order to create a strong learner
(or ensemble model) that achieves better performances.


## Bagging

Bagging stands for bootstrap aggregation. 
In bagging, a collection of bootstrap samples of the data in a
training dataset are generated; a model (e.g., decision tree;
regression) is fit to each of the bootstrap sample independently; the
predictions of all the members in the ensemble are aggregated (e.g.,
majority vote). Bagging reduces the variance of the final prediction
through aggregation.


### Random Forest

Random forest is a bagging approach with decision trees as individual
ensemble members, except that the features used for each tree are a
random subsample of all the features. The purpose of resampling the
features it to make the trees less dependent and more robust to
missing data.

Tuning parameters for a random forest include:
1. number of trees;
1. number of features;
1. tree depth.

## Boosting

Boostting is a sequential approach where the training of a model
learner at a given step applies to "residuals" from the model fitted
at the previous steps. This way, the current step improves on data
points where the cumulative performance is not good. Boosting produces
an ensemble model that in general less biased than the weak learners
that compose it. 

Unlike bagging, boosting cannot be done in parallel.

Boosting methods differ on how they create and aggregate the weak
learners during the sequential process.

See [Joseph Rocca's
post](https://towardsdatascience.com/ensemble-methods-bagging-boosting-and-stacking-c9214a10a205);
[Jason Brownlee's
blog](https://machinelearningmastery.com/gentle-introduction-gradient-boosting-algorithm-machine-learning/).


### Adaptive Boosting (AdaBoost)
Adaptive boosting updates the weights attached to each of the training
dataset observations. The ensemble model is a weighted sum of the weak
learners. Instead of trying to solve it in one single shot (finding
all the coefficients and weak learners that give the best overall
additive model), an iterative optimisation process is
much more tractable, even if it can lead to a sub-optimal solution.


For illustration, consider a binary classification problem, with $N$
observations and a given family of weak models. To initialize, all the
observations have the same weights $1/N$. Repeat over each weak model:
+ fit the best possible weak model with the current observations
  weights;
+ compute the value of the update coefficient that is some kind of
  scalar evaluation metric of the weak learner that indicates how much
  this weak learner should be taken into account into the ensemble
  model;
+ update the strong learner by adding the new weak learner multiplied
  by its update coefficient;
+ compute new observations weights that
  expresse which observations we would like to focus on at the next
  iteration (weights of observations wrongly predicted by the
  aggregated model increase and weights of the correctly predicted
  observations decrease).

### Gradient Boosting

Gradient boosting updates the dataset in eaching step. It casts the
problem into a gradient descent one: at each iteration we fit a weak
learner to the negative gradients of the current fitting error
(pseudo-residuals) with respect to the current ensemble model. In the
regression setting, the negative gradient is the residual from the
current ensemble model.

For illustration, continue with the binary classification
problem. Initialize the pseudo-residuals as the observation outcomes.
Repeat over the following steps:
+ fit the best possible weak learner to pseudo-residuals (approximate
  the negative gradients with respect to the current strong learner);
+ compute the value of the optimal step size that defines by how much
  we update the ensemble model in the direction of the new weak
  learner;
+ update the ensemble model by adding the new weak learner multiplied
  by the step size (make a step of gradient descent);
+ compute new pseudo-residuals that indicate, for each observation, in
  which direction we would like to update next the ensemble model
  predictions.

Gradient boosting uses a gradient descent approach and can be easily
adapted to a large number of loss functions. It can be considered as a
generalization of adaboost to arbitrary differentiable loss functions.


#### Stochastic gradient boosting

Stochastic gradient boosting (SGD) is a stochastic version of gradient
boosting. At each iteration, a subsample of the training data is drawn
at random (without replacement) from the full training dataset. The
randomly selected subsample is then used, instead of the full sample,
to fit the base learner. It reduce the dependence between the trees
in the sequence in gradient boosting models.



#### Extreme gradient boosting
Extreme gradient boosting is a specific implementation of the gradient
boosting method which uses more accurate approximations to find the
best tree model. It employs a number of nifty tricks that make it
exceptionally successful, particularly with structured data. 

+ computing second-order gradients, i.e. second partial derivatives of
  the loss function (similar to Newton’s method), which provides more
  information about the direction of gradients and how to get to the
  minimum of our loss function. While regular gradient boosting uses
  the loss function of our base model (e.g. decision tree) as a proxy
  for minimizing the error of the overall model, XGBoost uses the 2nd
  order derivative as an approximation.
+ advanced regularization (L1 & L2), which improves model
  generalization.
+ parallelized.
