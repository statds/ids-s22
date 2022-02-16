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


# Gmplot
6.6. GM Plot
To install gmplot, we can use pip by running pip or conda

pip install gmplot

conda install -c mlgill gmplot

After installing the package, we can now use it in Jupyter Notebook. gmplot's most unique use is it's ability to save geographical coordinates on Google Maps to a local file. Additionally, it has a similar interface to matplotlib in regards to its generation of the HTML to deliver all the additional data on top of Google Maps.

First, we have to import gmplot

import gmplot
6.6.1. Basic Plot
After doing so, we can generate a simple map using gmplot.GoogleMapPlotter. This will create an .html file. I saved mine to Jupyter Notebook but you can set your path to save it to anywhere you choose.

#first two arguments are longitude and latitude and the third one is zoom resolution
gmap = gmplot.GoogleMapPlotter(17.438139, 78.39583, 18)
# Location where you want to save your file.
gmap.draw("map.html")
6.6.2 Polygon
Next we can draw a polygon on Google Maps to highlight a specific area of interest. By using .polygon, we can create a highlighted border around a specific area. The inputs are the longitude and latitude points of the shape

latitude_list = [ 17.4567417, 17.5587901, 17.6245545]
longitude_list = [ 78.2913637, 78.007699, 77.9266135 ]
gmap = gmplot.GoogleMapPlotter(17.438139, 78.3936413, 11)
gmap.polygon(latitude_list, longitude_list, color = 'cornflowerblue')
gmap.draw( "poly.html" )
6.6.3 Complete Map
A more complete map is shown below by using many longitude and latitude points to highlight specific areas of the map. First, we create a zip of the outline of Golden Gate Park and use .polygon as we did previously. Next, we add some attraction points use .scatter. Lastly, we can add a marker to give attention to a specific spot using .marker.

# Create the map plotter:
gmap = gmplot.GoogleMapPlotter(37.766956, -122.448481, 14)

# Outline the Golden Gate Park:
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

# Highlight some attractions:
attractions_lats, attractions_lngs = zip(*[
    (37.769901, -122.498331),
    (37.768645, -122.475328),
    (37.771478, -122.468677),
    (37.769867, -122.466102),
    (37.767187, -122.467496),
    (37.770104, -122.470436)
])
gmap.scatter(attractions_lats, attractions_lngs, color='#3B0B39', size=40, marker=False)

# Mark a hidden gem:
gmap.marker(37.770776, -122.461689, color='cornflowerblue')

# Draw the map:
gmap.draw('GGP.html')
6.6.4 Heat Map
Another cool aspect of gmplot is the ability to create a heatmap. In this, I randomly generated 700 points around the world. Then I used .heatmap, which generates the points. You have the option to add markers to them, but it looks bad since so many points were generated in this specific example.

#Import libraries
import gmplot
import numpy as np

# generate the random points for heat map
latitude = (np.random.random_sample(size = 500) - 0.5) * 180
longitude = (np.random.random_sample(size = 500) - 0.5) * 360

#center of the map and zoom
gmap = gmplot.GoogleMapPlotter(0, 0, 2)

# plot heatmap
gmap.heatmap(latitude, longitude)

# save it to html
gmap.draw("heatmap.html")
6.6.5 NYC Data
import pandas as pd

nyc_crash = pd.read_csv("/Users/robbieshamirian/STAT 3255/my_nyc.csv")
nyc_crash.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 7659 entries, 0 to 7658
Data columns (total 29 columns):
 #   Column                         Non-Null Count  Dtype  
