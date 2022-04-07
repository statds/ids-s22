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

# XGBoost

XGBoost = Extreme Gradient Boosting. It is an implementation of gradient boosted decision trees. There are many other types of gradient boosting libraries such as Catboost and LightGBM.

**Pros:**

1. Less feature engineering required (normalizing data and can handle missing values well)
2. Feature importance can be seen
3. Outliers have minimal impact.
4. Handles large sized datasets well.
5. Good Execution speed
6. Good model performance (wins most of the Kaggle competitions)

**Cons:**

1. Visualization can be tough
2. Overfitting possible if parameters not tuned properly.
3. Harder to tune as there are too many hyperparameters.
4. Doesn't easily work with categorical variables

## Installing Packages

`pip install xgboost` to install

Can also use the graphviz package to visualize the trees in the same way we could for decision trees

## Data Import and Data Cleaning

We have to do a lot of data cleaning in order to add columns that we can use to be able to model easily with the data. 

```{code-cell} ipython3
import pandas as pd
import numpy as np
```


```{code-cell} ipython3
#nyc_crash = pd.read_csv("../data/nyc_mv_collisions_202201.csv")
nyc_crash = pd.read_csv("/content/nyc_mv_collisions_202201.csv") #- path I used but its wrong

nyc_crash["time"] = [x.split(":")[0] for x in nyc_crash["CRASH TIME"]]
nyc_crash["time"] = [int(x) for x in nyc_crash["time"]]

nyc_crash["injury_binary"] = nyc_crash["NUMBER OF PERSONS INJURED"].map(lambda x: 1 if x>0 else 0)

nyc_crash.rename(columns={"NUMBER OF PERSONS KILLED": "num_ppl_killed"}, inplace=True)
nyc_crash.rename(columns={"NUMBER OF PERSONS INJURED": "num_ppl_injured"}, inplace=True)

def seasons(row):
    if row['CRASH DATE'] < "03/20/2021":
        val = "Winter"
    elif row['CRASH DATE'] < "06/21/2021":
        val = "Spring"
    elif row['CRASH DATE'] < "09/23/2021":
        val = "Summer"
    elif row['CRASH DATE'] < "12/21/2021":
        val = "Autumn"
    else:
        val = "Winter"
    return val

nyc_crash['Season'] = nyc_crash.apply(seasons, axis = 1)


nyc_crash["num_injured"] = nyc_crash["NUMBER OF PEDESTRIANS INJURED"]
nyc_crash["num_injured"] += nyc_crash["NUMBER OF CYCLIST INJURED"]
nyc_crash["num_injured"] += nyc_crash["NUMBER OF MOTORIST INJURED"]

def injury(row):
    if row["num_injured"] >= 1:
        val = 1
    else:
        val = 0
    return val

nyc_crash['injury'] = nyc_crash.apply(injury, axis = 1)

```


```{code-cell} ipython3
nyc_crash["CONTRIBUTING FACTOR VEHICLE 1"] = nyc_crash["CONTRIBUTING FACTOR VEHICLE 1"].fillna('Other')
df = nyc_crash["CONTRIBUTING FACTOR VEHICLE 1"].value_counts()

def cfv1_1000(row):
    if df[row['CONTRIBUTING FACTOR VEHICLE 1']] > 1000:
        val = row['CONTRIBUTING FACTOR VEHICLE 1']
    else:
        val = "Other"
    return val

nyc_crash['cfv1_1000'] = nyc_crash.apply(cfv1_1000, axis = 1)


def timeframes(x):
    if x <= 5:
        return 1
    elif x > 5 and x <= 11:
        return 2
    elif x > 11 and x <= 17:
        return 3
    else:
        return 4

nyc_crash["CONTRIBUTING FACTOR VEHICLE 2"] = nyc_crash["CONTRIBUTING FACTOR VEHICLE 2"].fillna('NaN')
nyc_crash["CONTRIBUTING FACTOR VEHICLE 3"] = nyc_crash["CONTRIBUTING FACTOR VEHICLE 3"].fillna('NaN')
nyc_crash["CONTRIBUTING FACTOR VEHICLE 4"] = nyc_crash["CONTRIBUTING FACTOR VEHICLE 4"].fillna('NaN')
nyc_crash["CONTRIBUTING FACTOR VEHICLE 5"] = nyc_crash["CONTRIBUTING FACTOR VEHICLE 5"].fillna('NaN')        

def number_of_vehicles(row):
    if row["CONTRIBUTING FACTOR VEHICLE 5"] != 'NaN':
        val = 5
    elif row["CONTRIBUTING FACTOR VEHICLE 4"] != 'NaN':
        val = 4
    elif row["CONTRIBUTING FACTOR VEHICLE 3"] != 'NaN':
        val = 3
    elif row["CONTRIBUTING FACTOR VEHICLE 2"] != 'NaN':
        val = 2
    elif row["CONTRIBUTING FACTOR VEHICLE 1"] != 'NaN':
        val = 1
    return val

nyc_crash['num_vehicles'] = nyc_crash.apply(number_of_vehicles, axis = 1)
    

## Take crash hours and put them into specifc intervals
nyc_crash['timeframe'] = nyc_crash['time'].apply(timeframes)
nyc_crash['timeframe'] = nyc_crash['timeframe'].astype('category')

```
## Classification XGBoost Model

In this model we are trying to predict if someone was injured or not in the crash. The variables that we will predict it with are the number of vehicles, season, time of day, bourough and the cause of the crash.

XGBoost needs a DMatrix to be created in order to run `xgb.train` but we will use the fit function here which accepts a pandas dataframe. A DMatrix can be easily created and it helps to increase memory efficiency and training speed.

