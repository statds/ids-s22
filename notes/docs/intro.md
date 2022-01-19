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

[Ubunto Linux for beginners]({https://ubuntu.com/tutorials/command-line-for-beginners).


### Python

- Install Python package manager __miniconda__ or __pip__.
- Install Python
- Install an IDE (Jupyter Notebook or VS Code)

### A book project with Jupyter-book

- Markdown for text
- Jupyter notebook for code demo
- Jupytext

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

### Git and GitHub

See documentations at [GitHub Docs](https://docs.github.com/).

