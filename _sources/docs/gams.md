---
jupyter:
  jupytext:
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.11.5
  kernelspec:
    display_name: Python 3 (ipykernel)
    language: python
    name: python3
---

# Generalized Additive Models


### Background


Generalized Additive Models (abbreviated as GAM) are a regression technique used to model nonlinear variables. 


GAMs use piecewise equations to create a regression line that is continuous in the second derivative. 


A simple linear regression equation is $y~=~\beta$<sub>0</sub>$~+~\beta$<sub>1</sub>$x$<sub>1</sub>$~+~\epsilon$


The same equation as a GAM would be $y~=~\alpha~+~f$<sub>1</sub>$(x$<sub>1</sub>$)~+~\epsilon$


**Two important terms**
- lambda: smooths regression line to prevent overfitting
- knots: controls amount of sub-functions in piecewise equation, also related to overfitting


**Assumptions**

- Observations are idependent
- Correct smoothing
- Correct link function
- Minimal outliers
- No multicollinearity between predictors

Source: http://www.medicine.mcgill.ca/epidemiology/goldberg/gam_class_part4.pdf


**Pros**
- Linear model on steroids
- More understandable than other methods such as XGBoost
- Continuity of second derivative
- Can be used on not just linear models, but also logistic, poisson, etc. 

**Cons**
- Not as understandable as a linear model
- Not as powerful as other methods such as XGBoost
- Requires assumptions to be met
- The python package is a work in progress


### Python


Resources
- https://pygam.readthedocs.io/en/latest/notebooks/tour_of_pygam.html
- https://www.youtube.com/watch?v=XQ1vk7wEI7c


To install the package, use one of the following commands:

- `pip install pygam`
- `conda install -c conda-forge pyGAM`


**Dataset**

Fantasy football draft data (2015 to 2021)

```python
import pandas as pd
```

Load in average draft position (ADP) data

```python
df_ADP = pd.read_html("https://www.fantasypros.com/nfl/adp/ppr-overall.php?year=2015")[0]
df_ADP["year"] = int(2015)
df_ADP.head()
```

```python
for i in range(16, 22):
    df_ADP = df_ADP.append(pd.read_html("https://www.fantasypros.com/nfl/adp/ppr-overall.php?year=20" + str(i))[0])
    df_ADP.loc[df_ADP["year"].isnull(), "year"] = int("20" + str(i))
df_ADP.reset_index(drop = True, inplace = True)
df_ADP.sample(10)
```

```python
df_ADP.info()
```

Clean column names

```python
df_ADP.rename({
               "Rank": "rank_adp", 
               "Player Team (Bye)": "player", 
               "AVG": "avg_adp"
              }, axis = 1, inplace = True)
df_ADP.columns = df_ADP.columns.str.lower()
df_ADP.head()
```

Clean player names (because player names include team and bye)

```python
temp = df_ADP["player"].str.split(n = 2)
df_ADP["player"] = temp.str[0] + " " + temp.str[1]
df_ADP.head()
```

Clean position (because position includes ranking number)

```python
df_ADP['pos'] = df_ADP['pos'].str.replace('\d+', '')
df_ADP.head()
```

Load in points data (2015 to 2021)

```python
df_points = pd.read_html("https://www.fantasypros.com/nfl/reports/leaders/ppr.php?year=2015&start=1&end=16")[0]
df_points["year"] = 2015
df_points.head()
```

```python
for i in range(16, 22):
    df_points = df_points.append(
        pd.read_html("https://www.fantasypros.com/nfl/reports/leaders/ppr.php?year=20" + str(i) + "&start=1&end=16"
                    )[0])
    df_points.loc[df_points["year"].isnull(), "year"] = int("20" + str(i))
    
df_points.reset_index(drop = True, inplace = True)
df_points.sample(10)
```

```python
df_points.info()
```

Clean column names

```python
df_points.rename({
                  "Rank": "rank_scoring", 
                  "Position": "pos", 
                  "Avg": "avg_scoring"
                 }, axis = 1, inplace = True)
df_points.columns = df_points.columns.str.lower()
df_points.head()
```

Clean player names (to match with ADP)

```python
temp = df_points["player"].str.split(n = 2)
df_points["player"] = temp.str[0] + " " + temp.str[1]
df_points.head()
```

Merge dataframes

```python
df = pd.merge(df_ADP, df_points,  how='left', on = ["player", "pos", "year"])
df.head()
```

Only include players who have played over 8 games and players with ADP before 251. 

Players who played less than 9 games may have crazy averages due to low sample size. 

Players with ADP above 250 would not get drafted in basically any fantasy league of reasonable size. 

```python
df = df.loc[(df["games"] > 8) & (df["rank_adp"] <= 250)]
df.head()
```

To start with, let's only analyze running backs

```python
df_rb = df.loc[df["pos"] == "RB", ]
df_rb.head()
```

```python
from plotnine import *
```

```python
(
    ggplot(data = df_rb, mapping = aes(x = "rank_adp", y = "avg_scoring")) +
    geom_point() +
    labs(title = "Running Back Scoring Average by ADP", 
         x = "Draft Ranking", y = "Scoring Average")
)
```

The graph is clearly nonlinear


**Model Building**


First specify x and y variables

