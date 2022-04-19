# Stepwise Regression

In a stepwise regression, variables are added and removed from the model based on significance. You can have a forward selection stepwise which adds variables if they are statistically significant until all the variables outside the model are not significant, a backwards elimination stepwise regression which puts in all the variables and then removes those that are not statistically significant until only statistically significant ones remain, and a bidirectional elimination which both adds and removes until all the variables inside are significant AND all those outside are not significant.


```python
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np
import seaborn as sns
import statsmodels.api as sm
import warnings
warnings.filterwarnings('ignore')
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>CRASH DATE</th>
      <th>CRASH TIME</th>
      <th>BOROUGH</th>
      <th>ZIP CODE</th>
      <th>LATITUDE</th>
      <th>LONGITUDE</th>
      <th>LOCATION</th>
      <th>ON STREET NAME</th>
      <th>CROSS STREET NAME</th>
      <th>OFF STREET NAME</th>
      <th>...</th>
      <th>VEHICLE TYPE CODE 5</th>
      <th>lat</th>
      <th>long</th>
      <th>hour</th>
      <th>date</th>
      <th>BROOK</th>
      <th>BRONX</th>
      <th>MANHAT</th>
      <th>STATEN</th>
      <th>QUEENS</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>01/01/2022</td>
      <td>7:05</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>EAST 128 STREET</td>
      <td>3 AVENUE BRIDGE</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>01/01/2022</td>
      <td>14:43</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>40.769993</td>
      <td>-73.915825</td>
      <td>(40.769993, -73.915825)</td>
      <td>GRAND CENTRAL PKWY</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>1</td>
      <td>1</td>
      <td>14</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>01/01/2022</td>
      <td>21:20</td>
      <td>QUEENS</td>
      <td>11414.0</td>
      <td>40.657230</td>
      <td>-73.841380</td>
      <td>(40.65723, -73.84138)</td>
      <td>91 STREET</td>
      <td>160 AVENUE</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>1</td>
      <td>1</td>
      <td>21</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>01/01/2022</td>
      <td>4:30</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Southern parkway</td>
      <td>Jfk expressway</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>4</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>01/01/2022</td>
      <td>7:57</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>WESTCHESTER AVENUE</td>
      <td>SHERIDAN EXPRESSWAY</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>7</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 38 columns</p>
</div>



### Data scrubbing


```python
df = pd.read_csv("../data/nyc_mv_collisions_202201.csv")

zeros = []
for i in range(len(df)):
    zeros.append(0)
    
df["lat"] = zeros
for i in range(len(df)):
    if df["LATITUDE"][i] > 0 or df["LATITUDE"][i] < 0:
        df["lat"][i] = 1
    else:
        df["lat"][i] = 0

df["long"] = zeros
for i in range(len(df)):
    if df["LATITUDE"][i] > 0 or df["LONGITUDE"][i] < 0:
        df["long"][i] = 1
    else:
        df["long"][i] = 0
    
df["hour"] = zeros
for i in range(len(df)):
    if (df["CRASH TIME"][i])[1:2] == ':':
        df["hour"][i] = str(df["CRASH TIME"][i])[0:1]
    else:
        df["hour"][i] = str(df["CRASH TIME"][i])[0:2]
    cap = int(df["hour"][i])
    df["hour"][i] = cap

df["date"] = zeros
for i in range(len(df)):
    df["date"][i] = str(df["CRASH DATE"][i])[3:5]
    cap = int(df["date"][i])
    df["date"][i] = cap
    
df["BROOK"] = zeros
df["BRONX"] = zeros
df["MANHAT"] = zeros
df["STATEN"] = zeros
df["QUEENS"] = zeros

for i in range(len(df)):
    if df["BOROUGH"][i] == "NaN":
        cat = 1
    elif df["BOROUGH"][i] == 'BROOKLYN':
        df["BROOK"][i] = 1
    elif df["BOROUGH"][i] == 'BRONX':
        df["BRONX"][i] = 1
    elif df["BOROUGH"][i] == 'MANHATTAN':
        df["MANHAT"][i] = 1
    elif df["BOROUGH"][i] == 'STATEN ISLAND':
        df["STATEN"][i] = 1
    elif df["BOROUGH"][i] == 'QUEENS':
        df["QUEENS"][i] = 1
```


```python
x_columns = ["date", "hour", "BROOK", "BRONX", "STATEN", "QUEENS", "MANHAT", "NUMBER OF PERSONS KILLED", "lat"]
y = df["NUMBER OF PERSONS INJURED"]
```


```python
def get_stats():
    x = df[x_columns]
    results = sm.OLS(y, x).fit()
    print(results.summary())

