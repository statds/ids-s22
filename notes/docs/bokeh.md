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
