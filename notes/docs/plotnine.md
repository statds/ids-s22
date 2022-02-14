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

url = 'https://raw.githubusercontent.com/statds/ids-s22/main/notes/data/nyc_mv_collisions_202201.csv'
nyc = pd.read_csv(url)
nyc_crash.info()
```

## Histograms
With plotnine, we can create histogram plots.
```{code-cell}
nyc['hour'] = [x.split(':')[0] for x in nyc['CRASH TIME']]
nyc['hour'] = [int(x) for x in nyc['hour']]
(
    ggplot(nyc, aes(x = 'hour', y = after_stat('count')))
    + geom_histogram(binwidth = 1, bins = 24)
    + ggtitle("Hourly Crash Count")
)
```

We can easily add multiple plots.
```{code-cell}
(
    ggplot(nyc, aes(x = 'hour', y = after_stat('count')))
    + geom_histogram(binwidth = 1, alpha = 0.5)
    + geom_histogram(binwidth = 2, alpha = 0.5, fill = 'green')
    + ggtitle("Hourly and Bihourly Crash Count")
)
```

We can visualize the plots with respect to other variables.
```{code-cell}
(
    ggplot(nyc, aes(x = 'hour', y = after_stat('count'), fill = 'BOROUGH'))
    + geom_histogram(binwidth = 4)
    + ggtitle("Hourly Crash Count for each Borough")
)
```

## Boxplot
```{code-cell}
(
    ggplot(nyc)
    + geom_boxplot(aes(x = 'BOROUGH', y = 'NUMBER OF PERSONS INJURED'))
    + ggtitle("Boxplot of # of Persons Injured for each Borough")
)
```

## Violin Plot
```{code-cell}
(
  ggplot(nyc, aes('BOROUGH', 'NUMBER OF PERSONS KILLED'))
  + geom_violin(nyc)
  + geom_point()
)
```

## Scatter Plot
```{code-cell}
(
  nyc[nyc.LONGITUDE == 0] = np.nan
nyc_plot = ggplot(nyc, aes(x = 'LONGITUDE', y = 'LATITUDE', color = 'BOROUGH'))
nyc_plot + geom_point(alpha = 0.1) + ggtitle("Coordinates of Car Crashes")
)
```
)