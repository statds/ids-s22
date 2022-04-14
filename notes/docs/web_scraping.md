---
jupyter:
  jupytext:
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.11.5
  kernelspec:
    display_name: Python 3 (ipykernel)
    language: python
    name: python3
---

# Web Scraping with Beautiful Soup
More information about webscraping can be found at this site:
https://towardsdatascience.com/a-step-by-
step-guide-to-web-scraping-in-python-5c4d9cef76e8

First, we need to install BeautifulSoup, we can use 
`pip install beautifulsoup4`

We can read any webpage with BeautifulSoup using the following method.


```python
import pandas as pd
import requests as rq
from bs4 import BeautifulSoup as bs
from IPython.core.display import display, HTML
url = 'https://en.wikipedia.org/wiki/List_of_municipalities_in_Connecticut'
page = rq.get(url).text
soup = bs(page)
display(HTML(page))
```

We can navigate any part of the html tree using the specific tag we want.


```python
soup.title
```

We can find any part of the html tree in a similar way, but it will default to 
the first appearance of that tag. 
We can specify that we want the text component.


```python
soup.find('h1').text
```

We can find all appearances of that tag.


```python
for x in soup.find_all('h2'):
    print(x.text)
```

If we we just want a table as a dataframe, 
the easiest way to do this is with pandas.


```python
# Extract tables
dfs = pd.read_html(url)

# Get first table                                                                                                           
df = dfs[1]

# Extract columns                                                                                                           
df.info()
```

However, if we want to get information from different webpages, this method is 
not sufficient. In this example, we use Beautiful Soup to create an easy way to 
access information of CT towns. We first start by going through the table to 
create a list of links for the towns of Connecticut. Then we will extract some 
information from each page's infobox.


```python
table = soup.find('table', {'class': "wikitable sortable"}) # Finds table
rows = table.find_all('tr')[1:] # Finds all rows of the table
base_url = 'https://en.wikipedia.org' # base_url to which the href will be added
town_links = []
for r in range(0, 169):
    town_links.append(rows[r].find('a')['href']) 
town_urls = [base_url + link for link in town_links]
town_urls
```

Here, we define a function that extracts what county a town is from.


```python
def get_county(link):
    link_page = rq.get(link).text
    link_soup = bs(link_page)
    table = link_soup.find('table')
    c = table.find(text = 'County') # Finds part of html that has 'County'
    b = link_soup.find('h1').text
    if c is None:
        return [b, ' ']
    else:
        county = c.next.next.text
        return [b, ' '.join(county.split('\xa0'))] 
get_county('https://en.wikipedia.org/wiki/Andover,_Connecticut')
```

We could do a separate function for each piece of information, or we could do 
things a little faster.


```python
def get_info(link):
    link_page = rq.get(link).text
    link_soup = bs(link_page)
    table = link_soup.find('table')
    rows = table.find_all('tr', {'class' : 'mergedrow'}) 
    town_info = [link_soup.find('h1').text]
    for row in rows:
        r = row.find('td', {'class' : 'infobox-data'})
        if r is None:
            town_info.append('')
        else:
            town_info.append(' '.join(r.text.split('\xa0')))
    return town_info
get_info('https://en.wikipedia.org/wiki/Hartford,_Connecticut')
```

We can also define a function to extract a specific piece of info


```python
def get_info_2(link, key): # This function takes in a keyword
    link_page = rq.get(link).text
    link_soup = bs(link_page)
    table = link_soup.find('table')
    i = table.find(text = key) # Finds part of html with this keyword
    b = link_soup.find('h1').text
    if i is None:
        return [b, ' ']
    else:
        info = i.next.next.text
        return [b, ' '.join(info.split('\xa0'))]
get_info_2('https://en.wikipedia.org/wiki/Mansfield,_Connecticut', ' • Density')
```

Here we use the above function to create a list of CT towns and their elevation. 


```python
#to add pauses
import time
#to store towns info
town_info_list = []

for link in town_urls:
  #get town info
  town_info = get_info_2(link, 'Elevation')
  #if everything is correct and no error occurs
  if town_info:
    town_info_list.append(town_info)
  #pause a second between each town
  time.sleep(1)
town_info_list
```

We can convert the list to a dataframe, and use the data to make a nice barplot.


```python
df1 = pd.DataFrame(town_info_list, columns = ['town', 'elevation'])
df1.info()
```


```python
df1
```


```python
import numpy as np
df1[df1.elevation == ' '] = np.nan
df1 = df1.dropna()
df1.info()
```


```python
df1['town_name'] = [x.split(',')[0] for x in df1['town']]
df1['elevation_feet'] = [int(x.split(' ')[0].replace(',', '')) for x in df1['elevation']]
df1 = df1[df1['elevation_feet'] >= 700]
df1 = df1.sort_values(by = 'elevation_feet', ascending = False)
df1
```


```python
import matplotlib.pyplot as plt
plt.figure(figsize = (30, 30))
plt.bar(df1['town_name'], df1['elevation_feet'], align = 'center', alpha = 0.5)
```
