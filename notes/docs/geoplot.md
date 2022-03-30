# Spatial visualization with Geoplot
Geoplot is an extenstion of Cartopy for performing geospatial
visualization. Geoplot provides a high level API for making 
such visualizations. It is also completely compatible with 
Cartopy and Matplotlib, so we can always dive down and use the 
tool from those packages if we need to.

Geoplot can be installed with `conda install geoplot -c conda-
forge` or `pip install geoplot`. 

This tutorial will also use the GeoPandas package, and 
extension of Pandas for working with geographic data. It can be
installed with `conda install -c conda-forge geopandas`. The
GeoPandas website doesn't reccomend installing with pip; if you
really want to, the GeoPandas website has a whole page 
dedicated to special instructions for getting all the the
required system dependencies installed for pip.

For this tutorial, we will making visualizations concerning US 
school districts. Shapefile showing the boundries of all school
districts in the US are available from the US Census Bureau at
https://nces.ed.gov/programs/edge/Geographic/DistrictBoundaries. Note that this shapefile is actually too large to be uploaded to github. (There are a lot of school districts in the US.) Consequently, I decided to create a new shapefile that only contained school districts in Connecticut.

We will also use a data set containing information on 
individual school districts available from the Urban Institute 
at https://datacatalog.urban.org/dataset/household-conditions-geographic-school-district.

First, let's load all of the required libraries and shapefiles:

```python
import geoplot as gplt
import geopandas as gpd
import geoplot.crs as gcrs
import pandas as pd
import numpy as np

ct_d = gpd.read_file("../data/districts/ct_districts.shp")
```

Here we use `gpd.read_file` from GeoPandas to read the 
shapefile, and save it in a variable called `ct_d`. Let's take a 
look at `ct_d`.

```python
ct_d.head()
```

Notice the `geometry` column. This is the column where 
GeoPandas stores the visual information of the school district,
which GeoPlot will use to plot. With the exception of the `geometry` column, this dataset is otherwise the same as an ordinary Pandas dataset, and all of the pandas operations will work on it as normal. For example, we could take a subset of the file, to obtain a smaller data set. we could then use the GeoPandas `to_file()` function to save this as a new shape file.

Let's start by plotting all school districts in  Connecticut 
using the `polyplot` function in Geoplot.

```python
%%capture --no-display

gplt.polyplot(ct_d);
```

We can use the `gplt.pointplot` function to plot points on the map.

GeoPandas comes with a number of built in datasets, one of 
which contains information on every city in the US. Let's use
`point_plot` to overlay the locations of all cities in CT over
the school district map, colored by population.


```python
%%capture --no-display
path = gplt.datasets.get_path("usa_cities")
usa_cities = gpd.read_file(path)
ct_cities = usa_cities[usa_cities.STATE=="CT"]

ax = gplt.polyplot(ct_d)
gplt.pointplot(ct_cities,
               hue = "POP_2010",
               ax = ax, 
               extent = ct_d.total_bounds,
               legend = True,
               cmap="Greens")
```

A choropleth is a geospacial visualization where geographic objects are colored according to some data. Let's create a make a choropleth where the school districts of Connecticut are colored by their poverty rate. First, let's input the data and do some basic cleaning, and merge it with the shapefile data.

```python
%%capture --no-display
df = pd.read_csv("../data/districts/NHGIS_District_data.csv")
df = df.rename(columns = {'Geographic School District' : 'NAME',
                    '% Poverty (SAIPE Estimate)' : 'pov'})
df_ct = df[df.State == "Connecticut"]
df_ct.pov = [int(x.split("%")[0]) for x in df_ct.pov]
df_ct = pd.merge(ct_d, df_ct, on="NAME")
```

Next, we use the `gplt.choropleth` function to construct the choropleth.

```python
%%capture --no-display
ax = gplt.polyplot(df_ct)
gplt.choropleth(df_ct, 
                hue="pov",
                cmap="Greens",
                legend=True,
                ax=ax)

```

We can also make a cartogram, which maps the variable of interest to the size of the object.

```python
%%capture --no-display
gplt.cartogram(df_ct,
              scale = "pov")
```

Note that in order to use Geoplot functions like `polyplot` and `choropleth`, the data must be formated as a geo dataframe. This can be done easily using `gpd.GeoDataFrame` function. Let's convert the crash data set into a geo dataframe and so we can plot it using Geoplot.

```python
boroughs = gpd.read_file("../data/nyc/geo_export_a1f96cd2-7ce6-44bb-9568-f3ee8bcba44a.shp")
crash = pd.read_csv("../data/nyc_mv_collisions_202201.csv")
crash[crash.LONGITUDE == 0] = np.nan
crash = crash[crash.LONGITUDE.notna()]

geocrash = gpd.GeoDataFrame(
    crash, 
    geometry = gpd.points_from_xy(crash.LONGITUDE, 
                                  crash.LATITUDE))
```

Now we can plot it over the boroughs map using `pointplot`.

```python
%%capture --no-display
ax = gplt.polyplot(boroughs)
gplt.pointplot(geocrash, alpha=.3, ax=ax)
```

Alternately, we can use can use the `gplt.kdeplot` function to create a heatmap showing the density of crashes.

```python
%%capture --no-display
ax = gplt.polyplot(boroughs)
gplt.kdeplot(geocrash, cmap="Reds", shade=True, 
             clip=boroughs, ax=ax)
```

```python

```
