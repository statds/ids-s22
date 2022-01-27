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

(fixedit)=

# I Fixed It

When something does not work as expected, be sure to read the error
message carefully. Oftentimes, the problem has been encountered by
others. A Google search for the quoted error message may lead to an
existing solution.


This is a collection of questions and answers during this semester,
which could be helpful for others with the same problems.

## TypeError when trying to build the Jupyter Book

### Error in Question:

    TypeError: asdict() got an unexpected keyword argument 'value_serializer'

### Solution: Update your packages
The solution that worked for me came from this forum: https://githubhelp.com/executablebooks/sphinx-external-toc/issues/49. In the forum, it's suggested that the package "sphinx-external-toc" may be outdated and the current version does not meet the requirements to build the book.
To solve this, you can use pip to update this package. Enter the following into the command line:

    pip install --upgrade sphinx-external-toc


In my case I had to use pip3 install, but the idea should be the same. Then, run "jb build ." again and the book should build without error. This solution worked for me so hopefully it works for anyone else who runs into this error!

```{code-cell} ipython3

```
