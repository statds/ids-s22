 # Pygal Visualizations
 From https://pythonprogramming.net/pygal-tutorial/

Pygal is a Python module that creates SVG
(Scalable Vector Graphics) graphs/charts in a variety of styles.
Pygal is highly customize-able, yet also extremely simplistic, 
which is a very rare combination.

## Installing Pygal
```{code cell} ipython3
pip install pygal
```

## Very Simple Example
```{code cell} ipython3
from IPython.display import display, HTML

##needed for interactive visualization
## in Jupyter Notebook
base_html = """
<!DOCTYPE html>
<html>
  <head>
  <script type="text/javascript" src="http://kozea.github.com/pygal.js/javascripts/svg.jquery.js"></script>
  <script type="text/javascript" src="https://kozea.github.io/pygal.js/2.0.x/pygal-tooltips.min.js""></script>
  </head>
  <body>
    <figure>
      {rendered_chart}
    </figure>
  </body>
</html>
"""

##creating points, titles
##makes interactive graph
import pygal
graph = pygal.Line()
graph.title = '% Change of languages over time'
graph.x_labels = ['2011','2012','2013','2014','2015','2016']
graph.add('Python',  [15, 31, 89, 200, 356, 900])
graph.add('Java',    [15, 45, 76, 80,  91,  95])
graph.add('C++',     [5,  51, 54, 102, 150, 201])
graph.add('All others combined!',  [5, 15, 21, 55, 92, 105])
display(HTML(base_html.format(rendered_chart=graph.render(is_unicode=True))))
```

## Using Sample Pandas Data
``` {code cell} ipython3
import pandas as pd
df = pd.read_csv('https://anvil.works/blog/img/plotting-in-python/uk-election-results.csv')
df
```
```{code cell} ipython3
import pygal
from pygal.style import Style
custom_style = Style(
    colors=('#0343df', '#e50000', '#ffff14', '#929591'),
    font_family='Roboto,Helvetica,Arial,sans-serif',
    background='transparent',
    label_font_size=14,
)

c = pygal.Bar(
    title="UK Election Results",
    style=custom_style,
    y_title='Seats',
    width=1200,
    x_label_rotation=270,
    
c.add('Conservative', df['conservative'])
c.add('Labour', df['labour'])
c.add('Liberal', df['liberal'])
c.add('Others', df['others'])

c.x_labels = df['year']

display(HTML(base_html.format(rendered_chart=c.render(is_unicode=True))))
)
```

## Using NYC Crash Data

``` {code cell} ipython3
import pandas as pd
df = pd.read_csv('https://raw.githubusercontent.com/statds/ids-s22/main/notes/data/nyc_mv_collisions_202201.csv')
df.info()
```
```{code cell}
mean_by_category = df.groupby("BOROUGH")['NUMBER OF PERSONS INJURED'].mean()

bar_chart = pygal.Bar(height=200)
[bar_chart.add(x[0], x[1]) for x in mean_by_category.items()]
display(HTML(base_html.format(rendered_chart=bar_chart.render(is_unicode=True))))

mean_by_category_2 = df.groupby("BOROUGH")['NUMBER OF PERSONS INJURED'].mean()
mean_by_category_2

gauge = pygal.SolidGauge(inner_radius=0.70)
[gauge.add(x[0], [{"value" : x[1] * 100}] ) for x in mean_by_category_2.iteritems()]
gauge.title = "Injuries Per 100 Crashes In Each Borough"
display(HTML(base_html.format(rendered_chart=gauge.render(is_unicode=True))))

#interesting graphic, could be
# awesome for speed-related data
gauge = pygal.Gauge(human_readable=True)
gauge.range = [30,42]
[gauge.add(x[0], [{"value" : x[1] * 100}] ) for x in mean_by_category.iteritems()]
display(HTML(base_html.format(rendered_chart=gauge.render(is_unicode=True))))
```

```{code cell} ipython3
#interesting graphic, could be
# awesome for speed-related data
gauge = pygal.Gauge(human_readable=True)
gauge.range = [30,42]
[gauge.add(x[0], [{"value" : x[1] * 100}] ) for x in mean_by_category.iteritems()]
display(HTML(base_html.format(rendered_chart=gauge.render(is_unicode=True))))
```

```{code cell} ipython3
radar_plot = pygal.Radar(human_readable=True)
radar_plot.add("Bronx", bronx_injured)
radar_plot.add("Brooklyn", brooklyn_injured)
radar_plot.add("Manhattan", manhattan_injured)
radar_plot.add("Queens", queens_injured)
radar_plot.add("Staten Island", staten_island_injured)
display(HTML(base_html.format(rendered_chart=radar_plot.render(is_unicode=True))))
```
