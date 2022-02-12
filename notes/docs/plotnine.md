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

(plotnine)=

# Plotnine

> plotnine is an implementation of a grammar of graphics in Python, it is based on ggplot2. The grammar allows users to compose plots by explicitly mapping data to the visual objects that make up the plot. Plotting with a grammar is powerful, it makes custom (and otherwise complex) plots easy to think about and then create, while the simple plots remain simple.

## Install Plotnine

To install plotnine with pip, use the command: 
> pip install plotnine

## Import Plotnine
First we need to import pandas, numpy, and plotnine with the following code:
```{code-cell}
import pandas as pd
import numpy as np
from plotnine import *

nyc = pd.read_csv("../data/nyc_mv_collisions_202201.csv")
nyc_crash.info()
```