---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

(prman)=

# Project Management

## Reproducible Data Science Project

### Using Virtual Environment for Python

It is a good practice to set up and use virtual environments for Python (or R) projects.
See a tutorial of virtual environments at [Python Docs](https://docs.python.org/3/tutorial/venv.html).

### Using Jupyter Notebook
+ File name extension `.ipynb`
+ Separate code chunks and companion texts
+ Test and edit until the whole notebook runs as expected
+ Download the notebook into a pdf file (for GitHub project release)

###  Using `python` Engine in `RMarkdown`
+ Find a Markdown cheatsheet
+ Install R `reticulate` package
+ See example in `hw-rmkdn.Rmd` in the repo


## Setting up a Git Repo

+ See documentations at [GitHub Docs](https://docs.github.com/).
+ Demonstration with homework template

## Styles

### Programming

+ Naming
    - file/folder
    - variables
    - functions
    - modules/packages
+ Spacing
+ Indentation

Google code recommendations:
<https://code.google.com/archive/p/soc/wikis/PythonStyleGuide.wiki>

### Git Repo

+ Frequent commit (more snapshots)
+ Informative message
+ Keep it clean (no temporary or generated files)
+ Make it reproducible (e.g., relative path)