---  ------                         --------------  -----  
 0   CRASH DATE                     7659 non-null   object 
 1   CRASH TIME                     7659 non-null   object 
 2   BOROUGH                        5025 non-null   object 
 3   ZIP CODE                       5025 non-null   float64
 4   LATITUDE                       7097 non-null   float64
 5   LONGITUDE                      7097 non-null   float64
 6   LOCATION                       7097 non-null   object 
 7   ON STREET NAME                 5625 non-null   object 
 8   CROSS STREET NAME              3620 non-null   object 
 9   OFF STREET NAME                2034 non-null   object 
 10  NUMBER OF PERSONS INJURED      7659 non-null   int64  
 11  NUMBER OF PERSONS KILLED       7659 non-null   int64  
 12  NUMBER OF PEDESTRIANS INJURED  7659 non-null   int64  
 13  NUMBER OF PEDESTRIANS KILLED   7659 non-null   int64  
 14  NUMBER OF CYCLIST INJURED      7659 non-null   int64  
 15  NUMBER OF CYCLIST KILLED       7659 non-null   int64  
 16  NUMBER OF MOTORIST INJURED     7659 non-null   int64  
 17  NUMBER OF MOTORIST KILLED      7659 non-null   int64  
 18  CONTRIBUTING FACTOR VEHICLE 1  7615 non-null   object 
 19  CONTRIBUTING FACTOR VEHICLE 2  5624 non-null   object 
 20  CONTRIBUTING FACTOR VEHICLE 3  824 non-null    object 
 21  CONTRIBUTING FACTOR VEHICLE 4  225 non-null    object 
 22  CONTRIBUTING FACTOR VEHICLE 5  80 non-null     object 
 23  COLLISION_ID                   7659 non-null   int64  
 24  VEHICLE TYPE CODE 1            7539 non-null   object 
 25  VEHICLE TYPE CODE 2            4748 non-null   object 
 26  VEHICLE TYPE CODE 3            752 non-null    object 
 27  VEHICLE TYPE CODE 4            207 non-null    object 
 28  VEHICLE TYPE CODE 5            78 non-null     object 
dtypes: float64(3), int64(9), object(17)
memory usage: 1.7+ MB
nyc_crash_desc = nyc_crash.describe()
nyc_crash_desc
ZIP CODE	LATITUDE	LONGITUDE	NUMBER OF PERSONS INJURED	NUMBER OF PERSONS KILLED	NUMBER OF PEDESTRIANS INJURED	NUMBER OF PEDESTRIANS KILLED	NUMBER OF CYCLIST INJURED	NUMBER OF CYCLIST KILLED	NUMBER OF MOTORIST INJURED	NUMBER OF MOTORIST KILLED	COLLISION_ID
count	5025.000000	7097.000000	7097.000000	7659.000000	7659.000000	7659.000000	7659.000000	7659.000000	7659.0	7659.000000	7659.000000	7.659000e+03
mean	10908.957015	40.609463	-73.705503	0.404753	0.002350	0.084345	0.001306	0.021935	0.0	0.287505	0.000914	4.495510e+06
std	514.740028	2.160598	3.919451	0.726622	0.048425	0.290332	0.036113	0.149131	0.0	0.692545	0.030220	2.338111e+03
min	10000.000000	0.000000	-74.249980	0.000000	0.000000	0.000000	0.000000	0.000000	0.0	0.000000	0.000000	4.491064e+06
25%	10459.000000	40.665085	-73.960370	0.000000	0.000000	0.000000	0.000000	0.000000	0.0	0.000000	0.000000	4.493532e+06
50%	11209.000000	40.712505	-73.917310	0.000000	0.000000	0.000000	0.000000	0.000000	0.0	0.000000	0.000000	4.495533e+06
75%	11354.000000	40.786957	-73.862274	1.000000	0.000000	0.000000	0.000000	0.000000	0.0	0.000000	0.000000	4.497480e+06
max	11697.000000	40.909206	0.000000	8.000000	1.000000	3.000000	1.000000	2.000000	0.0	8.000000	1.000000	4.500594e+06
bronx_long = nyc_crash.loc[nyc_crash['BOROUGH'] == 'BRONX',
                           ["CRASH DATE", "CRASH TIME", "LONGITUDE"]]
bronx_lat  = nyc_crash.loc[nyc_crash['BOROUGH'] == 'BRONX',
                           ["CRASH DATE", "CRASH TIME", "LATITUDE"]]
mybronx = pd.merge(bronx_lat, bronx_long)
lat = mybronx.dropna()
bronx_lat = lat["LATITUDE"]
bronx_lngs = lat["LONGITUDE"]
I chose to use the data for the Bronx borough as it was already cleaned. I zoomed in on the Bronx, and added in all of the crashes in that burough.

gmap = gmplot.GoogleMapPlotter(40.844782, -73.864827, 12)
gmap.scatter(bronx_lat, bronx_lngs, 'blue', size = 100, marker = False)
gmap.draw('bronxmap.html')
...