def rem_high():
    x = df[x_columns]
    results = sm.OLS(y, x).fit()
    spot = 0
    for i in range(len(results.pvalues)):
        if results.pvalues[spot] < results.pvalues[i]:
            spot = i
    if results.pvalues[spot] > .05:
        x_columns.pop(spot)
    print(results.summary())
```


```python
get_stats()
rem_high()
```

          date  hour  BROOK  BRONX  STATEN  QUEENS  MANHAT  \
    0        1     7      0      0       0       0       0   
    1        1    14      0      0       0       0       0   
    2        1    21      0      0       0       1       0   
    3        1     4      0      0       0       0       0   
    4        1     7      0      0       0       0       0   
    ...    ...   ...    ...    ...     ...     ...     ...   
    7654    31    12      0      0       0       1       0   
    7655    31    21      1      0       0       0       0   
    7656    31     9      1      0       0       0       0   
    7657    31     6      0      1       0       0       0   
    7658    31    14      0      0       0       1       0   
    
          NUMBER OF PERSONS KILLED  lat  
    0                            0    0  
    1                            0    1  
    2                            0    1  
    3                            0    0  
    4                            0    0  
    ...                        ...  ...  
    7654                         0    1  
    7655                         0    1  
    7656                         0    1  
    7657                         0    1  
    7658                         0    1  
    
    [7659 rows x 9 columns]
    


```python
rem_high()
```

                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.226
    Model:                                   OLS   Adj. R-squared (uncentered):              0.225
    Method:                        Least Squares   F-statistic:                              279.0
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                        0.00
    Time:                               03:31:10   Log-Likelihood:                         -8476.1
    No. Observations:                       7659   AIC:                                  1.697e+04
    Df Residuals:                           7651   BIC:                                  1.702e+04
    Df Model:                                  8                                                  
    Covariance Type:                   nonrobust                                                  
    ============================================================================================
                                   coef    std err          t      P>|t|      [0.025      0.975]
    --------------------------------------------------------------------------------------------
    date                         0.0018      0.001      2.109      0.035       0.000       0.003
    hour                         0.0139      0.001     11.350      0.000       0.011       0.016
    BROOK                       -0.0278      0.022     -1.279      0.201      -0.070       0.015
    STATEN                      -0.0293      0.053     -0.554      0.580      -0.133       0.074
    QUEENS                      -0.0682      0.023     -2.918      0.004      -0.114      -0.022
    MANHAT                      -0.0622      0.030     -2.084      0.037      -0.121      -0.004
    NUMBER OF PERSONS KILLED     0.2820      0.173      1.633      0.103      -0.057       0.621
    lat                          0.2189      0.022      9.965      0.000       0.176       0.262
    ==============================================================================
    Omnibus:                     4117.098   Durbin-Watson:                   1.964
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            35735.067
    Skew:                           2.450   Prob(JB):                         0.00
    Kurtosis:                      12.379   Cond. No.                         452.
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    

### The Bronx is removed


```python
rem_high()
```

                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.226
    Model:                                   OLS   Adj. R-squared (uncentered):              0.225
    Method:                        Least Squares   F-statistic:                              318.8
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                        0.00
    Time:                               03:31:12   Log-Likelihood:                         -8476.3
    No. Observations:                       7659   AIC:                                  1.697e+04
    Df Residuals:                           7652   BIC:                                  1.702e+04
    Df Model:                                  7                                                  
    Covariance Type:                   nonrobust                                                  
    ============================================================================================
                                   coef    std err          t      P>|t|      [0.025      0.975]
    --------------------------------------------------------------------------------------------
    date                         0.0018      0.001      2.108      0.035       0.000       0.003
    hour                         0.0139      0.001     11.340      0.000       0.011       0.016
    BROOK                       -0.0261      0.022     -1.214      0.225      -0.068       0.016
    QUEENS                      -0.0666      0.023     -2.871      0.004      -0.112      -0.021
    MANHAT                      -0.0605      0.030     -2.039      0.042      -0.119      -0.002
    NUMBER OF PERSONS KILLED     0.2826      0.173      1.636      0.102      -0.056       0.621
    lat                          0.2175      0.022      9.970      0.000       0.175       0.260
    ==============================================================================
    Omnibus:                     4118.495   Durbin-Watson:                   1.964
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            35771.998
    Skew:                           2.451   Prob(JB):                         0.00
    Kurtosis:                      12.384   Cond. No.                         452.
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    

### Staten island is removed


```python
rem_high()
```

                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.226
    Model:                                   OLS   Adj. R-squared (uncentered):              0.225
    Method:                        Least Squares   F-statistic:                              371.7
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                        0.00
    Time:                               03:31:15   Log-Likelihood:                         -8477.0
    No. Observations:                       7659   AIC:                                  1.697e+04
    Df Residuals:                           7653   BIC:                                  1.701e+04
    Df Model:                                  6                                                  
    Covariance Type:                   nonrobust                                                  
    ============================================================================================
                                   coef    std err          t      P>|t|      [0.025      0.975]
    --------------------------------------------------------------------------------------------
    date                         0.0018      0.001      2.072      0.038    9.57e-05       0.003
    hour                         0.0138      0.001     11.301      0.000       0.011       0.016
    QUEENS                      -0.0579      0.022     -2.624      0.009      -0.101      -0.015
    MANHAT                      -0.0518      0.029     -1.798      0.072      -0.108       0.005
    NUMBER OF PERSONS KILLED     0.2818      0.173      1.632      0.103      -0.057       0.620
    lat                          0.2098      0.021     10.049      0.000       0.169       0.251
    ==============================================================================
    Omnibus:                     4121.849   Durbin-Watson:                   1.964
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            35880.243
    Skew:                           2.453   Prob(JB):                         0.00
    Kurtosis:                      12.400   Cond. No.                         452.
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    

### Brooklyn is removed


```python
rem_high()
```

                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.225
    Model:                                   OLS   Adj. R-squared (uncentered):              0.225
    Method:                        Least Squares   F-statistic:                              445.4
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                        0.00
    Time:                               03:31:18   Log-Likelihood:                         -8478.3
    No. Observations:                       7659   AIC:                                  1.697e+04
    Df Residuals:                           7654   BIC:                                  1.700e+04
    Df Model:                                  5                                                  
    Covariance Type:                   nonrobust                                                  
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    date           0.0018      0.001      2.084      0.037       0.000       0.003
    hour           0.0138      0.001     11.296      0.000       0.011       0.016
    QUEENS        -0.0578      0.022     -2.621      0.009      -0.101      -0.015
    MANHAT        -0.0516      0.029     -1.791      0.073      -0.108       0.005
    lat            0.2102      0.021     10.071      0.000       0.169       0.251
    ==============================================================================
    Omnibus:                     4130.912   Durbin-Watson:                   1.964
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            36165.015
    Skew:                           2.458   Prob(JB):                         0.00
    Kurtosis:                      12.443   Cond. No.                         77.7
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    

### Persons Killed is removed


```python
rem_high()
```

                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.225
    Model:                                   OLS   Adj. R-squared (uncentered):              0.225
    Method:                        Least Squares   F-statistic:                              555.8
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                        0.00
    Time:                               03:31:21   Log-Likelihood:                         -8479.9
    No. Observations:                       7659   AIC:                                  1.697e+04
    Df Residuals:                           7655   BIC:                                  1.700e+04
    Df Model:                                  4                                                  
    Covariance Type:                   nonrobust                                                  
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    date           0.0017      0.001      2.036      0.042    6.47e-05       0.003
    hour           0.0137      0.001     11.253      0.000       0.011       0.016
    QUEENS        -0.0515      0.022     -2.365      0.018      -0.094      -0.009
    lat            0.2052      0.021      9.918      0.000       0.165       0.246
    ==============================================================================
    Omnibus:                     4134.561   Durbin-Watson:                   1.965
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            36246.070
    Skew:                           2.460   Prob(JB):                         0.00
    Kurtosis:                      12.454   Cond. No.                         60.8
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    

### Manhattan is removed

From here we can see all variables are under the .05 threshold established previously. Our formula would come out to be <br>
y = date * .0017 + hour * .0137 - QUEENS * .0515 + lat * .2052


```python
x_col = []
used = 0
def crunch_num():
    p_val = 1
    location = 0
    for i in range(len(x_columns)):
        if used == 0:
            x = df[x_columns[i]]
        results = sm.OLS(y, x).fit()
        print(p_val)
        print(results.pvalues[len(x_col)])
        if (p_val > results.pvalues[len(x_col)]):
            p_val = results.pvalues[len(x_col)]
            location = i
        print(results.summary())
    if (p_val <.05):
        print(location)
        x_col.append(x_columns[location])
        x_columns.remove(x_columns[location])
