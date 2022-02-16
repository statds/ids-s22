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

# 6.6. GM Plot

To install `gmplot`, we can use pip by running pip or conda

`pip install gmplot`

`conda install -c mlgill gmplot`

After installing the package, we can now use it in Jupyter Notebook. `gmplot`'s most unique use is it's ability to save geographical coordinates on Google Maps to a local file. Additionally, it has a similar interface to matplotlib in regards to its generation of the HTML to deliver all the additional data on top of Google Maps.

First, we have to `import gmplot`

import gmplot

## 6.6.1. Basic Plot

After doing so, we can generate a simple map using `gmplot.GoogleMapPlotter`. This will create an `.html` file. I saved mine to Jupyter Notebook but you can set your path to save it to anywhere you choose.

#first two arguments are longitude and latitude and the third one is zoom resolution

gmap = gmplot.GoogleMapPlotter(17.438139, 78.39583, 18)

#Location where you want to save your file.

gmap.draw("map.html")

## 6.6.2 Polygon

Next we can draw a polygon on Google Maps to highlight a specific area of interest. By using `.polygon`, we can create a highlighted border around a specific area. The inputs are the longitude and latitude points of the shape

latitude_list = [ 17.4567417, 17.5587901, 17.6245545]

longitude_list = [ 78.2913637, 78.007699, 77.9266135 ]

gmap = gmplot.GoogleMapPlotter(17.438139, 78.3936413, 11)

gmap.polygon(latitude_list, longitude_list, color = 'cornflowerblue')

gmap.draw( "poly.html" )

## 6.6.3 Complete Map

A more complete map is shown below by using many longitude and latitude points to highlight specific areas of the map. First, we create a zip of the outline of Golden Gate Park and use `.polygon` as we did previously. Next, we add some attraction points use `.scatter`. Lastly, we can add a marker to give attention to a specific spot using `.marker`.

#Create the map plotter:
gmap = gmplot.GoogleMapPlotter(37.766956, -122.448481, 14)

![Screen Shot 2022-02-16 at 1 39 32 PM](https://user-images.githubusercontent.com/98053064/154333387-65665133-e799-4c29-903f-51537601e9c3.png)

#Mark a hidden gem:
gmap.marker(37.770776, -122.461689, color='cornflowerblue')

#Draw the map:
gmap.draw('GGP.html')

## 6.6.4 Heat Map

Another cool aspect of `gmplot` is the ability to create a heatmap. In this, I randomly generated 700 points around the world. Then I used `.heatmap`, which generates the points. You have the option to add markers to them, but it looks bad since so many points were generated in this specific example.

![Screen Shot 2022-02-16 at 1 40 30 PM](https://user-images.githubusercontent.com/98053064/154333567-3928d2a1-d540-4199-95eb-e8e4f395ade0.png)

## 6.6.5 NYC Data

nyc_crash = pd.read_csv("/Users/robbieshamirian/STAT 3255/my_nyc.csv")
nyc_crash.info()

RangeIndex: 7659 entries, 0 to 7658

nyc_crash_desc = nyc_crash.describe

nyc_crash_desc = nyc_crash.describe()
nyc_crash_desc

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

