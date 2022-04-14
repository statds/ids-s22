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
(Tensorflow)=

# Tensorflow

Tensorflow is a software library commonly used for developing machine learning applications 
including detecting objects, recognizing images, and creating sequential models.


Offers:
	- building and training models with Keras
	- impressive graphing capabilities
	- supports powerful add on libraries
	- good for large projects and complex work flows
	- share trained models without including the full code

Downsides:
	- slower than similar programs like PyTorch	
	- only fully supports Python

## Background

Tensor = multidimensional array, where
	- n-d tensor = n indicies to access single value of interest
		- 'n = 0': scalar, 'n = 1': vector, etc.

Flow - successive execution of an operation through n-d tensor
	- dataflow graphs - structures describing how data moves through graph/series


## Installation and Python Implementation

'pip install tensorflow'




```{code-cell} ipython3
import pandas as pd
from sklearn.model_selection import train_test_split

url = \
"https://raw.githubusercontent.com/statds/ids-s22/main/notes/data/nyc_mv_collisions_202201.csv"
    
nyc_crash = pd.read_csv(url)

nyc_crash.columns
```

```{code-cell} ipython3
nyc_crash[nyc_crash['CONTRIBUTING FACTOR VEHICLE 1'] == 'Driver Inattention/Distraction']
```

```{code-cell} ipython3
nyc_crash['DISTRACTED DRIVING'] = [1 if x == 'Driver Inattention/Distraction' else 0 
                                   for x in nyc_crash['CONTRIBUTING FACTOR VEHICLE 1']]

x = pd.get_dummies(nyc_crash.drop(['ZIP CODE', 'LATITUDE', 'LONGITUDE', 'COLLISION_ID', 'LOCATION',
                                   'CRASH DATE', 'CRASH TIME', 'ON STREET NAME', 'CROSS STREET NAME', 
                                   'OFF STREET NAME', 'VEHICLE TYPE CODE 1', 'VEHICLE TYPE CODE 2', 
                                  'VEHICLE TYPE CODE 3', 'VEHICLE TYPE CODE 4', 'VEHICLE TYPE CODE 5', 
                                  'CONTRIBUTING FACTOR VEHICLE 2', 'CONTRIBUTING FACTOR VEHICLE 3', 
                                  'CONTRIBUTING FACTOR VEHICLE 4', 'CONTRIBUTING FACTOR VEHICLE 5'], axis = 1))

y = nyc_crash['DISTRACTED DRIVING']
```

```{code-cell} ipython3
x_train, x_test, y_train, y_test = train_test_split(x, y, test_size = .2)
```

```{code-cell} ipython3
from tensorflow.keras.models import Sequential, load_model
from tensorflow.keras.layers import Dense
from sklearn.metrics import accuracy_score

# Sequential Models
#    - one input tensor to one output tensor

ddriving = Sequential()
```