```


```python
crunch_num()
```

    1
    1.4323814844141276e-306
                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.167
    Model:                                   OLS   Adj. R-squared (uncentered):              0.167
    Method:                        Least Squares   F-statistic:                              1537.
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                   1.43e-306
    Time:                               05:18:18   Log-Likelihood:                         -8755.8
    No. Observations:                       7659   AIC:                                  1.751e+04
    Df Residuals:                           7658   BIC:                                  1.752e+04
    Df Model:                                  1                                                  
    Covariance Type:                   nonrobust                                                  
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    date           0.0185      0.000     39.207      0.000       0.018       0.019
    ==============================================================================
    Omnibus:                     3858.755   Durbin-Watson:                   1.810
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            29521.077
    Skew:                           2.303   Prob(JB):                         0.00
    Kurtosis:                      11.444   Cond. No.                         1.00
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    1.4323814844141276e-306
    0.0
                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.208
    Model:                                   OLS   Adj. R-squared (uncentered):              0.208
    Method:                        Least Squares   F-statistic:                              2014.
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                        0.00
    Time:                               05:18:18   Log-Likelihood:                         -8562.1
    No. Observations:                       7659   AIC:                                  1.713e+04
    Df Residuals:                           7658   BIC:                                  1.713e+04
    Df Model:                                  1                                                  
    Covariance Type:                   nonrobust                                                  
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    hour           0.0271      0.001     44.880      0.000       0.026       0.028
    ==============================================================================
    Omnibus:                     3979.018   Durbin-Watson:                   1.957
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            33128.310
    Skew:                           2.360   Prob(JB):                         0.00
    Kurtosis:                      12.030   Cond. No.                         1.00
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    0.0
    4.269856558800235e-90
                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.052
    Model:                                   OLS   Adj. R-squared (uncentered):              0.051
    Method:                        Least Squares   F-statistic:                              416.0
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                    4.27e-90
    Time:                               05:18:18   Log-Likelihood:                         -9253.7
    No. Observations:                       7659   AIC:                                  1.851e+04
    Df Residuals:                           7658   BIC:                                  1.852e+04
    Df Model:                                  1                                                  
    Covariance Type:                   nonrobust                                                  
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    BROOK          0.3956      0.019     20.397      0.000       0.358       0.434
    ==============================================================================
    Omnibus:                     3929.307   Durbin-Watson:                   1.678
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            32281.384
    Skew:                           2.327   Prob(JB):                         0.00
    Kurtosis:                      11.916   Cond. No.                         1.00
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    0.0
    2.393436485478676e-52
                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.030
    Model:                                   OLS   Adj. R-squared (uncentered):              0.030
    Method:                        Least Squares   F-statistic:                              235.4
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                    2.39e-52
    Time:                               05:18:18   Log-Likelihood:                         -9340.3
    No. Observations:                       7659   AIC:                                  1.868e+04
    Df Residuals:                           7658   BIC:                                  1.869e+04
    Df Model:                                  1                                                  
    Covariance Type:                   nonrobust                                                  
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    BRONX          0.4082      0.027     15.342      0.000       0.356       0.460
    ==============================================================================
    Omnibus:                     3987.014   Durbin-Watson:                   1.601
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            32899.862
    Skew:                           2.370   Prob(JB):                         0.00
    Kurtosis:                      11.979   Cond. No.                         1.00
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    0.0
    4.369430499033895e-11
                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.006
    Model:                                   OLS   Adj. R-squared (uncentered):              0.006
    Method:                        Least Squares   F-statistic:                              43.57
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                    4.37e-11
    Time:                               05:18:18   Log-Likelihood:                         -9434.6
    No. Observations:                       7659   AIC:                                  1.887e+04
    Df Residuals:                           7658   BIC:                                  1.888e+04
    Df Model:                                  1                                                  
    Covariance Type:                   nonrobust                                                  
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    STATEN         0.3842      0.058      6.601      0.000       0.270       0.498
    ==============================================================================
    Omnibus:                     4179.444   Durbin-Watson:                   1.527
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            37164.558
    Skew:                           2.489   Prob(JB):                         0.00
    Kurtosis:                      12.574   Cond. No.                         1.00
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    0.0
    6.945005855138339e-56
                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.032
    Model:                                   OLS   Adj. R-squared (uncentered):              0.032
    Method:                        Least Squares   F-statistic:                              252.1
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                    6.95e-56
    Time:                               05:18:18   Log-Likelihood:                         -9332.2
    No. Observations:                       7659   AIC:                                  1.867e+04
    Df Residuals:                           7658   BIC:                                  1.867e+04
    Df Model:                                  1                                                  
    Covariance Type:                   nonrobust                                                  
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    QUEENS         0.3482      0.022     15.878      0.000       0.305       0.391
    ==============================================================================
    Omnibus:                     3969.257   Durbin-Watson:                   1.627
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            32316.549
    Skew:                           2.362   Prob(JB):                         0.00
    Kurtosis:                      11.886   Cond. No.                         1.00
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    0.0
    4.461967379856326e-32
                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.018
    Model:                                   OLS   Adj. R-squared (uncentered):              0.018
    Method:                        Least Squares   F-statistic:                              140.3
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                    4.46e-32
    Time:                               05:18:18   Log-Likelihood:                         -9386.8
    No. Observations:                       7659   AIC:                                  1.878e+04
    Df Residuals:                           7658   BIC:                                  1.878e+04
    Df Model:                                  1                                                  
    Covariance Type:                   nonrobust                                                  
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    MANHAT         0.3596      0.030     11.843      0.000       0.300       0.419
    ==============================================================================
    Omnibus:                     4087.592   Durbin-Watson:                   1.584
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            35155.863
    Skew:                           2.431   Prob(JB):                         0.00
    Kurtosis:                      12.302   Cond. No.                         1.00
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    0.0
    0.0018205759792527649
                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.001
    Model:                                   OLS   Adj. R-squared (uncentered):              0.001
    Method:                        Least Squares   F-statistic:                              9.729
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                     0.00182
    Time:                               05:18:18   Log-Likelihood:                         -9451.4
    No. Observations:                       7659   AIC:                                  1.890e+04
    Df Residuals:                           7658   BIC:                                  1.891e+04
    Df Model:                                  1                                                  
    Covariance Type:                   nonrobust                                                  
    ============================================================================================
                                   coef    std err          t      P>|t|      [0.025      0.975]
    --------------------------------------------------------------------------------------------
    NUMBER OF PERSONS KILLED     0.6111      0.196      3.119      0.002       0.227       0.995
    ==============================================================================
    Omnibus:                     4194.263   Durbin-Watson:                   1.510
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            37329.833
    Skew:                           2.501   Prob(JB):                         0.00
    Kurtosis:                      12.590   Cond. No.                         1.00
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    0.0
    0.0
                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.210
    Model:                                   OLS   Adj. R-squared (uncentered):              0.210
    Method:                        Least Squares   F-statistic:                              2032.
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                        0.00
    Time:                               05:18:18   Log-Likelihood:                         -8555.0
    No. Observations:                       7659   AIC:                                  1.711e+04
    Df Residuals:                           7658   BIC:                                  1.712e+04
    Df Model:                                  1                                                  
    Covariance Type:                   nonrobust                                                  
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    lat            0.3962      0.009     45.077      0.000       0.379       0.413
    ==============================================================================
    Omnibus:                     4119.911   Durbin-Watson:                   1.965
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            35447.666
    Skew:                           2.456   Prob(JB):                         0.00
    Kurtosis:                      12.324   Cond. No.                         1.00
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    1
    

### Start by adding Hour


```python
def crunch_nums():
    p_val = 1
    location = 0
    for i in range(len(x_columns)-len(x_col)):
        x_col.append(x_columns[i])
        x = df[x_col]
        results = sm.OLS(y, x).fit()
        print(results.summary())
        x_col.pop(len(x_col)-1)
        print(p_val)
        print(results.pvalues[len(x_col)])
        if (p_val > results.pvalues[len(x_col)]):
            p_val = results.pvalues[len(x_col)]
            location = i
    if (p_val <.05):
        print(location)
        x_col.append(x_columns[location])
        x_columns.remove(x_columns[int(location)])
