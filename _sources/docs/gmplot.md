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

# GM Plot

To install `gmplot`, we can use pip by running pip or conda

`pip install gmplot`

`conda install -c mlgill gmplot`

After installing the package, we can now use it in Jupyter Notebook. `gmplot`'s most unique use is it's ability to save geographical coordinates on Google Maps to a local file. Additionally, it has a similar interface to matplotlib in regards to its generation of the HTML to deliver all the additional data on top of Google Maps.

First, we have to `import gmplot`

``` {code cell} 
import gmplot
```

## Basic Plot

After doing so, we can generate a simple map using `gmplot.GoogleMapPlotter`. This will create an `.html` file. I saved mine to Jupyter Notebook but you can set your path to save it to anywhere you choose.

```{code cell}
#first two arguments are longitude and latitude and the third one is zoom resolution
gmap = gmplot.GoogleMapPlotter(17.438139, 78.39583, 18)

#Location where you want to save your file.
gmap.draw("map.html")
```

## Polygon

Next we can draw a polygon on Google Maps to highlight a specific area of interest. By using `.polygon`, we can create a highlighted border around a specific area. The inputs are the longitude and latitude points of the shape

```{code cell}
latitude_list = [ 17.4567417, 17.5587901, 17.6245545]
longitude_list = [ 78.2913637, 78.007699, 77.9266135 ]
gmap = gmplot.GoogleMapPlotter(17.438139, 78.3936413, 11)
gmap.polygon(latitude_list, longitude_list, color = 'cornflowerblue')
gmap.draw( "poly.html" )
```

## Complete Map

A more complete map is shown below by using many longitude and latitude points to highlight specific areas of the map. First, we create a zip of the outline of Golden Gate Park and use `.polygon` as we did previously. Next, we add some attraction points use `.scatter`. Lastly, we can add a marker to give attention to a specific spot using `.marker`.

``` {code cell}
#Create the map plotter:
gmap = gmplot.GoogleMapPlotter(37.766956, -122.448481, 14)

#Outline the Golden Gate Park:
golden_gate_park = zip(*[
    (37.771269, -122.511015),
    (37.773495, -122.464830),
    (37.774797, -122.454538),
    (37.771988, -122.454018),
    (37.773646, -122.440979),
    (37.772742, -122.440797),
    (37.771096, -122.453889),
    (37.768669, -122.453518),
    (37.766227, -122.460213),
    (37.764028, -122.510347)
])
gmap.polygon(*golden_gate_park, color='cornflowerblue', edge_width=10)

#Highlight some attractions:
attractions_lats, attractions_lngs = zip(*[
    (37.769901, -122.498331),
    (37.768645, -122.475328),
    (37.771478, -122.468677),
    (37.769867, -122.466102),
    (37.767187, -122.467496),
    (37.770104, -122.470436)
])
gmap.scatter(attractions_lats, attractions_lngs, color='#3B0B39', size=40, marker=False)

#Mark a hidden gem:
gmap.marker(37.770776, -122.461689, color='cornflowerblue')

#Draw the map:
gmap.draw('GGP.html')
```

## Heat Map

Another cool aspect of `gmplot` is the ability to create a heatmap. In this, I randomly generated 700 points around the world. Then I used `.heatmap`, which generates the points. You have the option to add markers to them, but it looks bad since so many points were generated in this specific example.

```{code cell}
#Import libraries
import gmplot
import numpy as np

#generate the random points for heat map
latitude = (np.random.random_sample(size = 500) - 0.5) * 180
longitude = (np.random.random_sample(size = 500) - 0.5) * 360

#center of the map and zoom
gmap = gmplot.GoogleMapPlotter(0, 0, 2)

#plot heatmap
gmap.heatmap(latitude, longitude)

#save it to html
gmap.draw("heatmap.html")
```
## NYC Data

```{code cell}
import pandas as pd

nyc_crash = pd.read_csv("/Users/robbieshamirian/STAT 3255/my_nyc.csv")
nyc_crash.info()
```
``` {code cell}
nyc_crash_desc = nyc_crash.describe()
nyc_crash_desc
```
```{code cell}
bronx_long = nyc_crash.loc[nyc_crash['BOROUGH'] == 'BRONX',
                           ["CRASH DATE", "CRASH TIME", "LONGITUDE"]]
bronx_lat  = nyc_crash.loc[nyc_crash['BOROUGH'] == 'BRONX',
                           ["CRASH DATE", "CRASH TIME", "LATITUDE"]]
```{code cell}
mybronx = pd.merge(bronx_lat, bronx_long)
lat = mybronx.dropna()
bronx_lat = lat["LATITUDE"]
bronx_lngs = lat["LONGITUDE"]
```

I chose to use the data for the Bronx borough as it was already cleaned. I zoomed in on the Bronx, and added in all of the crashes in that burough.

```{code cell}
gmap = gmplot.GoogleMapPlotter(40.844782, -73.864827, 12)
gmap.scatter(bronx_lat, bronx_lngs, 'blue', size = 100, marker = False)
gmap.draw('bronxmap.html')
```
