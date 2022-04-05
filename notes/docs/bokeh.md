---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.13.7
kernelspec:
  display_name: Python 3 (ipykernel)
  language: python
  name: python3
---

# Interactive Visualizations Using Bokeh

From [docs.bokeh.org](https://docs.bokeh.org):

> Bokeh is a Python library for creating interactive visualizations for modern web browsers. It helps you build 
> beautiful graphics, ranging from simple plots to complex dashboards with streaming datasets. With Bokeh, you 
> can create JavaScript-powered visualizations without writing any JavaScript yourself.

## Installing Bokeh

- pip: `pip install bokeh`
- conda: `conda install bokeh`

## Bokeh Basics:

```{code-cell} ipython3
import numpy as np
import pandas as pd
from bokeh.io import curdoc, push_notebook, output_notebook
from bokeh.layouts import column, gridplot, layout, row, widgetbox
from bokeh.models import ( 
    CategoricalColorMapper, CDSView, CheckboxGroup, ColumnDataSource, 
    Div, RangeSlider, Slider, Spinner, TextInput
)
from bokeh.palettes import Spectral6
from bokeh.plotting import figure, show
from bokeh.tile_providers import get_provider, Vendors
from bokeh.transform import factor_cmap, factor_mark, linear_cmap
from ipywidgets import interact
```

```{code-cell} ipython3
output_notebook() # Display outputs inside notebook
```

```{code-cell} ipython3
x = [-5, -4, -3, -2, -1, 0, 1, 2, 3, 4, 5]
y = [i**2 for i in x]

p = figure(title = 'Exponential Function', x_axis_label = 'x',
           y_axis_label = 'x^2')
p.circle(x, y, fill_color = 'cyan', size = 15, line_color = 'red')
show(p)
```

By default, whenever we create a plot, a toolbar containing various buttons will be available to us on the right side. Some of 
these buttons include the ability to move the graph within the plot, zoom in and out using a mouse wheel or trackpad, zoom into 
an area of the plot by creating a box, exporting the current view as a .png file, reseting the view to its default settings, 
etc. You can also learn more about configuring plot tools by clicking on the help button at the bottom.

We can customize our plots by including different tools in the toolbar as well as changing the theme of our background, among 
many other things.

```{code-cell} ipython3
# Change theme/background
curdoc().theme = 'dark_minimal'

# Generate random points
x = np.random.randint(1, 1000, size = 100)
y = np.random.randint(1, 1000, size = 100)

# Create linear color mapper using color palette
mapper = linear_cmap(field_name = 'y', palette = Spectral6, low=min(y), high=max(y))

# Specify tools to include in the toolbar
tools = 'box_select, lasso_select, poly_select, tap, crosshair, hover, reset, wheel_zoom'

# Create figure and points to plot
points = figure(title = 'Random Points', x_axis_label = 'x', y_axis_label = 'y', 
           x_range = (0, 1000), sizing_mode = 'stretch_width', tools = tools)
r = points.circle(x, y, color = mapper, size = 10, line_color = 'white')

# Show plot
show(points)
```

Bokeh's plotting interface also allows us to render/plot multiple glyph types onto the same figure if we want to. Below is an 
example taken from Bokeh's tutorials, and we'll add to it by creating an interactive legend.

```{code-cell} ipython3
# Prepare some data
x = [1, 2, 3, 4, 5]
y1 = [6, 7, 2, 4, 5]
y2 = [2, 3, 4, 5, 6]
y3 = [4, 5, 5, 7, 2]

# Create a new plot with a title and axis labels
f = figure(title = "Multiple glyphs example", x_axis_label = "x", y_axis_label = "y")

# Add multiple renderers
f.line(x, y1, legend_label = "Temp.", color = "blue", line_width = 2)
f.vbar(x = x, top = y2, legend_label = "Rate", width = 0.5, bottom = 0, color = "red")
f.circle(x, y3, legend_label = "Objects", color = "yellow", size = 12)

# Make the legend interactive when clicked on
f.legend.location = "top_left"
f.legend.click_policy = "mute" # Can also be set to 'hide' to completely hide a glyph

# Show the results
show(f)
```

## Widget Interactions

One of the nice features of Bokeh is that it allows us to easily create widgets that interact with our plots in some way, which 
can help to make our visualizations more interesting and versatile. Here we'll create a few simple sliders that will allow us 
to adjust the size of the randomly generated points from earlier as well as change the ranges of the x and y axes.

```{code-cell} ipython3
# Create some basic sliders

# Define Div and Spinner object with start and end values
div = Div(
    text="""
        <p>Select the circle's size using this control element:</p>
        """,
    width=200, height=30)

ps = Spinner(title = 'Circle size', low = 10, high = 50, step = 5, 
             value = r.glyph.size, width = 200)
ps.js_link('value', r.glyph, 'size')

# Define RangeSlider object with start and end values
xs = RangeSlider(title = 'Adjust x-axis range', start = 0, end = 1000, step = 50,
                 value = (points.x_range.start, points.x_range.end))
xs.js_link('value', points.x_range, 'start', attr_selector = 0)
xs.js_link('value', points.x_range, 'end', attr_selector = 1)

ys = RangeSlider(title = 'Adjust y-axis range', start = 0, end = 1000, step = 50,
                 value = (points.x_range.start, points.x_range.end))
ys.js_link('value', points.y_range, 'start', attr_selector = 0)
ys.js_link('value', points.y_range, 'end', attr_selector = 1)

show(column(row(div, ps), xs, ys, points))
```

## Linked Plots

+++

Another interesting feature of Bokeh is the ability to create linked visualizations, which lets you add connected interactivity 
between plots. This can be useful if you have multiple plots which you want to apply the same actions to simultaneously. In 
order to create linked plots or other advanced visualization features, we'll need to convert our data into a Bokeh 
`ColumnDataSource` object. This object is the core to most Bokeh plots, as it provides the data to the glyphs of our plots.
More information about the `ColumnDataSource` object can be found [here](https://docs.bokeh.org/en/latest/docs/user_guide/data.html).

```{code-cell} ipython3
# Define sin and cos functions
x = np.arange(0,4*np.pi,0.1)
y1 = np.sin(x)
y2 = np.cos(x)

source = ColumnDataSource(data = dict(x=x, y1=y1, y2=y2))
TOOLS = ['box_select, box_zoom, lasso_select, pan, reset']

left = figure(title = 'Sine function', width = 400, height = 400, tools = TOOLS, 
              x_axis_label = 'x', y_axis_label = 'y1')
left.circle('x', 'y1', source = source, color = 'cyan', size = 10)

right = figure(title = 'Cosine Function', width = 400, height = 400, tools = TOOLS,
               x_axis_label = 'x', y_axis_label = 'y2', x_range = left.x_range, 
               y_range = left.y_range)
right.circle('x', 'y2', source = source, color = 'red', size = 10)

# Create a gridplot to put the plots side-by-side
p = gridplot([[left, right]])
show(p)
```

## Working with NYC Crash Dataset

Using the NYC Motor Vehicle Crash data, we'll create a few visualizations that utilize different interactive features to try 
and make them more interesting and impactful.

```{code-cell} ipython3
## Read in NYC crash data
nyc_crash = pd.read_csv('../data/nyc_mv_collisions_202201.csv')
nyc_crash.head()
```

```{code-cell} ipython3
# Drop rows with missing values for the columns below
nyc_crash.dropna(subset = ['LATITUDE', 'LONGITUDE', 'LOCATION', 'BOROUGH'], inplace = True)
nyc_crash.drop(nyc_crash[nyc_crash.LATITUDE == 0.0].index, inplace = True)
nyc_crash.drop(nyc_crash[nyc_crash.LONGITUDE == 0.0].index, inplace = True)
nyc_crash.reset_index(drop = True, inplace = True)

# Create variable HOUR
nyc_crash['HOUR'] = [int(x.split(':')[0]) for x in nyc_crash['CRASH TIME']]

def timeframes(x):
    if x <= 5:
        return '12AM-6AM'
    elif x > 5 and x <= 11:
        return '6AM-12PM'
    elif x > 11 and x <= 17:
        return '12PM-6PM'
    else:
        return '6PM-12AM'

## Take crash hours and put them into specifc intervals
nyc_crash['TIMEFRAME'] = nyc_crash['HOUR'].apply(timeframes)
```

Here we're going to create some interactive maps of the crashes that took place across the different boroughs. Before we do so, 
something to note is that as opposed to using standard latitude-longitude coordinates, Bokeh plots typically use something 
called [Web Mercator coordinates](https://en.wikipedia.org/wiki/Web_Mercator_projection). Because of this, we'll have to first 
convert our existing coordinates into these new coordinates, which can be done with the help of the function below. Special 
thanks goes out to this [article](https://towardsdatascience.com/creating-an-interactive-map-in-python-using-bokeh-and-pandas-f84414536a06) for the solution to this problem.

```{code-cell} ipython3
# Define function to switch from lat/long to mercator coordinates
def x_coord(x, y):
    lat = x
    lon = y
    r_major = 6378137.000
    x = r_major * np.radians(lon)
    scale = x/lon
    y = 180.0/np.pi * np.log(np.tan(np.pi/4.0 + 
        lat * (np.pi/180.0)/2.0)) * scale
    return (x, y)

# Define coord as tuple (lat,long)
nyc_crash['LOCATION'] = list(zip(nyc_crash['LATITUDE'], nyc_crash['LONGITUDE']))
# Obtain list of mercator coordinates
mercators = [x_coord(x, y) for x, y in nyc_crash['LOCATION']]

# Create mercator column in our df
nyc_crash['Mercator'] = mercators
# Split that column out into two separate columns - mercator_x and mercator_y
nyc_crash[['Mercator_x', 'Mercator_y']] = nyc_crash['Mercator'].apply(pd.Series)
```

Now that we've solved the issue of the coordinate systems, we can now create different maps of motor vehicle crashes. In the 
example below, we'll create side-by-side plots, one plot showing the crashes that took place by `BOROUGH`, and the other one 
showing crashes that took place based on 6-hour intervals as follows:

- 12AM through 5:59AM
- 6AM through 11:59AM
- 12PM through 5:59PM
- 6PM through 11:59PM

```{code-cell} ipython3
# Interactive Map 1

# Get map tiling for plot displays
tile_provider = get_provider(Vendors.STAMEN_TONER)

# Color scheme for legend labels
index_cmap = factor_cmap('BOROUGH', palette = ['red', 'blue', 'green', 'orange', 'purple'], 
                         factors = sorted(nyc_crash.BOROUGH.unique()))
boroughs = ['BRONX', 'BROOKLYN', 'MANHATTAN', 'QUEENS', 'STATEN ISLAND']

# Create plot figure and glyphs
# Range bounds supplied in web mercator coordinates
p1 = figure(title = 'Motor Vehicle Crashes in NYC', 
           x_range=(-8250000, -8230000), y_range=(4930000, 5010000),
           x_axis_type="mercator", y_axis_type="mercator", 
           width = 500, height = 500)

r1 = p1.circle(x = 'Mercator_x', y = 'Mercator_y', source = nyc_crash[nyc_crash.BOROUGH == 'BRONX'], 
             radius = 100, fill_alpha = 0.5, color = index_cmap, legend_label = 'BRONX')
r2 = p1.circle(x = 'Mercator_x', y = 'Mercator_y', source = nyc_crash[nyc_crash.BOROUGH == 'BROOKLYN'], 
             radius = 100, fill_alpha = 0.5, color = index_cmap, legend_label = 'BROOKLYN')
r3 = p1.circle(x = 'Mercator_x', y = 'Mercator_y', source = nyc_crash[nyc_crash.BOROUGH == 'MANHATTAN'], 
             radius = 100, fill_alpha = 0.5, color = index_cmap, legend_label = 'MANHATTAN')
r4 = p1.circle(x = 'Mercator_x', y = 'Mercator_y', source = nyc_crash[nyc_crash.BOROUGH == 'QUEENS'], 
             radius = 100, fill_alpha = 0.5, color = index_cmap, legend_label = 'QUEENS')
r5 = p1.circle(x = 'Mercator_x', y = 'Mercator_y', source = nyc_crash[nyc_crash.BOROUGH == 'STATEN ISLAND'], 
             radius = 100, fill_alpha = 0.5, color = index_cmap, legend_label = 'STATEN ISLAND')

p1.legend.location = 'bottom_right'
p1.legend.click_policy = 'hide'
p1.add_tile(tile_provider)

# Interactive Map 2

# Color scheme for legend labels
index_cmap2 = factor_cmap('TIMEFRAME', palette = ['blue', 'red', 'yellow', 'cyan'], 
                          factors = sorted(nyc_crash.TIMEFRAME.unique()))
timeframes = ['12AM-6AM', '6AM-12PM', '12PM-6PM', '6PM-12AM']

# Create plot figure and glyphs
# Range bounds supplied in web mercator coordinates
p2 = figure(title = 'Motor Vehicle Crashes in NYC By 6-Hour Intervals', 
           x_range=(-8250000, -8230000), y_range=(4930000, 5010000),
           x_axis_type="mercator", y_axis_type="mercator", 
           width = 500, height = 500)

s1 = p2.circle(x = 'Mercator_x', y = 'Mercator_y', source = nyc_crash[nyc_crash.TIMEFRAME == '12AM-6AM'], 
             radius = 100, fill_alpha = 0.5, color = index_cmap2, legend_label = '12AM-6AM')
s2 = p2.circle(x = 'Mercator_x', y = 'Mercator_y', source = nyc_crash[nyc_crash.TIMEFRAME == '6AM-12PM'], 
             radius = 100, fill_alpha = 0.5, color = index_cmap2, legend_label = '6AM-12PM')
s3 = p2.circle(x = 'Mercator_x', y = 'Mercator_y', source = nyc_crash[nyc_crash.TIMEFRAME == '12PM-6PM'], 
             radius = 100, fill_alpha = 0.5, color = index_cmap2, legend_label = '12PM-6PM')
s4 = p2.circle(x = 'Mercator_x', y = 'Mercator_y', source = nyc_crash[nyc_crash.TIMEFRAME == '6PM-12AM'], 
             radius = 100, fill_alpha = 0.5, color = index_cmap2, legend_label = '6PM-12AM')

p2.legend.location = 'bottom_right'
p2.legend.click_policy = 'hide'
p2.add_tile(tile_provider)

# Create slider to adjust point size
slider = Slider(title = 'Adjust point size', start = 100, end = 500, step = 10, value = 300)

# Link slider to each individual glpyh plotted (5 boroughs, 4 timeframes)
slider.js_link('value', r1.glyph, 'radius')
slider.js_link('value', r2.glyph, 'radius')
slider.js_link('value', r3.glyph, 'radius')
slider.js_link('value', r4.glyph, 'radius')
slider.js_link('value', r5.glyph, 'radius')
slider.js_link('value', s1.glyph, 'radius')
slider.js_link('value', s2.glyph, 'radius')
slider.js_link('value', s3.glyph, 'radius')
slider.js_link('value', s4.glyph, 'radius')

# Show plots
show(column(slider, row(p1, p2)))
```

Next we'll create the same plots as before, but link them together so that they have simultaneous interactivity when using the 
different toolbar features.

```{code-cell} ipython3
# Combined Linked Plots:

# Interactive Map 1

tile_provider = get_provider(Vendors.STAMEN_TONER)
x = nyc_crash['Mercator_x']
y = nyc_crash['Mercator_y']
borough = nyc_crash['BOROUGH']
timeframe = nyc_crash['TIMEFRAME']

# Convert dataset to a Bokeh ColumnDataSource
source = ColumnDataSource(data = dict(x=x, y=y, borough=borough, timeframe=timeframe))

# Set tools to add to figure
TOOLS = ['box_select, box_zoom, lasso_select, pan, reset, wheel_zoom']
boroughs = ['BRONX', 'BROOKLYN', 'MANHATTAN', 'QUEENS', 'STATEN ISLAND']

# Color scheme for legend labels, whcih are categorical
color_mapper = CategoricalColorMapper(factors = boroughs, palette = 
                                      ['red', 'blue', 'green', 'orange', 'purple'])

# Range bounds supplied in web mercator coordinates
# Create plotted figure
left = figure(title = 'Motor Vehicle Crashes in NYC', 
              x_range=(-8250000, -8230000), y_range=(4930000, 5010000),
              x_axis_type="mercator", y_axis_type="mercator", 
              sizing_mode = 'stretch_width', tools = TOOLS)
l = left.scatter(x = 'x', y = 'y', fill_alpha = 0.5, source = source, radius = 100, 
                 color = dict(field = 'borough', transform = color_mapper), legend_group = 'borough')

# Set legend location and determine its behavior
left.legend.location = 'bottom_right'
left.legend.click_policy = 'mute'

# Add map tiling to plot figure
left.add_tile(tile_provider)



# Interactive Map 2

timeframes = ['12AM-6AM', '6AM-12PM', '12PM-6PM', '6PM-12AM']
color_mapper2 = CategoricalColorMapper(factors = timeframes, palette = ['blue', 'red', 'yellow', 'cyan'])

# Range bounds supplied in web mercator coordinates
right = figure(title = 'Motor Vehicle Crashes in NYC By 6-Hour Intervals', 
               x_range=left.x_range, y_range=left.y_range,
               x_axis_type="mercator", y_axis_type="mercator", 
               sizing_mode = 'stretch_width', tools = TOOLS)

r = right.scatter(x = 'x', y = 'y', fill_alpha = 0.5, source = source, radius = 100, 
              color = dict(field = 'timeframe', transform = color_mapper2), legend_group = 'timeframe')

right.legend.location = 'bottom_right'
right.legend.click_policy = 'mute'
right.add_tile(tile_provider)

# Create slider to adjust point/glyph size
point_slider = Slider(title = 'Adjust point size', start = 100, end = 500, step = 10, value = 300)
point_slider.js_link('value', l.glyph, 'radius')
point_slider.js_link('value', r.glyph, 'radius')

p = gridplot([[left, right]])
show(column(row(point_slider), row(p)))
```

## Using Bokeh Server

Up until now, we've been creating plots that are interactive, but don't actively change the data being shown when applying 
things like sliders, filters, and other widgets. In order to create these more advanced visualizations, we can use a [Bokeh 
Server](https://docs.bokeh.org/en/latest/docs/user_guide/server.html), which is often used to create interactive web applications that connect front-end UI events to running Python code. To run the app on a Bokeh server, run 
`bokeh serve --show file.ext` in the command line or terminal in the directory where the file is located.

```{code-cell} ipython3
# Interactive plot using Bokeh Server

# Convert dataframe columns of interest into a Bokeh ColumnDataSource
source = ColumnDataSource(data = dict(x = nyc_crash['Mercator_x'], y = nyc_crash['Mercator_y'],
                                      borough = nyc_crash['BOROUGH'], hour = nyc_crash['HOUR']))
borough_list = ['BRONX', 'BROOKLYN', 'MANHATTAN', 'QUEENS', 'STATEN ISLAND']
color_mapper = CategoricalColorMapper(factors = borough_list, palette = ['red', 'blue', 'green', 'orange', 'purple'])
tile_provider = get_provider(Vendors.STAMEN_TONER)

# Create plotted figure
plot = figure(title = 'NYC Motor Vehicle Crashes', height = 600, width = 600,
              x_range = (-8250000, -8230000), y_range=(4930000, 5010000),
              x_axis_type = 'mercator', y_axis_type = 'mercator')
r = plot.circle(x = 'x', y = 'y', fill_alpha = 0.5, source = source,
            color = dict(field = 'borough', transform = color_mapper), legend_group = 'borough')

# Create slider to adjust point/glyph size
point_slider = Slider(title = 'Adjust point size', start = 100, end = 500, step = 10, value = 300)
point_slider.js_link('value', r.glyph, 'radius')

# Set location of legend and determine its behavior
plot.legend.location = 'bottom_right'
plot.xaxis.axis_label = 'Longitude'
plot.yaxis.axis_label = 'Latitude'
plot.legend.click_policy = 'mute'

# Add map tiling to plot figure
plot.add_tile(tile_provider)

# Function to update data when slider value is changed
def update_plot(attr, old, new):
    time = time_slider.value
    plot.xaxis.axis_label = 'Longitude'
    plot.yaxis.axis_label = 'Latitude'
    
    new_data = {
        'x': nyc_crash[nyc_crash.HOUR == time]['Mercator_x'],
        'y': nyc_crash[nyc_crash.HOUR == time]['Mercator_y'],
        'borough': nyc_crash[nyc_crash.HOUR == time]['BOROUGH'],
        'hour': nyc_crash[nyc_crash.HOUR == time]['HOUR']
    }
    source.data = new_data

# Create a slider to adjust hour
time_slider = Slider(title = 'Select hour', value = 12, start = 0, end = 23, step = 1)
time_slider.on_change('value', update_plot)

layout = row(column(point_slider, time_slider), plot)

curdoc().add_root(layout)
curdoc().title = 'NYC Vehicle Crashes'
```

To run the code above and see the visualization, go into the terminal/command line and navigate to the directory where this 
file is located, then run `bokeh serve --show bokeh.ipynb`.

(Note: Because this is actually an .md file, the command above won't work, as `bokeh serve` only works with .py and .ipynb 
files. To see the result of the last code block, copy and paste everything here into a Jupyter Notebook file and then run the 
command. It will open a new tab in your web browser where you can see and interact with the visualization. When loading in 
the dataset, you can use the following URL instead of the relative path to the dataset.

URL: https://raw.githubusercontent.com/statds/ids-s22/main/notes/data/nyc_mv_collisions_202201.csv)

+++

## Sources and Additional Information

https://docs.bokeh.org/en/latest/

https://docs.bokeh.org/en/latest/docs/first_steps.html

https://medium.com/codex/visualizing-gapminder-data-using-bokeh-1aeeb7c85e6d

https://towardsdatascience.com/creating-an-interactive-map-in-python-using-bokeh-and-pandas-f84414536a06
