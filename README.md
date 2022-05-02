---
jupyter:
  jupytext:
    formats: ipynb,py:light,md
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.13.6
  kernelspec:
    display_name: Python 3
    language: python
    name: python3
---

# Timezones


Jupyter notebook showing a dynamically generated table of selected timezones

```python
import pandas as pd
import pytz
import pendulum

parameters = {
    'cities': ['Los_Angeles',
               'Chicago',
               'New_York',
               'London',
               'Berlin',
               'Moscow',
               'Dubai',
               'Shanghai',
               'Tokyo'],
    'cities_west_of_chicago': 1,
    'first_column_chicago': 17,
    'separator_chicago': 9
}

timezone_cities = [ [tz for tz in pytz.all_timezones if city in tz][0] for city in parameters['cities']]
pendulum_for_each_city = [pendulum.today(tz) for tz in timezone_cities]

index_chicago = parameters['cities'].index('Chicago')
p_chicago = pendulum_for_each_city[index_chicago]

def get_offset_city(i):
    if i<parameters['cities_west_of_chicago']:
        return(-p_chicago.diff(pendulum_for_each_city[i]).in_hours())
    else:
        return(p_chicago.diff(pendulum_for_each_city[i]).in_hours())

def get_entry(h,o):
    return((parameters['first_column_chicago']+o+h)%24)

offset_cities = [get_offset_city(i) for i in range(len(pendulum_for_each_city))]
data = {_: [get_entry(_,o) for o in offset_cities] for _ in range(24)}
df = pd.DataFrame(data)
df.rename(index={_: parameters['cities'][_] for _ in range(len(parameters['cities']))}, inplace=True)

# Using DataFrame.insert() to add a column
index_open_market=int(df.loc[parameters['cities'][index_chicago],[df[col][index_chicago]==parameters['separator_chicago'] for col in df.columns]].index[0])
df.insert(index_open_market, "|", ["|" for  city in parameters['cities']], True)

pd.options.display.max_columns = None
display(df)

```
```python

```

