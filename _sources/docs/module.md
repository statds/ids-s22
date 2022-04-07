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

(module)=

# Building a Python Module

This is a basic tutorial on how to build your own a Python module, or package. There are a few required downloads necessary and strict steps to follow.

## File Structure

The file structure for this example is as follows:

--> `base_stat3255tutorial`

--------> `stat3255tutorial`

----------------> `__init__.py`

----------------> `add.py`

--------> `setup.py`

You always want to have a base folder that houses your actual module. `add.py` contains the code and functions that we would like to package.
    
## \_\_init\_\_.py

In the `__init__.py` file, we want to import the functions from the file that we stored them. It tells Python to treat the directory as a module. You would need an `__init__.py` file in every sub-folder of the directory if you had a more complex directory.

```python
from stat3255tutorial.add import add
```

## setup.py

`setup.py` goes in the base directory alongside your actual module. This file has information about the package, like the name, version, dependencies, etc.

```python
from setuptools import find_packages, setup
setup(
    name='stat3255tutorial',
    packages=find_packages(),
    version='0.1.0',
    description='Tutorial Python library for STAT 3255',
    author='Anthony Zeimbekakis',
    license='license',
)
```

Now, we need to run the following command in the terminal. 

    python setup.py sdist bdist_wheel
    
This command builds a source distribution and a wheel. The source distribution contains source code and the wheel makes the installation faster.

## PyPi

From [PyPi](https://pypi.org), "the Python Package Index (PyPI) is a repository of software for the Python programming language." Anyone and everyone can make a Python package and upload it to the repository for download. Note that you must have a PyPi account in order to upload a package.

The files that we are actually uploading are the ones that were generated when we ran `setup.py`. In order to upload, the `twine` package is required and can be installed with a simple command.

    pip install twine
    
With that good to go, run the following command in the terminal. You will need your username and password.

    twine upload dist/*
    
This uploads all the contents in the `dist` folder which was created by `setup.py`. Now, we can install our very own Python package!

## Installing Our Package

We can now install our package just like any other that we've done before.

    pip install stat3255tutorial
    
Now, Python should recognize the package and we can access the functions inside.

```{code-cell} ipython3
import stat3255tutorial as stat

stat.add(1, 1)
```

## More Information

The package `stat3255tutorial` is very bare bones and lacks much of what you would need for an actual package release. The [Python Packaging Authority](https://packaging.python.org/en/latest/guides/distributing-packages-using-setuptools/) has a much more in-depth tutorial that contains information on licensing, versioning, and other topics and should be the first resource consulted.
