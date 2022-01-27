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

(intro)=

# Introduction

## What is Data Science?

One widely accepted concept is the three pillars of data science:
mathematics/statistics, computer science, and domain knowledge.

In her 2014 Presidential Address, Prof. Bin Yu, then President of the
Institute of Mathematical Statistics, gave an interesting definition:
```{math}
\mbox{Data Science} =
\mbox{S}\mbox{D}\mbox{C}^3,
```
where S is Statistics, D is domain/science knowledge, and
the three C's are computing, collaboration/teamwork, and communication
to outsiders.


## Computing Environment

All setups are operating system dependent.

As soon as possible, stay away from Windows. Otherwise, good luck (you
need it).

### Command line interface

[Ubunto Linux for beginners](https://ubuntu.com/tutorials/command-line-for-beginners).

### Python

- Install Python package manager __miniconda__ or __pip__.
- Install Python
- Install an IDE (Jupyter Notebook or VS Code)

### A book project with Jupyter-book

- Markdown for text
- Jupyter notebook for code demo
- Jupytext has nice features handling markdown source
- Install `jupyter-boook` with  `pip install jupyter-book`

To build the book, go to the source directory of the book. under
`notes`,  run `jb build .`

### MyST Markdown

Markedly Structured Text (MyST) examples:

```{admonition} Add my admonition
Adding my little admonition
```

````{note}
Initial
```{warning}
warning
```
````

```{eval-rst}
.. note::

   A note written in reStructuredText.
```

```{code-cell} ipython3
---
other:
  more: true
tags: [hide-output, show-input]
---
print("Hello!")
```

```{code-block} python
---
lineno-start: 10
emphasize-lines: 1, 3
caption: |
    This is my
    multi-line caption. It is *pretty nifty* 
---
a = 2
print('my 1st line')
print(f'my {a}nd line')
```

```{admonition} Here's my title
:class: warning

Here's my admonition content
```


```{math} ax^{2} + bx + c
---
label: quadratic
---
```

The basic quadratic equation, {math:numref}`quadratic`, allows for the 
construction of all kinds of parabolas


## Wish List

This is a wish list from all members of the class (alphabetical
order). Add yours; note the syntax of nested list in Markdown.
+ Aimandi, Sakeena
    - Complete a data science project to gain some meaningful insight and solve a real-world problem
+ Chandy, Mathew
    - Learn the fundamentals of data science
    - Become more comfortable with command line interface
    - Work on a data science project involving a meaningful topic
+ Hughes, Sam
    - Become comfortable with git
    - Learn data visualization techniques in Python
    - Learn reinforcement learning through Python
+ McClurg, Taelor
    - Gain hands on experience using data science tools to analyze real data and answer interesting questions.
    - Improve my comfort level with Python.
    - Explore data visualization tools available in Python.
    - Become proficient in git.
+ Yan, Jun
    - Make data science accessible to undergraduates
    - Co-develop a notebook with Jupyter-book in collaboration with students
+ Zeimbekakis, Anthony
    - Become proficient in using command line with tools such as GitHub or even just file management
    - Apply past knowledge of Python to be comfortable performing data science tasks
+ Campman, Benjamin
    - Develop more concrete ability and knowledge of Python
    - Understand basics of Git
+ Shamirian, Robbie
    - Continue to grow my Python skills in relation to data science
    - Understand the fundamentals of Git

## Task Board

+ Project management 
    - Markdown basics
    - Jupyter notebook
    - Jupyter book
    - Jun Yan ~~Git basics~~
+ Python refreshment
    - Vectors, matrices, and arrays
    - Distributions
+ Optimization
    - Unrestricted
    - Restricted
+ Visualization
    - Package matplotlib
    - Package seaborn
+ Statistical tests and models
    - Hypothesis tests
    - Generalized linear models (specifically logistic)
+ Tree-based models
    - Tree models
    - Random forest
+ Boosting and XGBoost
    - Gradient boosting and its variants
    - XGBoost in action
+ Unsupervised learning
    - K-means clustering
    - Model-based
+ Neural networks and deep learning
