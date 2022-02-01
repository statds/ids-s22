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

(refresh)=

# Python Refreshment

You have programmed in Python. Regardless of your skill level, let us
do some refreshing.

## The Python World

+ Function: a block of organized, reusable code to complete certain
  task.
+ Module: a file containing a collection of functions, variables, and
  statements.
+ Package: a structured directory containing collections of modules
  and an `__init.py__` file by which the directory is interpreted as a
  package.
+ Library: a collection of related functionality of codes. It is a
  reusable chunk of code that we can use by importing it in our
  program, we can just use it by importing that library and calling
  the method of that library with period(.).
  
See, for example, [how to build a Python
libratry](https://medium.com/analytics-vidhya/how-to-create-a-python-library-7d5aea80cc3f).


Question: How to get the constant $e$ to an arbitary precision?

The constant is only represented by a given double precision.
```{code-cell} ipython3
import math
print("%0.20f" % math.e)
print("%0.80f" % math.e)
```
Now use package `decimal` to export with an arbitary precision.
```{code-cell} ipython3
import decimal  # for what?

## set the required number digits to 150
decimal.getcontext().prec = 150
decimal.Decimal(1).exp().to_eng_string()
decimal.Decimal(1).exp().to_eng_string()[2:]
```

Question: how to draw a random sample from a normal distribution and
evaluate the density and distributions at these points?
```{code-cell} ipython3
from scipy.stats import norm

mu, sigma = 2, 4
mean, var, skew, kurt = norm.stats(mu, sigma, moments='mvsk')
print(mean, var, skew, kurt)
x = norm.rvs(loc = mu, scale = sigma, size = 10)
x
```
The pdf and cdf can be evaluated:
```{code-cell} ipython3
norm.pdf(x, loc = mu, scale = sigma)
```

## Writing a Function

Consider the Fibonacci Sequence
$1, 1, 2, 3, 5, 8, 13, 21, 34, ...$.
The next number is found by adding up the two numbers before it.
We are going to use 3 ways to solve the problems.


The first is a recursive solution.
```{code-cell} ipython3
def fib_rs(n):
    if (n==1 or n==2):
        return 1
    else:
        return fib_rs(n - 1) + fib_rs(n - 2)

%timeit fib_rs(10)
```

The second uses dynamic programming memoization.
```{code-cell} ipython3
def fib_dm_helper(n, mem):
    if mem[n] is not None:
        return mem[n]
    elif (n == 1 or n == 2):
        result = 1
    else:
        result = fib_dm_helper(n - 1, mem) + fib_dm_helper(n - 2, mem)
    mem[n] = result
    return result

def fib_dm(n):
    mem = [None] * (n + 1)
    return fib_dm_helper(n, mem)

%timeit fib_dm(10)
```

The third is still dynamic programming but bottom-up.
```{code-cell} ipython3
def fib_dbu(n):
    mem = [None] * (n + 1)
    mem[1]=1;
    mem[2]=1;
    for i in range(3,n+1):
        mem[i] = mem[i-1] + mem[i-2]
    return mem[n]
        

%timeit fib_dbu(500)
```

Apparently, the three solutions have very different performance for
larger `n`.


## Variables versus Objects

In Python, variables and the objects they point to actually live in
two different places in the computer memory. Think of variables as
pointers to the objects they’re associated with, rather than being
those objects. This matters when multiple variables point to the same
object.
```{code-cell} ipython3
x = [1, 2, 3]  # create a list; x points to the list
y = x          # y also points to the same list in the memory
y.append(4)    # append to y
x              # x changed!
```
Now check their addresses
```{code-cell} ipython3
print(id(x))   # address of x
print(id(y))   # address of y
```


Nonetheless, some data types in Python are "immutable", meaning that
their values cannot be changed in place. One such example is strings.
```{code-cell} ipython3
x = "abc"
y = x
y = "xyz"
x
```

Now check their addresses
```{code-cell} ipython3
print(id(x))   # address of x
print(id(y))   # address of y
```

Question: What's mutable and what's immutable?

Anything that is a collection of other objects is mutable, except
``tuples``.

Not all manipulations of mutable objects change the object rather than
create a new object. Sometimes when you do something to a mutable
object, you get back a new object. Manipulations that change an
existing object, rather than create a new one, are referred to as
“in-place mutations” or just “mutations.” So:

+ __All__ manipulations of immutable types create new objects.
+ __Some__ manipulations of mutable types create new objects.

Different variables may all be pointing at the same object is
preserved through function calls (a behavior known as “pass by
object-reference”). So if you pass a list to a function, and that
function manipulates that list using an in-place mutation, that change
will affect any variable that was pointing to that same object outside
the function.
```{code-cell} ipython3
x = [1, 2, 3]
y = x

def append_42(input_list):
    input_list.append(42)
    return input_list

append_42(x)
```
Note that both `x` and `y` have been appended by $42$.

## Number Representation

Numers in a computer's memory are represented by binary styles (on and
off of bits). 