```


```python
crunch_nums()
```

                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.215
    Model:                                   OLS   Adj. R-squared (uncentered):              0.215
    Method:                        Least Squares   F-statistic:                              1049.
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                        0.00
    Time:                               05:18:31   Log-Likelihood:                         -8529.0
    No. Observations:                       7659   AIC:                                  1.706e+04
    Df Residuals:                           7657   BIC:                                  1.708e+04
    Df Model:                                  2                                                  
    Covariance Type:                   nonrobust                                                  
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    hour           0.0210      0.001     21.614      0.000       0.019       0.023
    date           0.0060      0.001      8.154      0.000       0.005       0.007
    ==============================================================================
    Omnibus:                     4031.194   Durbin-Watson:                   1.943
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            33946.805
    Skew:                           2.396   Prob(JB):                         0.00
    Kurtosis:                      12.133   Cond. No.                         3.01
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    1
    4.0666379515353367e-16
                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.209
    Model:                                   OLS   Adj. R-squared (uncentered):              0.209
    Method:                        Least Squares   F-statistic:                              1013.
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                        0.00
    Time:                               05:18:31   Log-Likelihood:                         -8557.5
    No. Observations:                       7659   AIC:                                  1.712e+04
    Df Residuals:                           7657   BIC:                                  1.713e+04
    Df Model:                                  2                                                  
    Covariance Type:                   nonrobust                                                  
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    hour           0.0263      0.001     39.070      0.000       0.025       0.028
    BROOK          0.0593      0.020      3.011      0.003       0.021       0.098
    ==============================================================================
    Omnibus:                     3992.980   Durbin-Watson:                   1.959
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            33490.382
    Skew:                           2.368   Prob(JB):                         0.00
    Kurtosis:                      12.084   Cond. No.                         32.6
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    4.0666379515353367e-16
    0.0026154978758844666
                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.209
    Model:                                   OLS   Adj. R-squared (uncentered):              0.209
    Method:                        Least Squares   F-statistic:                              1012.
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                        0.00
    Time:                               05:18:31   Log-Likelihood:                         -8558.0
    No. Observations:                       7659   AIC:                                  1.712e+04
    Df Residuals:                           7657   BIC:                                  1.713e+04
    Df Model:                                  2                                                  
    Covariance Type:                   nonrobust                                                  
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    hour           0.0266      0.001     41.659      0.000       0.025       0.028
    BRONX          0.0722      0.025      2.847      0.004       0.022       0.122
    ==============================================================================
    Omnibus:                     3981.961   Durbin-Watson:                   1.957
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            33171.456
    Skew:                           2.362   Prob(JB):                         0.00
    Kurtosis:                      12.035   Cond. No.                         41.9
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    4.0666379515353367e-16
    0.004426633639098556
                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.208
    Model:                                   OLS   Adj. R-squared (uncentered):              0.208
    Method:                        Least Squares   F-statistic:                              1007.
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                        0.00
    Time:                               05:18:31   Log-Likelihood:                         -8561.8
    No. Observations:                       7659   AIC:                                  1.713e+04
    Df Residuals:                           7657   BIC:                                  1.714e+04
    Df Model:                                  2                                                  
    Covariance Type:                   nonrobust                                                  
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    hour           0.0271      0.001     44.270      0.000       0.026       0.028
    STATEN         0.0367      0.053      0.699      0.484      -0.066       0.140
    ==============================================================================
    Omnibus:                     3980.742   Durbin-Watson:                   1.957
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            33169.872
    Skew:                           2.361   Prob(JB):                         0.00
    Kurtosis:                      12.036   Cond. No.                         86.9
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    4.0666379515353367e-16
    0.4843669202067119
                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.208
    Model:                                   OLS   Adj. R-squared (uncentered):              0.208
    Method:                        Least Squares   F-statistic:                              1007.
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                        0.00
    Time:                               05:18:31   Log-Likelihood:                         -8561.9
    No. Observations:                       7659   AIC:                                  1.713e+04
    Df Residuals:                           7657   BIC:                                  1.714e+04
    Df Model:                                  2                                                  
    Covariance Type:                   nonrobust                                                  
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    hour           0.0270      0.001     41.304      0.000       0.026       0.028
    QUEENS         0.0114      0.021      0.532      0.594      -0.031       0.053
    ==============================================================================
    Omnibus:                     3980.405   Durbin-Watson:                   1.957
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            33143.808
    Skew:                           2.361   Prob(JB):                         0.00
    Kurtosis:                      12.031   Cond. No.                         35.5
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    4.0666379515353367e-16
    0.5944459717669641
                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.208
    Model:                                   OLS   Adj. R-squared (uncentered):              0.208
    Method:                        Least Squares   F-statistic:                              1007.
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                        0.00
    Time:                               05:18:31   Log-Likelihood:                         -8562.0
    No. Observations:                       7659   AIC:                                  1.713e+04
    Df Residuals:                           7657   BIC:                                  1.714e+04
    Df Model:                                  2                                                  
    Covariance Type:                   nonrobust                                                  
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    hour           0.0271      0.001     42.898      0.000       0.026       0.028
    MANHAT         0.0115      0.028      0.405      0.686      -0.044       0.067
    ==============================================================================
    Omnibus:                     3980.428   Durbin-Watson:                   1.958
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            33157.904
    Skew:                           2.361   Prob(JB):                         0.00
    Kurtosis:                      12.034   Cond. No.                         47.0
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    4.0666379515353367e-16
    0.6858402170710972
                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.209
    Model:                                   OLS   Adj. R-squared (uncentered):              0.208
    Method:                        Least Squares   F-statistic:                              1009.
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                        0.00
    Time:                               05:18:31   Log-Likelihood:                         -8560.3
    No. Observations:                       7659   AIC:                                  1.712e+04
    Df Residuals:                           7657   BIC:                                  1.714e+04
    Df Model:                                  2                                                  
    Covariance Type:                   nonrobust                                                  
    ============================================================================================
                                   coef    std err          t      P>|t|      [0.025      0.975]
    --------------------------------------------------------------------------------------------
    hour                         0.0271      0.001     44.790      0.000       0.026       0.028
    NUMBER OF PERSONS KILLED     0.3296      0.175      1.888      0.059      -0.013       0.672
    ==============================================================================
    Omnibus:                     3969.378   Durbin-Watson:                   1.957
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            32855.187
    Skew:                           2.355   Prob(JB):                         0.00
    Kurtosis:                      11.987   Cond. No.                         289.
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    4.0666379515353367e-16
    0.059032381034271476
    0
    

### Add Date


```python
crunch_nums()
```

                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.215
    Model:                                   OLS   Adj. R-squared (uncentered):              0.215
    Method:                        Least Squares   F-statistic:                              1049.
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                        0.00
    Time:                               05:12:14   Log-Likelihood:                         -8529.0
    No. Observations:                       7659   AIC:                                  1.706e+04
    Df Residuals:                           7657   BIC:                                  1.708e+04
    Df Model:                                  2                                                  
    Covariance Type:                   nonrobust                                                  
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    hour           0.0210      0.001     21.614      0.000       0.019       0.023
    date           0.0060      0.001      8.154      0.000       0.005       0.007
    ==============================================================================
    Omnibus:                     4031.194   Durbin-Watson:                   1.943
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            33946.805
    Skew:                           2.396   Prob(JB):                         0.00
    Kurtosis:                      12.133   Cond. No.                         3.01
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    1
    4.0666379515353367e-16
                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.209
    Model:                                   OLS   Adj. R-squared (uncentered):              0.209
    Method:                        Least Squares   F-statistic:                              1013.
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                        0.00
    Time:                               05:12:14   Log-Likelihood:                         -8557.5
    No. Observations:                       7659   AIC:                                  1.712e+04
    Df Residuals:                           7657   BIC:                                  1.713e+04
    Df Model:                                  2                                                  
    Covariance Type:                   nonrobust                                                  
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    hour           0.0263      0.001     39.070      0.000       0.025       0.028
    BROOK          0.0593      0.020      3.011      0.003       0.021       0.098
    ==============================================================================
    Omnibus:                     3992.980   Durbin-Watson:                   1.959
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            33490.382
    Skew:                           2.368   Prob(JB):                         0.00
    Kurtosis:                      12.084   Cond. No.                         32.6
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    4.0666379515353367e-16
    0.0026154978758844666
                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.209
    Model:                                   OLS   Adj. R-squared (uncentered):              0.209
    Method:                        Least Squares   F-statistic:                              1012.
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                        0.00
    Time:                               05:12:14   Log-Likelihood:                         -8558.0
    No. Observations:                       7659   AIC:                                  1.712e+04
    Df Residuals:                           7657   BIC:                                  1.713e+04
    Df Model:                                  2                                                  
    Covariance Type:                   nonrobust                                                  
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    hour           0.0266      0.001     41.659      0.000       0.025       0.028
    BRONX          0.0722      0.025      2.847      0.004       0.022       0.122
    ==============================================================================
    Omnibus:                     3981.961   Durbin-Watson:                   1.957
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            33171.456
    Skew:                           2.362   Prob(JB):                         0.00
    Kurtosis:                      12.035   Cond. No.                         41.9
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    4.0666379515353367e-16
    0.004426633639098556
                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.208
    Model:                                   OLS   Adj. R-squared (uncentered):              0.208
    Method:                        Least Squares   F-statistic:                              1007.
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                        0.00
    Time:                               05:12:14   Log-Likelihood:                         -8561.8
    No. Observations:                       7659   AIC:                                  1.713e+04
    Df Residuals:                           7657   BIC:                                  1.714e+04
    Df Model:                                  2                                                  
    Covariance Type:                   nonrobust                                                  
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    hour           0.0271      0.001     44.270      0.000       0.026       0.028
    STATEN         0.0367      0.053      0.699      0.484      -0.066       0.140
    ==============================================================================
    Omnibus:                     3980.742   Durbin-Watson:                   1.957
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            33169.872
    Skew:                           2.361   Prob(JB):                         0.00
    Kurtosis:                      12.036   Cond. No.                         86.9
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    4.0666379515353367e-16
    0.4843669202067119
                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.208
    Model:                                   OLS   Adj. R-squared (uncentered):              0.208
    Method:                        Least Squares   F-statistic:                              1007.
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                        0.00
    Time:                               05:12:14   Log-Likelihood:                         -8561.9
    No. Observations:                       7659   AIC:                                  1.713e+04
    Df Residuals:                           7657   BIC:                                  1.714e+04
    Df Model:                                  2                                                  
    Covariance Type:                   nonrobust                                                  
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    hour           0.0270      0.001     41.304      0.000       0.026       0.028
    QUEENS         0.0114      0.021      0.532      0.594      -0.031       0.053
    ==============================================================================
    Omnibus:                     3980.405   Durbin-Watson:                   1.957
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            33143.808
    Skew:                           2.361   Prob(JB):                         0.00
    Kurtosis:                      12.031   Cond. No.                         35.5
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    4.0666379515353367e-16
    0.5944459717669641
                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.208
    Model:                                   OLS   Adj. R-squared (uncentered):              0.208
    Method:                        Least Squares   F-statistic:                              1007.
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                        0.00
    Time:                               05:12:14   Log-Likelihood:                         -8562.0
    No. Observations:                       7659   AIC:                                  1.713e+04
    Df Residuals:                           7657   BIC:                                  1.714e+04
    Df Model:                                  2                                                  
    Covariance Type:                   nonrobust                                                  
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    hour           0.0271      0.001     42.898      0.000       0.026       0.028
    MANHAT         0.0115      0.028      0.405      0.686      -0.044       0.067
    ==============================================================================
    Omnibus:                     3980.428   Durbin-Watson:                   1.958
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            33157.904
    Skew:                           2.361   Prob(JB):                         0.00
    Kurtosis:                      12.034   Cond. No.                         47.0
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    4.0666379515353367e-16
    0.6858402170710972
                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.209
    Model:                                   OLS   Adj. R-squared (uncentered):              0.208
    Method:                        Least Squares   F-statistic:                              1009.
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                        0.00
    Time:                               05:12:14   Log-Likelihood:                         -8560.3
    No. Observations:                       7659   AIC:                                  1.712e+04
    Df Residuals:                           7657   BIC:                                  1.714e+04
    Df Model:                                  2                                                  
    Covariance Type:                   nonrobust                                                  
    ============================================================================================
                                   coef    std err          t      P>|t|      [0.025      0.975]
    --------------------------------------------------------------------------------------------
    hour                         0.0271      0.001     44.790      0.000       0.026       0.028
    NUMBER OF PERSONS KILLED     0.3296      0.175      1.888      0.059      -0.013       0.672
    ==============================================================================
    Omnibus:                     3969.378   Durbin-Watson:                   1.957
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            32855.187
    Skew:                           2.355   Prob(JB):                         0.00
    Kurtosis:                      11.987   Cond. No.                         289.
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    4.0666379515353367e-16
    0.059032381034271476
    

### Add Bronx


```python
crunch_nums()
```

                                        OLS Regression Results                                    
    ==============================================================================================
    Dep. Variable:     NUMBER OF PERSONS INJURED   R-squared (uncentered):                   0.216
    Model:                                   OLS   Adj. R-squared (uncentered):              0.216
    Method:                        Least Squares   F-statistic:                              421.9
    Date:                       Tue, 19 Apr 2022   Prob (F-statistic):                        0.00
    Time:                               05:19:25   Log-Likelihood:                         -8524.1
    No. Observations:                       7659   AIC:                                  1.706e+04
    Df Residuals:                           7654   BIC:                                  1.709e+04
    Df Model:                                  5                                                  
    Covariance Type:                   nonrobust                                                  
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    hour           0.0202      0.001     20.088      0.000       0.018       0.022
    date           0.0056      0.001      7.367      0.000       0.004       0.007
    BRONX          0.0624      0.026      2.412      0.016       0.012       0.113
    BROOK          0.0472      0.020      2.333      0.020       0.008       0.087
    STATEN         0.0417      0.053      0.793      0.428      -0.061       0.145
    ==============================================================================
    Omnibus:                     4043.897   Durbin-Watson:                   1.945
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            34284.761
    Skew:                           2.403   Prob(JB):                         0.00
    Kurtosis:                      12.184   Cond. No.                         137.
    ==============================================================================
    
    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    1
    0.42787080095123353
    

### Add Brooklyn

In the new model we have Hour, Date, Bronx, and Brooklyn. A very different outcome than we saw with the other style of selection
