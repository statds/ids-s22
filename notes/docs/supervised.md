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

(supervised)=

# Supervised Learning

Supervised learning uses labeled datasets to train algorithms that to classify
data or predict outcomes accurately. As input data is fed into the model, it
adjusts its weights until the model has been fitted appropriately, which occurs
as part of the cross validation process.


In contrast, unsupervised learning uses unlabeled data to discover patterns that
help solve for clustering or association problems. This is particularly useful
when subject matter experts are unsure of common properties within a data set.


## The `scikit-learn` Package

One of the best known package for machine learning is `scikit-learn`.
It provides efficient versions of a large number of common algorithms with a
clean, iuniform, and streamlined API. 

To install with `pip`:
`pip install scikit-learn`


Note that `sklearn` and `scikit-learn` both refer to the same package.

For better-looking visualizations, we need the `graphviz` package. It can be
installed with `conda` easily:
`conda install python-graphviz`. 
To install with `pip`, the system library `graphviz` needs to be
installed first. On a Mac, for example, one could do so with 
`brew install graphviz`. 
Then the `graphviz` Python package can be installed with
`pip install graphviz`.