```python
X = df_rb["rank_adp"].to_numpy()
y = df_rb["avg_scoring"].to_numpy()
```

It is advised to use `to_numpy()` as this will allow gridsearching parameters but there is a bug for doing so with a single predictor anyway

```python
from pygam import LinearGAM, s, l, f
```

- `s`: smoothing
- `l`: linear
- `f`: factor


To start with, we can make a linear line by using `l`

```python
# Note 0 is the first term inserted to X
lin = LinearGAM(l(0)).fit(X, y)
lin.summary()
```

```python
df_rb["prediction_lin"] = lin.predict(X)
df_rb.head()
```

```python
(
    ggplot(data = df_rb, mapping = aes(x = "rank_adp", y = "avg_scoring")) +
    geom_point() +
    geom_line(aes(y = "prediction_lin"), color = "blue", size = 3) +
    labs(title = "Running Back Scoring Average by ADP", 
         x = "Draft Ranking", y = "Scoring Average")
)
```

Now let's fit a real GAM using `s`

```python
gam = LinearGAM(s(0)).fit(X, y)
gam.summary()
```

```python
df_rb["prediction_gam"] = gam.predict(X)
df_rb.head()
```

```python
(
    ggplot(data = df_rb, mapping = aes(x = "rank_adp", y = "avg_scoring")) +
    geom_point() +
    geom_line(aes(y = "prediction_gam"), color = "red", size = 3) +
    labs(title = "Running Back Scoring Average by ADP", 
         x = "Draft Ranking", y = "Scoring Average")
)
```

It looks like the line is a little more squiggly than it needs to be. 
This means that are model is likely overfitting. 


To counteract this, we can adjust `n_splines` and/or `lam`

```python
gam_n = LinearGAM(s(0, n_splines = 6)).fit(X, y)
gam_n.summary()
```

```python
df_rb["prediction_gam_n"] = gam_n.predict(X)
df_rb.head()
```

```python
gam_lam = LinearGAM(s(0, lam = 100)).fit(X, y)
gam_lam.summary()
```

```python
df_rb["prediction_gam_lam"] = gam_lam.predict(X)
df_rb.head()
```

```python
(
    ggplot(data = df_rb, mapping = aes(x = "rank_adp", y = "avg_scoring")) +
    geom_point(alpha = 0.25) +
    geom_line(aes(y = "prediction_lin"), color = "blue", size = 3, alpha = 0.75) +
    geom_line(aes(y = "prediction_gam"), color = "red", size = 3, alpha = 0.75) +
    geom_line(aes(y = "prediction_gam_n"), color = "orange", size = 3, alpha = 0.75) +
    geom_line(aes(y = "prediction_gam_lam"), color = "green", size = 3, alpha = 0.75) +
    labs(title = "Running Back Scoring Average by ADP", 
         x = "Draft Ranking", y = "Scoring Average")
)
```

```python
X.shape
```

Unfortunately, when there is only 1 column of `shape`, it does not display it. 
This causes `gridsearch` to break so we cannot use this to find the best equation. 


We can also fit a regression model with multiple predictors


We need to label encode categorical variables for the model to behave correctly

```python
from sklearn.preprocessing import LabelEncoder
```

```python
le = LabelEncoder()
le.fit(df["pos"])
df["le_pos"] = le.transform(df["pos"])
df.head()
```

```python
X = df[["rank_adp", "le_pos"]].to_numpy()
y = df["avg_scoring"].to_numpy()
X
```

```python
gam = LinearGAM(s(0) + f(1)).fit(X, y)
gam.summary()
```

```python
df["prediction_gam"] = gam.predict(X)
```

```python
(
    ggplot(data = df, mapping = aes(x = "rank_adp", y = "avg_scoring", color = "pos")) +
    geom_point(alpha = 0.25) +
    geom_line(aes(y = "prediction_gam"), size = 3, alpha = 0.75) +
    labs(title = "Scoring Average by ADP", 
         x = "Draft Ranking", y = "Scoring Average")
)
```

Once again, it looks like the lines are overfitting due to the squigglyness


To counteract this, we can create an array with random terms to use for lambda.
`gridsearch` will select the best lambda values via cross validation. 

```python
import numpy as np
```

This formula will create a matrix of shape (1000, 2) with each value between $10^{-3}$ and $10^3$. 
We can use these as potential lambda values to optimize our GAM. 

```python
np.random.seed(123)
lams = 10 ** (np.random.rand(1000, 2) * 6 - 3)
lams
```

The `gridsearch` method uses the above matrix as well as cross validation
(or another specified criteria) to determine the best values of lambda. 

```python
gam_grid = LinearGAM(s(0) + f(1)).gridsearch(X, y, lam = lams)
gam_grid.summary()
```

```python
df["prediction_gam_grid"] = gam_grid.predict(X)
```

```python
(
    ggplot(data = df, mapping = aes(x = "rank_adp", y = "avg_scoring", color = "pos")) +
    geom_point(alpha = 0.25) +
    geom_line(aes(y = "prediction_gam_grid"), size = 3, alpha = 0.75) +
    labs(title = "Scoring Average by ADP", 
         x = "Draft Ranking", y = "Scoring Average")
)
```

It is clear that the regression lines are much smoother and no longer suffer from overfitting. 

```python

```