A DMatrix can be created with `data_dmatrix = xgb.DMatrix(data=,label=)`

```{code-cell} ipython3
import xgboost as xgb
from sklearn import metrics
from sklearn.metrics import classification_report, confusion_matrix
from sklearn.model_selection import train_test_split

feature_cols1 = nyc_crash[['num_vehicles','Season','timeframe', 'BOROUGH', 'cfv1_1000']]
features = pd.get_dummies(feature_cols1)

y = nyc_crash.injury


x_train, x_test, y_train, y_test = train_test_split(features, y, 
                                                    test_size = 0.2, 
                                                    random_state = 12)

xg_cls = xgb.XGBClassifier(objective ='binary:logistic',seed = 20)

xg_cls.fit(x_train,y_train)

y_pred = xg_cls.predict(x_test)

print("Accuracy:", metrics.accuracy_score(y_test, y_pred))
print(confusion_matrix(y_test, y_pred))
```
# Tuning the Paramaters - Grid Search

An important aspect of XGBoost is tuning the parameters to make the parameters more successful in training the model. One way we can do this is by using Grid Search to search over every combination of specified parameter values and find out which one is the best. 

There are other ways to find the best parameters but we will just focus on Grid Search here.

For this specific example running the actual model only takes seconds but this Grid Search took about 8 minutes.

```{code-cell} ipython3
from sklearn.model_selection import GridSearchCV


params = { 'max_depth': [3,6,10],
           'learning_rate': [0.01, 0.05, 0.1],
           'n_estimators': [100, 500, 1000],
           'colsample_bytree': [0.3, 0.7]}

xgbc = xgb.XGBClassifier(objective ='binary:logistic',seed = 20)

clf = GridSearchCV(estimator=xgbc, 
                   param_grid=params,
                   scoring='neg_mean_squared_error', 
                   verbose=1)

clf.fit(x_train,y_train)
print("Best parameters:", clf.best_params_)
```


```{code-cell} ipython3
xg_cls = xgb.XGBClassifier(objective ='binary:logistic',seed = 20,colsample_bytree = .7, learning_rate = .05,
                max_depth = 3, n_estimators = 100)

xg_cls.fit(x_train,y_train)

y_pred = xg_cls.predict(x_test)

print("Accuracy:", metrics.accuracy_score(y_test, y_pred))
print(confusion_matrix(y_test, y_pred))
```
### Feature Importance

Here we see how much each variable is affecting the overall prediction

```{code-cell} ipython3
import matplotlib.pyplot as plt

xgb.plot_importance(xg_cls)
plt.rcParams['figure.figsize'] = [5, 5]
plt.show()
```
## Regression XGBoost Model

Almost exactly the same as the classification model except we are predicting the number of people injured instead of if someone was injured. 

The only difference is we use `xgb.XGBRegressor`

```{code-cell} ipython3
from sklearn.metrics import mean_squared_error

feature_cols2 = nyc_crash[['num_vehicles','Season','timeframe', 'BOROUGH', 'cfv1_1000']]
features2 = pd.get_dummies(feature_cols2)

y2 = nyc_crash.num_ppl_injured

data_dmatrix = xgb.DMatrix(data=features2,label=y)

x_train, x_test, y_train, y_test = train_test_split(features2, y2, 
                                                    test_size = 0.2, 
                                                    random_state = 12)

xg_reg = xgb.XGBRegressor(objective ='reg:squarederror')

xg_reg.fit(x_train,y_train)

y_pred = xg_reg.predict(x_test)

df2=pd.DataFrame({'Actual':y_test, 'Predicted':y_pred})
df2["diff"] = abs(df2["Actual"] - df2["Predicted"])
df2.sort_values(by=['diff'], inplace=True)

rmse = np.sqrt(mean_squared_error(y_test, y_pred))
print("RMSE: %f" % (rmse))

df2.head()
```


```{code-cell} ipython3
df2.sort_values(by=['diff'], ascending=False,inplace=True)
df2.head()
```
## Tuning the Parameters - Grid Search

```{code-cell} ipython3

from sklearn.model_selection import GridSearchCV


params = { 'max_depth': [3,6,10],
           'learning_rate': [0.01, 0.05, 0.1],
           'n_estimators': [100, 500, 1000],
           'colsample_bytree': [0.3, 0.7]}
xgbr = xgb.XGBRegressor(objective ='reg:squarederror',seed = 20)
clf = GridSearchCV(estimator=xgbr, 
                   param_grid=params,
                   scoring='neg_mean_squared_error', 
                   verbose=1)

clf.fit(x_train,y_train)
print("Best parameters:", clf.best_params_)
print("Lowest RMSE: ", (-clf.best_score_)**(1/2.0))
```


```{code-cell} ipython3
xg_reg = xgb.XGBRegressor(objective ='reg:squarederror', colsample_bytree = 0.7, learning_rate = 0.01,
                max_depth = 3, n_estimators = 500)

xg_reg.fit(x_train,y_train)

y_pred = xg_reg.predict(x_test)

df2=pd.DataFrame({'Actual':y_test, 'Predicted':y_pred})
df2["diff"] = abs(df2["Actual"] - df2["Predicted"])
df2.sort_values(by=['diff'], inplace=True)

rmse = np.sqrt(mean_squared_error(y_test, y_pred))
print("RMSE: %f" % (rmse))

df2.head()
```
### Feature Importance

```{code-cell} ipython3
xgb.plot_importance(xg_reg)
plt.rcParams['figure.figsize'] = [5, 5]
plt.show()
```
