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