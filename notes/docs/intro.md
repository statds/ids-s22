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
- Install `jupyter-book` with  `pip install jupyter-book`

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

```{code-cell}
---
other:
  more: true
tags: [hide-output, show-input]
---
print("Hello!")
```

```{code-block}
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
+ Campman, Benjamin
    - Develop more concrete ability and knowledge of Python
    - Understand basics of Git
+ Hughes, Sam
    - Become proficient with git
    - Learn data visualization techniques in Python
    - Learn reinforcement learning through Python
    - Learn K Nearest Neighbors Algorithm
+ McClurg, Taelor
    - Gain hands on experience using data science tools to analyze real data and answer interesting questions.
    - Improve my comfort level with Python.
    - Explore data visualization tools available in Python.
    - Become proficient in git.
+ Shamirian, Robbie
    - Continue to grow my Python skills in relation to data science
    - Understand the fundamentals of Git
+ Sharma, Sinchan
    - Become adept at using Git and GitHub for projects
    - Become more comfortable with using Python
    - Learn data analysis and visualization techniques using Python
+ Taffe, Thalia
    - Better understand cmd functionality
+ Xu, Zhenyu
    - Get familiar with Git and Github like a professional data scientist
    - Learn more Python skills
    - Participate in a practical data science competition
+ Yan, Jun
    - Make data science accessible to undergraduates
    - Co-develop a notebook with Jupyter-book in collaboration with students
+ Zeimbekakis, Anthony
    - Become proficient in using command line with tools such as GitHub or even just file management
    - Apply past knowledge of Python to be comfortable performing data science tasks

## Task Board

+ Project management
    - Markdown basics
    - Jupyter notebook
    - Jupyter book
    - Jun Yan ~~Git basics~~
+ Python refreshment
    - Vectors, matrices, and arrays
    - Distributions
    - Optimization
    - Data manipulation
+ Visualization
    - Package matplotlib (Tu, Feb. 8: Taelor McClurg)
    - Package cartopy/basemap (Th, Feb. 10: Sam Hugh)
    - Package plotnine (ggplot2 equivalent) (Tu. Feb. 15: Mathew Chandy)
    - Google Maps Plot (Thurs. Feb 17: Robbie Shamirian)
+ Statistical tests and models
    - Desribing statistical models with package `patsy`  (Th. Feb. 17:
      Anthony Zeimbekakis) 
    - Statistical models and hypothesis tests with package
      `statsmodels` 
+ Supervised learning
    - Decision trees (Th. Feb. 24: Ryan Schoenfeld)
    - Support vector machine (Tu. March 1: Peter Busa)
    - Random forests (Th. Mar. 3: Sinchan Sharma)
    - Nearest neighbor
    - Ensembool methods
        + Bagging
        + Boosting
        + XGBoost
+ Unsupervised learning
    - K-means clustering
    - Gaussian mixture models
+ Neural networks and deep learning
+ Further interests
    - MIT App Inventor turorial
    - Using R from Python and vice versa
    - Building a Python module

## Topic Presentation Sign-up

Date    | Presenter            | Topic
----- | ------------- | -----
02/08 | Taelor McClurg   | package `matplotlib`
02/10 | Samuel Hugh      | package `cartopy`
02/15 | Mathew Chandy  | package `plotnine`
02/17 | Robert Shamirian | package `ggmap`
02/17 | Anthony Zeimbekakis | package `patsy`
02/22 | Jun Yan               | package `statsmodels` 
02/24 | Ryan Schoenfeld | decision trees
03/01 | Peter Busa           | support vector machine
03/03 | Sinchan Sharma  | Random forest
03/10 |  | 
03/10 |  | 
03/22 |  |
03/22 |  |
03/24 |  |
03/24 |  |


## Data Challenges in Action

+ [When Will the Cherry Trees
  Bloom](https://competition.statistics.gmu.edu/)

+ [ASA Data Challenge
  Expo](https://community.amstat.org/dataexpo/home)

## NYC Open Data Week Event

### Open Data in a Classroom
The NYC Open Data provides opportunities for data scientists to
demonstrate what data science can do in real life. Students taking
Introduction to Data Science (STAT3255/5255) in Spring 2022 at UConn
are required to work on a project of their choice with any dataset on
NYC Open Data. The topics will be a mix of instructor recommendations
and self selections, covering transportation, construction, education,
finance, and health, among others. Examples are the motor vehicle
collisions crashes; DOB job application filling. NYC leading causes of
death. and for students in data science. data science education. Five
students will be selected from the class to present their works in a
virtual panel. The presented project will be made public in
crowd-sourced open class notes, facilitating real-life open data
projects in data science education.

### Dates
Four students will present during our lecture hour on
Tuesday, March 8, 2022, to the general public as part of the NYC Open
Data Week.
