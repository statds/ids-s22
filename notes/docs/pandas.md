---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.11.5
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

(pandas)=

# Data Manipulation with Pandas

From [`pandas` documentation](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html):

> pandas is a Python package providing fast, flexible, and expressive
> data structures designed to make working with “relational” or
> “labeled” data both easy and intuitive. It aims to be the
> fundamental high-level building block for doing practical,
> real-world data analysis in Python. Additionally, it has the broader
> goal of becoming the most powerful and flexible open source data
> analysis/manipulation tool available in any language. It is already
> well on its way toward this goal.

> pandas is well suited for many different kinds of data:
>
> + Tabular data with heterogeneously-typed columns, as in an SQL
>   table or Excel spreadsheet
> + Ordered and unordered (not necessarily fixed-frequency) time
>   series data
> + Arbitrary matrix data (homogeneously typed or heterogeneous) with
>   row and column labels
> + Any other form of observational / statistical data sets. The data
>   need not be labeled at all to be placed into a pandas data
>   structure

## Import/Export

```{code-cell}
import pandas as pd

nyc_crash = pd.read_csv("../data/nyc_mv_collisions_202201.csv")
nyc_crash.info()
```

Exporting data out of pandas is provided by different `to_*`methods.

```{code-cell}
nyc_crash.to_csv("my_nyc.csv", index=False)
```

## View and Description

The `head/tail/info` methods and the `dtypes` attribute are convenient
for a first check.


Descriptive statistics for numeric variables:
```{code-cell}
nyc_crash_desc = nyc_crash.describe()
nyc_crash_desc
```

Transpose the data:
```{code-cell}
nyc_crash_desc.T
```

Frequency table:
```{code-cell}
pd.crosstab(index=nyc_crash["BOROUGH"], columns = "freq")
```

## Subsetting

Selection by row:
```{code-cell}
brooklyn_crash = nyc_crash[nyc_crash["BOROUGH"] == "BROOKLYN"]
bronx_crash    = nyc_crash[nyc_crash["BOROUGH"] == "BRONX"]
```

Selection by column:
```{code-cell}
bronx_crash[["CRASH DATE", "CRASH TIME"]]
```

By both
```{code-cell}
bronx_crash.iloc[0:9, 0:2]
```


## Creating new columns

Convert the time string to numeric for later processing:
```{code-cell}
nyc_crash["time"] = [x.split(":")[0] for x in nyc_crash["CRASH TIME"]]
nyc_crash["time"] = [int(x) for x in nyc_crash["time"]]

import matplotlib.pyplot as plt
plt.hist(nyc_crash["time"])
```

## Reshape

Sort table rows
```{code-cell}
nyc_crash.sort_values(by="time").head()
```

Pivot table (long to wide)
```{code-cell}
nyc_crash.pivot_table(
    values  = ["LATITUDE", "LONGITUDE"],
	index   = "BOROUGH",
	aggfunc = "mean",
	margins = True)
```

Melting (wide to long)

## Combining

Row bind
```{code-cell}
pd.concat([bronx_crash, brooklyn_crash], axis = 0).head(5)
```

Also can use `append()` method.
```{code-cell}
bronx_crash.append(brooklyn_crash).tail(5)
```

Column bind