### Integers
If not careful, It is easy to be bitten by overflow with integers when
using Numpy and Pandas in Python.


```{code-cell} ipython3
import numpy as np

x = np.array(2**63 - 1 , dtype='int')
x
# This should be the largest number numpy can display, with
# the default int8 type (64 bits)
```

What if we increment it by 1?
```{code-cell} ipython3
y = np.array(x + 1, dtype='int')
y
# Because of the overflow, it becomes negative!
```
For vanilla Python, the overflow errors are checked and more digits
are allocated when needed, at the cost of being slow.
```{code-cell} ipython3
2**63 * 1000 
```
This number is 1000 times largger than the prior number, 
but still displayed perfectly without any overflows 

### Floating Number

Standard double-precision floating point number uses 64 bits. Among
them, 1 is for sign, 11 is for exponent, and 52 are fraction significand, 
See <https://en.wikipedia.org/wiki/Double-precision_floating-point_format>.
The bottom line is that, of course, not every real number is exactly
representable.


```{code-cell} ipython3
0.1 + 0.1 + 0.1 == 0.3
```

```{code-cell} ipython3
0.3 - 0.2 == 0.1
```
What is really going on?
```{code-cell} ipython3
import decimal
decimal.Decimal(0.1)
```


Because the mantissa bits are limited, it can not represent a floating point 
that's both very big and very precise. Most computers can represent all integers
up to $2^53$, after that it starts skipping numbers.

```{code-cell} ipython3
2.1**53 +1 == 2.1**53

# Find a number larger than 2 to the 53rd
```

```{code-cell} ipython3
x = 2.1**53
for i in range(1000000):
    x = x + 1
x == 2.1**53
```
We add 1 to `x` by 1000000 times, but it still equal to its initial
value,  2.1**53. This is because this number is too big that computer
can't handle it with precision like add 1.

Machine epsilon is the smallest positive floating-point number `x` such that
`1 + x != 1`.
```{code-cell} ipython3
print(np.finfo(float).eps)
print(np.finfo(np.float32).eps)

```

## Data Importation

NYC Open Data is great resource of open data. One specific dataset of
interest is the [Motor Vehicle
Collisons-Crashes](https://data.cityofnewyork.us/Public-Safety/Motor-Vehicle-Collisions-Crashes/h9gi-nx95).


> The Motor Vehicle Collisions crash table contains details on the
> crash event. Each row represents a crash event. The Motor Vehicle
> Collisions data tables contain information from all police reported
> motor vehicle collisions in NYC. The police report (MV104-AN) is
> required to be filled out for collisions where someone is injured or
> killed, or where there is at least $1000 worth of damage

The data is big. I only downloaded the data from January 1 to 25,
2022.

```{code-cell} ipython3
import pandas as pd

nyc_crash = pd.read_csv("../data/nyc_mv_collisons_202201.csv")
nyc_crash.head(10)
```

There are 29 variables.
```{code-cell} ipython3
nyc_crash.columns
```

