Weather Analysis

Overview

This Python script will visualize the weather of 500 unique cities across the world in random locations selected using Citypy. Citipy is a city-finding python package that was used to find randomly located cities. The OpenWeatherMap API was used to return current weather statistics for those cities.

A series of scatter plots will be used to showcase the following relationships:
Temperature (F) vs. Latitude.
Humidity (%) vs. Latitude.
Cloudiness (%) vs. Latitude.
Wind Speed (mph) vs. Latitude.

The script will:
Randomly select at least 500 unique (non-repeat) cities based on latitude and longitude.
Perform a weather check on each of the cities using a series of successive API calls.
Include a print log of each city as it's being processed with the city number, city name, and requested URL.
Save both a CSV of all data retrieved and png images for each scatter plot.



TRENDS:
1) Temperature increases as the latitude approaches the equator (latitude 0).
2) Humidity is more concentrated at higher values as the latitude approaches the equator (latitude 0).
3) Wind speeds increase as the latitude goes away from the equator (latitude -90 and 90).

```python
# Dependencies
import json
import requests
import random
import pandas as pd
import numpy as np
import time
from citipy import citipy
import matplotlib.pyplot as plt
import seaborn as sns

# Import Open Weather Map API key.
from config import api_key
```


```python
# Declare variables describing the scope of lat/lng search for cities.
# Lat ranges from -90 to 90. Lng ranges from -180 to 180.
lat = {'min': -90, 'max': 90}
lng = {'min': -180, 'max': 180}

# Create arrays containing increments of lat and long.
lat_values = np.arange(lat['min'], lat['max'], 0.01)
lng_values = np.arange(lng['min'], lng['max'], 0.01)
```


```python

# Create an empty data frame to city and weather data
column_names = ('city_name', 'country_code', 'rand_lat', 'rand_lng', 'Latitude', 'Longitude','Temp (F)',
            'Humidity (%)','Cloudiness (%)','Wind Speed (mph)')
cities_df = pd.DataFrame(columns = column_names)
cities_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>city_name</th>
      <th>country_code</th>
      <th>rand_lat</th>
      <th>rand_lng</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Temp (F)</th>
      <th>Humidity (%)</th>
      <th>Cloudiness (%)</th>
      <th>Wind Speed (mph)</th>
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>
</div>




```python
'''Query Citipy with random lat-long values until we collect our sample, and append weather
data via Open Weather Map API call.'''

# Set the sample size.
sample_size = 500

target_url = 'http://api.openweathermap.org/data/2.5/weather?q='
units = 'imperial'

record = 0

# Loop through and grab the Temp, Humidity, Cloudiness and Wind Speed using OpenWeatherMapAPI

while len(cities_df) < sample_size:
    # Choose a random point within our lat-lng domain.
    rand_lat = random.choice(lat_values)
    rand_lng = random.choice(lng_values)
    # Call citipy's nearest_city() method to get a city object.
    city = citipy.nearest_city(rand_lat, rand_lng)
    city_name = city.city_name
    country_code = city.country_code
    # Call Open Weather Map API to obtain data and append it to df
    url = target_url + city_name + ',' + country_code + '&units=' + units + '&APPID=' + api_key
    weather_response = requests.get(url)
    weather_json = weather_response.json()
    if weather_json["cod"] == 200:
        print('City: %s. %s' % (weather_json['name'], url))
        latitude = weather_json["coord"]["lat"]
        longitude = weather_json["coord"]["lon"]
        temp = weather_json["main"]["temp"]
        humidity = weather_json["main"]["humidity"]
        cloud = weather_json["clouds"]["all"]
        wind = weather_json["wind"]["speed"]
        # Avoid repeating cities
        if city_name not in cities_df.city_name.values:
            print('Status code: %s. DF length is now: %d' % (str(weather_json["cod"]), len(cities_df)+1))
            # Append data to df columns
            cities_df.set_value(record, "city_name", city_name)
            cities_df.set_value(record, "country_code", country_code)
            cities_df.set_value(record, "rand_lat", rand_lat)
            cities_df.set_value(record, "rand_lng", rand_lng)
            cities_df.set_value(record, "Latitude", latitude)
            cities_df.set_value(record, "Longitude", longitude)
            cities_df.set_value(record, "Temp (F)", temp)
            cities_df.set_value(record, "Humidity (%)", humidity)
            cities_df.set_value(record, "Cloudiness (%)", cloud)
            cities_df.set_value(record, "Wind Speed (mph)", wind)

            record += 1

            # Wait between 1-4 seconds before next loop
            time.sleep(random.randint(1, 4))
        else:
            pass
    else:
        pass

print(
"------------------------------\n"
"Data Retrieval Complete\n"
"------------------------------\n"
)

# Visualize df
cities_df.head()
```

    City: Maceio. http://api.openweathermap.org/data/2.5/weather?q=maceio,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 1


    /anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:38: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    /anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:39: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    /anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:40: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    /anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:41: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    /anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:42: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    /anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:43: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    /anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:44: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    /anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:45: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    /anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:46: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    /anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:47: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead


    City: Bluff. http://api.openweathermap.org/data/2.5/weather?q=bluff,nz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 2
    City: Punta Arenas. http://api.openweathermap.org/data/2.5/weather?q=punta arenas,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 3
    City: Taoudenni. http://api.openweathermap.org/data/2.5/weather?q=taoudenni,ml&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 4
    City: Bredasdorp. http://api.openweathermap.org/data/2.5/weather?q=bredasdorp,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 5
    City: Chokurdakh. http://api.openweathermap.org/data/2.5/weather?q=chokurdakh,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 6
    City: Port Elizabeth. http://api.openweathermap.org/data/2.5/weather?q=port elizabeth,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 7
    City: Hilo. http://api.openweathermap.org/data/2.5/weather?q=hilo,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 8
    City: Santa Maria. http://api.openweathermap.org/data/2.5/weather?q=santa maria,cv&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 9
    City: Punta Arenas. http://api.openweathermap.org/data/2.5/weather?q=punta arenas,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Mahebourg. http://api.openweathermap.org/data/2.5/weather?q=mahebourg,mu&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 10
    City: Nemuro. http://api.openweathermap.org/data/2.5/weather?q=nemuro,jp&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 11
    City: Hirara. http://api.openweathermap.org/data/2.5/weather?q=hirara,jp&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 12
    City: Nanortalik. http://api.openweathermap.org/data/2.5/weather?q=nanortalik,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 13
    City: Taree. http://api.openweathermap.org/data/2.5/weather?q=taree,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 14
    City: Bosaso. http://api.openweathermap.org/data/2.5/weather?q=bosaso,so&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 15
    City: Punta Arenas. http://api.openweathermap.org/data/2.5/weather?q=punta arenas,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Inuvik. http://api.openweathermap.org/data/2.5/weather?q=inuvik,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 16
    City: Sao Filipe. http://api.openweathermap.org/data/2.5/weather?q=sao filipe,cv&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 17
    City: La Asuncion. http://api.openweathermap.org/data/2.5/weather?q=la asuncion,ve&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 18
    City: Yamandu. http://api.openweathermap.org/data/2.5/weather?q=yamandu,sl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 19
    City: Arua. http://api.openweathermap.org/data/2.5/weather?q=arua,ug&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 20
    City: Soyo. http://api.openweathermap.org/data/2.5/weather?q=soyo,ao&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 21
    City: Waipawa. http://api.openweathermap.org/data/2.5/weather?q=waipawa,nz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 22
    City: Saint Petersburg. http://api.openweathermap.org/data/2.5/weather?q=saint petersburg,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 23
    City: Mehriz. http://api.openweathermap.org/data/2.5/weather?q=mehriz,ir&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 24
    City: Guozhen. http://api.openweathermap.org/data/2.5/weather?q=guozhen,cn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 25
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 26
    City: Westport. http://api.openweathermap.org/data/2.5/weather?q=westport,nz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 27
    City: Richards Bay. http://api.openweathermap.org/data/2.5/weather?q=richards bay,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 28
    City: Padang. http://api.openweathermap.org/data/2.5/weather?q=padang,id&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 29
    City: Duku. http://api.openweathermap.org/data/2.5/weather?q=duku,ng&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 30
    City: Rock Sound. http://api.openweathermap.org/data/2.5/weather?q=rock sound,bs&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 31
    City: Carnarvon. http://api.openweathermap.org/data/2.5/weather?q=carnarvon,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 32
    City: Bluff. http://api.openweathermap.org/data/2.5/weather?q=bluff,nz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Mopti. http://api.openweathermap.org/data/2.5/weather?q=mopti,ml&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 33
    City: Alyangula. http://api.openweathermap.org/data/2.5/weather?q=alyangula,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 34
    City: Comodoro Rivadavia. http://api.openweathermap.org/data/2.5/weather?q=comodoro rivadavia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 35
    City: Valparaiso. http://api.openweathermap.org/data/2.5/weather?q=valparaiso,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 36
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 37
    City: Pangnirtung. http://api.openweathermap.org/data/2.5/weather?q=pangnirtung,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 38
    City: Thompson. http://api.openweathermap.org/data/2.5/weather?q=thompson,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 39
    City: Avarua. http://api.openweathermap.org/data/2.5/weather?q=avarua,ck&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 40
    City: Yellowknife. http://api.openweathermap.org/data/2.5/weather?q=yellowknife,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 41
    City: Punta Arenas. http://api.openweathermap.org/data/2.5/weather?q=punta arenas,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kyra. http://api.openweathermap.org/data/2.5/weather?q=kyra,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 42
    City: Hilo. http://api.openweathermap.org/data/2.5/weather?q=hilo,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Puerto Ayora. http://api.openweathermap.org/data/2.5/weather?q=puerto ayora,ec&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 43
    City: Elk Plain. http://api.openweathermap.org/data/2.5/weather?q=elk plain,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 44
    City: Bredasdorp. http://api.openweathermap.org/data/2.5/weather?q=bredasdorp,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Tocopilla. http://api.openweathermap.org/data/2.5/weather?q=tocopilla,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 45
    City: Saint-Joseph. http://api.openweathermap.org/data/2.5/weather?q=saint-joseph,re&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 46
    City: Biak. http://api.openweathermap.org/data/2.5/weather?q=biak,id&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 47
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kapaa. http://api.openweathermap.org/data/2.5/weather?q=kapaa,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 48
    City: Ancud. http://api.openweathermap.org/data/2.5/weather?q=ancud,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 49
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bambous Virieux. http://api.openweathermap.org/data/2.5/weather?q=bambous virieux,mu&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 50
    City: Tasiilaq. http://api.openweathermap.org/data/2.5/weather?q=tasiilaq,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 51
    City: Saint-Pierre. http://api.openweathermap.org/data/2.5/weather?q=saint-pierre,pm&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 52
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 53
    City: Albina. http://api.openweathermap.org/data/2.5/weather?q=albina,sr&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 54
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Mitu. http://api.openweathermap.org/data/2.5/weather?q=mitu,co&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 55
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Butaritari. http://api.openweathermap.org/data/2.5/weather?q=butaritari,ki&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 56
    City: Hasaki. http://api.openweathermap.org/data/2.5/weather?q=hasaki,jp&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 57
    City: Zhangjiakou. http://api.openweathermap.org/data/2.5/weather?q=zhangjiakou,cn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 58
    City: Tuktoyaktuk. http://api.openweathermap.org/data/2.5/weather?q=tuktoyaktuk,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 59
    City: Kruisfontein. http://api.openweathermap.org/data/2.5/weather?q=kruisfontein,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 60
    City: Grindavik. http://api.openweathermap.org/data/2.5/weather?q=grindavik,is&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 61
    City: Portobelo. http://api.openweathermap.org/data/2.5/weather?q=portobelo,pa&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 62
    City: Saldanha. http://api.openweathermap.org/data/2.5/weather?q=saldanha,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 63
    City: Gazanjyk. http://api.openweathermap.org/data/2.5/weather?q=gazanjyk,tm&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 64
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Yumen. http://api.openweathermap.org/data/2.5/weather?q=yumen,cn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 65
    City: Standerton. http://api.openweathermap.org/data/2.5/weather?q=standerton,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 66
    City: Punta Arenas. http://api.openweathermap.org/data/2.5/weather?q=punta arenas,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Tasiilaq. http://api.openweathermap.org/data/2.5/weather?q=tasiilaq,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Samarai. http://api.openweathermap.org/data/2.5/weather?q=samarai,pg&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 67
    City: Chuy. http://api.openweathermap.org/data/2.5/weather?q=chuy,uy&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 68
    City: Te Anau. http://api.openweathermap.org/data/2.5/weather?q=te anau,nz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 69
    City: Tasiilaq. http://api.openweathermap.org/data/2.5/weather?q=tasiilaq,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Altamont. http://api.openweathermap.org/data/2.5/weather?q=altamont,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 70
    City: Sao Joao da Barra. http://api.openweathermap.org/data/2.5/weather?q=sao joao da barra,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 71
    City: Tuktoyaktuk. http://api.openweathermap.org/data/2.5/weather?q=tuktoyaktuk,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Pandan. http://api.openweathermap.org/data/2.5/weather?q=pandan,ph&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 72
    City: Eldorado. http://api.openweathermap.org/data/2.5/weather?q=eldorado,mx&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 73
    City: Katsuura. http://api.openweathermap.org/data/2.5/weather?q=katsuura,jp&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 74
    City: Vaini. http://api.openweathermap.org/data/2.5/weather?q=vaini,to&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 75
    City: Havelock. http://api.openweathermap.org/data/2.5/weather?q=havelock,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 76
    City: Lebu. http://api.openweathermap.org/data/2.5/weather?q=lebu,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 77
    City: Miraflores. http://api.openweathermap.org/data/2.5/weather?q=miraflores,co&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 78
    City: New Norfolk. http://api.openweathermap.org/data/2.5/weather?q=new norfolk,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 79
    City: Ponta do Sol. http://api.openweathermap.org/data/2.5/weather?q=ponta do sol,cv&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 80
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: New Norfolk. http://api.openweathermap.org/data/2.5/weather?q=new norfolk,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Zyryanka. http://api.openweathermap.org/data/2.5/weather?q=zyryanka,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 81
    City: Gushi. http://api.openweathermap.org/data/2.5/weather?q=gushi,cn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 82
    City: Umm Lajj. http://api.openweathermap.org/data/2.5/weather?q=umm lajj,sa&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 83
    City: Winnemucca. http://api.openweathermap.org/data/2.5/weather?q=winnemucca,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 84
    City: Vila Franca do Campo. http://api.openweathermap.org/data/2.5/weather?q=vila franca do campo,pt&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 85
    City: Namibe. http://api.openweathermap.org/data/2.5/weather?q=namibe,ao&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 86
    City: Vaini. http://api.openweathermap.org/data/2.5/weather?q=vaini,to&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Roma. http://api.openweathermap.org/data/2.5/weather?q=roma,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 87
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kem. http://api.openweathermap.org/data/2.5/weather?q=kem,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 88
    City: Hermanus. http://api.openweathermap.org/data/2.5/weather?q=hermanus,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 89
    City: La Rioja. http://api.openweathermap.org/data/2.5/weather?q=la rioja,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 90
    City: Saint-Paul. http://api.openweathermap.org/data/2.5/weather?q=saint-paul,re&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 91
    City: Katsuura. http://api.openweathermap.org/data/2.5/weather?q=katsuura,jp&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: New Norfolk. http://api.openweathermap.org/data/2.5/weather?q=new norfolk,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Jalu. http://api.openweathermap.org/data/2.5/weather?q=jalu,ly&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 92
    City: Saskylakh. http://api.openweathermap.org/data/2.5/weather?q=saskylakh,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 93
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Tiksi. http://api.openweathermap.org/data/2.5/weather?q=tiksi,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 94
    City: Albany. http://api.openweathermap.org/data/2.5/weather?q=albany,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 95
    City: Onguday. http://api.openweathermap.org/data/2.5/weather?q=onguday,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 96
    City: Prince Rupert. http://api.openweathermap.org/data/2.5/weather?q=prince rupert,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 97
    City: Auki. http://api.openweathermap.org/data/2.5/weather?q=auki,sb&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 98
    City: Santa Cruz. http://api.openweathermap.org/data/2.5/weather?q=santa cruz,cr&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 99
    City: Tazovskiy. http://api.openweathermap.org/data/2.5/weather?q=tazovskiy,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 100
    City: Chapais. http://api.openweathermap.org/data/2.5/weather?q=chapais,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 101
    City: Fort Nelson. http://api.openweathermap.org/data/2.5/weather?q=fort nelson,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 102
    City: Albany. http://api.openweathermap.org/data/2.5/weather?q=albany,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Chuy. http://api.openweathermap.org/data/2.5/weather?q=chuy,uy&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Cockburn Town. http://api.openweathermap.org/data/2.5/weather?q=cockburn town,tc&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 103
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Lagoa. http://api.openweathermap.org/data/2.5/weather?q=lagoa,pt&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 104
    City: Bluff. http://api.openweathermap.org/data/2.5/weather?q=bluff,nz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Nhulunbuy. http://api.openweathermap.org/data/2.5/weather?q=nhulunbuy,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 105
    City: Albany. http://api.openweathermap.org/data/2.5/weather?q=albany,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Nanortalik. http://api.openweathermap.org/data/2.5/weather?q=nanortalik,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Jamestown. http://api.openweathermap.org/data/2.5/weather?q=jamestown,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 106
    City: Atuona. http://api.openweathermap.org/data/2.5/weather?q=atuona,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 107
    City: Frontera. http://api.openweathermap.org/data/2.5/weather?q=frontera,mx&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 108
    City: Puli. http://api.openweathermap.org/data/2.5/weather?q=puli,tw&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 109
    City: Bluff. http://api.openweathermap.org/data/2.5/weather?q=bluff,nz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Leningradskiy. http://api.openweathermap.org/data/2.5/weather?q=leningradskiy,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 110
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bethel. http://api.openweathermap.org/data/2.5/weather?q=bethel,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 111
    City: Trencianske Teplice. http://api.openweathermap.org/data/2.5/weather?q=trencianske teplice,sk&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 112
    City: Comodoro Rivadavia. http://api.openweathermap.org/data/2.5/weather?q=comodoro rivadavia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Port Alfred. http://api.openweathermap.org/data/2.5/weather?q=port alfred,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 113
    City: Kahului. http://api.openweathermap.org/data/2.5/weather?q=kahului,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 114
    City: Griffith. http://api.openweathermap.org/data/2.5/weather?q=griffith,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 115
    City: Inta. http://api.openweathermap.org/data/2.5/weather?q=inta,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 116
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Victoria. http://api.openweathermap.org/data/2.5/weather?q=victoria,sc&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 117
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Beira. http://api.openweathermap.org/data/2.5/weather?q=beira,mz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 118
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Dakar. http://api.openweathermap.org/data/2.5/weather?q=dakar,sn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 119
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Geraldton. http://api.openweathermap.org/data/2.5/weather?q=geraldton,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 120
    City: Avarua. http://api.openweathermap.org/data/2.5/weather?q=avarua,ck&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Domoni. http://api.openweathermap.org/data/2.5/weather?q=domoni,km&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 121
    City: Punta Arenas. http://api.openweathermap.org/data/2.5/weather?q=punta arenas,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Port Alfred. http://api.openweathermap.org/data/2.5/weather?q=port alfred,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Butaritari. http://api.openweathermap.org/data/2.5/weather?q=butaritari,ki&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Faanui. http://api.openweathermap.org/data/2.5/weather?q=faanui,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 122
    City: Albany. http://api.openweathermap.org/data/2.5/weather?q=albany,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Esperance. http://api.openweathermap.org/data/2.5/weather?q=esperance,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 123
    City: Izumo. http://api.openweathermap.org/data/2.5/weather?q=izumo,jp&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 124
    City: Guerrero Negro. http://api.openweathermap.org/data/2.5/weather?q=guerrero negro,mx&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 125
    City: Luganville. http://api.openweathermap.org/data/2.5/weather?q=luganville,vu&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 126
    City: Albany. http://api.openweathermap.org/data/2.5/weather?q=albany,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Boa Viagem. http://api.openweathermap.org/data/2.5/weather?q=boa viagem,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 127
    City: Mehamn. http://api.openweathermap.org/data/2.5/weather?q=mehamn,no&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 128
    City: Sinazongwe. http://api.openweathermap.org/data/2.5/weather?q=sinazongwe,zm&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 129
    City: Yulara. http://api.openweathermap.org/data/2.5/weather?q=yulara,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 130
    City: Nuuk. http://api.openweathermap.org/data/2.5/weather?q=nuuk,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 131
    City: Albany. http://api.openweathermap.org/data/2.5/weather?q=albany,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kapaa. http://api.openweathermap.org/data/2.5/weather?q=kapaa,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Villarrica. http://api.openweathermap.org/data/2.5/weather?q=villarrica,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 132
    City: Puerto Ayora. http://api.openweathermap.org/data/2.5/weather?q=puerto ayora,ec&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Lorengau. http://api.openweathermap.org/data/2.5/weather?q=lorengau,pg&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 133
    City: Atuona. http://api.openweathermap.org/data/2.5/weather?q=atuona,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Pevek. http://api.openweathermap.org/data/2.5/weather?q=pevek,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 134
    City: Leningradskiy. http://api.openweathermap.org/data/2.5/weather?q=leningradskiy,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Puqi. http://api.openweathermap.org/data/2.5/weather?q=puqi,cn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 135
    City: Port Elizabeth. http://api.openweathermap.org/data/2.5/weather?q=port elizabeth,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Nieuw Amsterdam. http://api.openweathermap.org/data/2.5/weather?q=nieuw amsterdam,sr&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 136
    City: San Rafael. http://api.openweathermap.org/data/2.5/weather?q=san rafael,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 137
    City: Rio Grande. http://api.openweathermap.org/data/2.5/weather?q=rio grande,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 138
    City: Kavieng. http://api.openweathermap.org/data/2.5/weather?q=kavieng,pg&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 139
    City: Yichun. http://api.openweathermap.org/data/2.5/weather?q=yichun,cn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 140
    City: Sorland. http://api.openweathermap.org/data/2.5/weather?q=sorland,no&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 141
    City: Saskylakh. http://api.openweathermap.org/data/2.5/weather?q=saskylakh,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bredasdorp. http://api.openweathermap.org/data/2.5/weather?q=bredasdorp,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Charters Towers. http://api.openweathermap.org/data/2.5/weather?q=charters towers,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 142
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Solnechnyy. http://api.openweathermap.org/data/2.5/weather?q=solnechnyy,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 143
    City: Leningradskiy. http://api.openweathermap.org/data/2.5/weather?q=leningradskiy,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Verkhoyansk. http://api.openweathermap.org/data/2.5/weather?q=verkhoyansk,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 144
    City: Bluff. http://api.openweathermap.org/data/2.5/weather?q=bluff,nz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Torbay. http://api.openweathermap.org/data/2.5/weather?q=torbay,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 145
    City: Caririacu. http://api.openweathermap.org/data/2.5/weather?q=caririacu,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 146
    City: Punta Arenas. http://api.openweathermap.org/data/2.5/weather?q=punta arenas,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Mar del Plata. http://api.openweathermap.org/data/2.5/weather?q=mar del plata,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 147
    City: Hashtrud. http://api.openweathermap.org/data/2.5/weather?q=hashtrud,ir&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 148
    City: Lebu. http://api.openweathermap.org/data/2.5/weather?q=lebu,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Santiago de Cao. http://api.openweathermap.org/data/2.5/weather?q=santiago de cao,pe&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 149
    City: Barra dos Coqueiros. http://api.openweathermap.org/data/2.5/weather?q=barra dos coqueiros,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 150
    City: Almaznyy. http://api.openweathermap.org/data/2.5/weather?q=almaznyy,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 151
    City: Bluff. http://api.openweathermap.org/data/2.5/weather?q=bluff,nz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Dikson. http://api.openweathermap.org/data/2.5/weather?q=dikson,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 152
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Banda Aceh. http://api.openweathermap.org/data/2.5/weather?q=banda aceh,id&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 153
    City: Hamilton. http://api.openweathermap.org/data/2.5/weather?q=hamilton,bm&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 154
    City: Mahebourg. http://api.openweathermap.org/data/2.5/weather?q=mahebourg,mu&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Puerto Ayora. http://api.openweathermap.org/data/2.5/weather?q=puerto ayora,ec&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Fairbanks. http://api.openweathermap.org/data/2.5/weather?q=fairbanks,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 155
    City: Carnarvon. http://api.openweathermap.org/data/2.5/weather?q=carnarvon,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Nioki. http://api.openweathermap.org/data/2.5/weather?q=nioki,cd&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 156
    City: Punta Arenas. http://api.openweathermap.org/data/2.5/weather?q=punta arenas,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ribeira Grande. http://api.openweathermap.org/data/2.5/weather?q=ribeira grande,pt&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 157
    City: Kasongo-Lunda. http://api.openweathermap.org/data/2.5/weather?q=kasongo-lunda,cd&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 158
    City: Warrington. http://api.openweathermap.org/data/2.5/weather?q=warrington,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 159
    City: Salalah. http://api.openweathermap.org/data/2.5/weather?q=salalah,om&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 160
    City: Puerto Ayora. http://api.openweathermap.org/data/2.5/weather?q=puerto ayora,ec&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Aykhal. http://api.openweathermap.org/data/2.5/weather?q=aykhal,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 161
    City: Cherskiy. http://api.openweathermap.org/data/2.5/weather?q=cherskiy,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 162
    City: Yellowknife. http://api.openweathermap.org/data/2.5/weather?q=yellowknife,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bluff. http://api.openweathermap.org/data/2.5/weather?q=bluff,nz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Eureka. http://api.openweathermap.org/data/2.5/weather?q=eureka,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 163
    City: Qaanaaq. http://api.openweathermap.org/data/2.5/weather?q=qaanaaq,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 164
    City: East London. http://api.openweathermap.org/data/2.5/weather?q=east london,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 165
    City: Padang. http://api.openweathermap.org/data/2.5/weather?q=padang,id&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Severo-Kurilsk. http://api.openweathermap.org/data/2.5/weather?q=severo-kurilsk,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 166
    City: Yellowknife. http://api.openweathermap.org/data/2.5/weather?q=yellowknife,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Nabire. http://api.openweathermap.org/data/2.5/weather?q=nabire,id&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 167
    City: New Norfolk. http://api.openweathermap.org/data/2.5/weather?q=new norfolk,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Okhotsk. http://api.openweathermap.org/data/2.5/weather?q=okhotsk,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 168
    City: Mawlaik. http://api.openweathermap.org/data/2.5/weather?q=mawlaik,mm&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 169
    City: Qaanaaq. http://api.openweathermap.org/data/2.5/weather?q=qaanaaq,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Noyabrsk. http://api.openweathermap.org/data/2.5/weather?q=noyabrsk,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 170
    City: Kashi. http://api.openweathermap.org/data/2.5/weather?q=kashi,cn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 171
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Cidreira. http://api.openweathermap.org/data/2.5/weather?q=cidreira,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 172
    City: Tautira. http://api.openweathermap.org/data/2.5/weather?q=tautira,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 173
    City: Kombissiri. http://api.openweathermap.org/data/2.5/weather?q=kombissiri,bf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 174
    City: Arraial do Cabo. http://api.openweathermap.org/data/2.5/weather?q=arraial do cabo,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 175
    City: Vostok. http://api.openweathermap.org/data/2.5/weather?q=vostok,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 176
    City: Atuona. http://api.openweathermap.org/data/2.5/weather?q=atuona,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Puerto Ayora. http://api.openweathermap.org/data/2.5/weather?q=puerto ayora,ec&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bambous Virieux. http://api.openweathermap.org/data/2.5/weather?q=bambous virieux,mu&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Lorengau. http://api.openweathermap.org/data/2.5/weather?q=lorengau,pg&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Atar. http://api.openweathermap.org/data/2.5/weather?q=atar,mr&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 177
    City: Almaznyy. http://api.openweathermap.org/data/2.5/weather?q=almaznyy,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kodiak. http://api.openweathermap.org/data/2.5/weather?q=kodiak,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 178
    City: Taltal. http://api.openweathermap.org/data/2.5/weather?q=taltal,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 179
    City: Talnakh. http://api.openweathermap.org/data/2.5/weather?q=talnakh,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 180
    City: Neuquen. http://api.openweathermap.org/data/2.5/weather?q=neuquen,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 181
    City: Atuona. http://api.openweathermap.org/data/2.5/weather?q=atuona,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Hermanus. http://api.openweathermap.org/data/2.5/weather?q=hermanus,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Yellowknife. http://api.openweathermap.org/data/2.5/weather?q=yellowknife,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rocha. http://api.openweathermap.org/data/2.5/weather?q=rocha,uy&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 182
    City: Boddam. http://api.openweathermap.org/data/2.5/weather?q=boddam,gb&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 183
    City: Yellowknife. http://api.openweathermap.org/data/2.5/weather?q=yellowknife,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Hermanus. http://api.openweathermap.org/data/2.5/weather?q=hermanus,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Lazaro Cardenas. http://api.openweathermap.org/data/2.5/weather?q=lazaro cardenas,mx&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 184
    City: Arraial do Cabo. http://api.openweathermap.org/data/2.5/weather?q=arraial do cabo,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Puerto Ayora. http://api.openweathermap.org/data/2.5/weather?q=puerto ayora,ec&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Saldanha. http://api.openweathermap.org/data/2.5/weather?q=saldanha,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Saint-Philippe. http://api.openweathermap.org/data/2.5/weather?q=saint-philippe,re&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 185
    City: Punta Arenas. http://api.openweathermap.org/data/2.5/weather?q=punta arenas,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Catacamas. http://api.openweathermap.org/data/2.5/weather?q=catacamas,hn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 186
    City: Cherskiy. http://api.openweathermap.org/data/2.5/weather?q=cherskiy,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Castro. http://api.openweathermap.org/data/2.5/weather?q=castro,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 187
    City: ChengDe. http://api.openweathermap.org/data/2.5/weather?q=chengde,cn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 188
    City: Port Lincoln. http://api.openweathermap.org/data/2.5/weather?q=port lincoln,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 189
    City: Lebu. http://api.openweathermap.org/data/2.5/weather?q=lebu,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Natal. http://api.openweathermap.org/data/2.5/weather?q=natal,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 190
    City: Hermanus. http://api.openweathermap.org/data/2.5/weather?q=hermanus,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Coahuayana. http://api.openweathermap.org/data/2.5/weather?q=coahuayana,mx&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 191
    City: Coahuayana. http://api.openweathermap.org/data/2.5/weather?q=coahuayana,mx&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Nikolskoye. http://api.openweathermap.org/data/2.5/weather?q=nikolskoye,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 192
    City: Baghmara. http://api.openweathermap.org/data/2.5/weather?q=baghmara,in&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 193
    City: Maniitsoq. http://api.openweathermap.org/data/2.5/weather?q=maniitsoq,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 194
    City: Taoudenni. http://api.openweathermap.org/data/2.5/weather?q=taoudenni,ml&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Cape Town. http://api.openweathermap.org/data/2.5/weather?q=cape town,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 195
    City: Timra. http://api.openweathermap.org/data/2.5/weather?q=timra,se&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 196
    City: Jalu. http://api.openweathermap.org/data/2.5/weather?q=jalu,ly&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Aranos. http://api.openweathermap.org/data/2.5/weather?q=aranos,na&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 197
    City: Qaanaaq. http://api.openweathermap.org/data/2.5/weather?q=qaanaaq,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Male. http://api.openweathermap.org/data/2.5/weather?q=male,mv&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 198
    City: Necochea. http://api.openweathermap.org/data/2.5/weather?q=necochea,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 199
    City: Port Elizabeth. http://api.openweathermap.org/data/2.5/weather?q=port elizabeth,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Yulara. http://api.openweathermap.org/data/2.5/weather?q=yulara,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Mar del Plata. http://api.openweathermap.org/data/2.5/weather?q=mar del plata,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Coihaique. http://api.openweathermap.org/data/2.5/weather?q=coihaique,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 200
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Balkanabat. http://api.openweathermap.org/data/2.5/weather?q=balkanabat,tm&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 201
    City: Lima. http://api.openweathermap.org/data/2.5/weather?q=lima,pe&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 202
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Atambua. http://api.openweathermap.org/data/2.5/weather?q=atambua,id&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 203
    City: Kosonsoy. http://api.openweathermap.org/data/2.5/weather?q=kosonsoy,uz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 204
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ilulissat. http://api.openweathermap.org/data/2.5/weather?q=ilulissat,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 205
    City: Krasnaya Zarya. http://api.openweathermap.org/data/2.5/weather?q=krasnaya zarya,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 206
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Along. http://api.openweathermap.org/data/2.5/weather?q=along,in&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 207
    City: Port Alfred. http://api.openweathermap.org/data/2.5/weather?q=port alfred,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Suluq. http://api.openweathermap.org/data/2.5/weather?q=suluq,ly&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 208
    City: La Ronge. http://api.openweathermap.org/data/2.5/weather?q=la ronge,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 209
    City: Muhos. http://api.openweathermap.org/data/2.5/weather?q=muhos,fi&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 210
    City: Punta Arenas. http://api.openweathermap.org/data/2.5/weather?q=punta arenas,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Cidreira. http://api.openweathermap.org/data/2.5/weather?q=cidreira,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Jamestown. http://api.openweathermap.org/data/2.5/weather?q=jamestown,sh&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Chaves. http://api.openweathermap.org/data/2.5/weather?q=chaves,pt&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 211
    City: Nikolskoye. http://api.openweathermap.org/data/2.5/weather?q=nikolskoye,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Torbay. http://api.openweathermap.org/data/2.5/weather?q=torbay,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Souillac. http://api.openweathermap.org/data/2.5/weather?q=souillac,mu&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 212
    City: Faanui. http://api.openweathermap.org/data/2.5/weather?q=faanui,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Hermanus. http://api.openweathermap.org/data/2.5/weather?q=hermanus,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ponta Delgada. http://api.openweathermap.org/data/2.5/weather?q=ponta delgada,pt&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 213
    City: Arraial do Cabo. http://api.openweathermap.org/data/2.5/weather?q=arraial do cabo,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Faanui. http://api.openweathermap.org/data/2.5/weather?q=faanui,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Hithadhoo. http://api.openweathermap.org/data/2.5/weather?q=hithadhoo,mv&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 214
    City: Santiago del Estero. http://api.openweathermap.org/data/2.5/weather?q=santiago del estero,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 215
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: New Norfolk. http://api.openweathermap.org/data/2.5/weather?q=new norfolk,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bosaso. http://api.openweathermap.org/data/2.5/weather?q=bosaso,so&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Flinders. http://api.openweathermap.org/data/2.5/weather?q=flinders,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 216
    City: Waingapu. http://api.openweathermap.org/data/2.5/weather?q=waingapu,id&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 217
    City: Jamestown. http://api.openweathermap.org/data/2.5/weather?q=jamestown,sh&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Georgetown. http://api.openweathermap.org/data/2.5/weather?q=georgetown,sh&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 218
    City: Hamilton. http://api.openweathermap.org/data/2.5/weather?q=hamilton,bm&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Mayo. http://api.openweathermap.org/data/2.5/weather?q=mayo,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 219
    City: Souillac. http://api.openweathermap.org/data/2.5/weather?q=souillac,mu&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Maxixe. http://api.openweathermap.org/data/2.5/weather?q=maxixe,mz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 220
    City: Yar-Sale. http://api.openweathermap.org/data/2.5/weather?q=yar-sale,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 221
    City: Vaini. http://api.openweathermap.org/data/2.5/weather?q=vaini,to&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bonavista. http://api.openweathermap.org/data/2.5/weather?q=bonavista,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 222
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Jamestown. http://api.openweathermap.org/data/2.5/weather?q=jamestown,sh&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Vaini. http://api.openweathermap.org/data/2.5/weather?q=vaini,to&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Tezu. http://api.openweathermap.org/data/2.5/weather?q=tezu,in&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 223
    City: Severo-Kurilsk. http://api.openweathermap.org/data/2.5/weather?q=severo-kurilsk,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Port Lincoln. http://api.openweathermap.org/data/2.5/weather?q=port lincoln,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Geraldton. http://api.openweathermap.org/data/2.5/weather?q=geraldton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Vallenar. http://api.openweathermap.org/data/2.5/weather?q=vallenar,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 224
    City: Atuona. http://api.openweathermap.org/data/2.5/weather?q=atuona,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Puerto Ayora. http://api.openweathermap.org/data/2.5/weather?q=puerto ayora,ec&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ogulin. http://api.openweathermap.org/data/2.5/weather?q=ogulin,hr&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 225
    City: East London. http://api.openweathermap.org/data/2.5/weather?q=east london,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Wagar. http://api.openweathermap.org/data/2.5/weather?q=wagar,sd&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 226
    City: Georgetown. http://api.openweathermap.org/data/2.5/weather?q=georgetown,sh&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ornskoldsvik. http://api.openweathermap.org/data/2.5/weather?q=ornskoldsvik,se&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 227
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Auki. http://api.openweathermap.org/data/2.5/weather?q=auki,sb&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Carnarvon. http://api.openweathermap.org/data/2.5/weather?q=carnarvon,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Albany. http://api.openweathermap.org/data/2.5/weather?q=albany,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ust-Kalmanka. http://api.openweathermap.org/data/2.5/weather?q=ust-kalmanka,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 228
    City: Cape Town. http://api.openweathermap.org/data/2.5/weather?q=cape town,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kirakira. http://api.openweathermap.org/data/2.5/weather?q=kirakira,sb&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 229
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Puerto Ayora. http://api.openweathermap.org/data/2.5/weather?q=puerto ayora,ec&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Akdepe. http://api.openweathermap.org/data/2.5/weather?q=akdepe,tm&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 230
    City: Moron. http://api.openweathermap.org/data/2.5/weather?q=moron,mn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 231
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Haines Junction. http://api.openweathermap.org/data/2.5/weather?q=haines junction,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 232
    City: Olenegorsk. http://api.openweathermap.org/data/2.5/weather?q=olenegorsk,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 233
    City: Dicabisagan. http://api.openweathermap.org/data/2.5/weather?q=dicabisagan,ph&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 234
    City: Aberystwyth. http://api.openweathermap.org/data/2.5/weather?q=aberystwyth,gb&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 235
    City: Bunbury. http://api.openweathermap.org/data/2.5/weather?q=bunbury,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 236
    City: Albany. http://api.openweathermap.org/data/2.5/weather?q=albany,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Albany. http://api.openweathermap.org/data/2.5/weather?q=albany,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Vaini. http://api.openweathermap.org/data/2.5/weather?q=vaini,to&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Mahebourg. http://api.openweathermap.org/data/2.5/weather?q=mahebourg,mu&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rassvet. http://api.openweathermap.org/data/2.5/weather?q=rassvet,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 237
    City: Thompson. http://api.openweathermap.org/data/2.5/weather?q=thompson,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Vaini. http://api.openweathermap.org/data/2.5/weather?q=vaini,to&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ruteng. http://api.openweathermap.org/data/2.5/weather?q=ruteng,id&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 238
    City: Serebryansk. http://api.openweathermap.org/data/2.5/weather?q=serebryansk,kz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 239
    City: Lebu. http://api.openweathermap.org/data/2.5/weather?q=lebu,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Port Alfred. http://api.openweathermap.org/data/2.5/weather?q=port alfred,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Barrow. http://api.openweathermap.org/data/2.5/weather?q=barrow,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 240
    City: Husavik. http://api.openweathermap.org/data/2.5/weather?q=husavik,is&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 241
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Biltine. http://api.openweathermap.org/data/2.5/weather?q=biltine,td&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 242
    City: Butaritari. http://api.openweathermap.org/data/2.5/weather?q=butaritari,ki&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Atuona. http://api.openweathermap.org/data/2.5/weather?q=atuona,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Pascagoula. http://api.openweathermap.org/data/2.5/weather?q=pascagoula,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 243
    City: Ponta do Sol. http://api.openweathermap.org/data/2.5/weather?q=ponta do sol,cv&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Tripoli. http://api.openweathermap.org/data/2.5/weather?q=tripoli,ly&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 244
    City: Aden. http://api.openweathermap.org/data/2.5/weather?q=aden,ye&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 245
    City: Vaini. http://api.openweathermap.org/data/2.5/weather?q=vaini,to&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Georgetown. http://api.openweathermap.org/data/2.5/weather?q=georgetown,sh&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Georgetown. http://api.openweathermap.org/data/2.5/weather?q=georgetown,sh&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Pangnirtung. http://api.openweathermap.org/data/2.5/weather?q=pangnirtung,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Punta Arenas. http://api.openweathermap.org/data/2.5/weather?q=punta arenas,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Hobart. http://api.openweathermap.org/data/2.5/weather?q=hobart,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 246
    City: San Policarpo. http://api.openweathermap.org/data/2.5/weather?q=san policarpo,ph&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 247
    City: Havre-Saint-Pierre. http://api.openweathermap.org/data/2.5/weather?q=havre-saint-pierre,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 248
    City: Punta Arenas. http://api.openweathermap.org/data/2.5/weather?q=punta arenas,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Upernavik. http://api.openweathermap.org/data/2.5/weather?q=upernavik,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 249
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Dongfeng. http://api.openweathermap.org/data/2.5/weather?q=dongfeng,cn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 250
    City: Hilo. http://api.openweathermap.org/data/2.5/weather?q=hilo,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Mar del Plata. http://api.openweathermap.org/data/2.5/weather?q=mar del plata,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Gobabis. http://api.openweathermap.org/data/2.5/weather?q=gobabis,na&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 251
    City: Barrow. http://api.openweathermap.org/data/2.5/weather?q=barrow,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kruisfontein. http://api.openweathermap.org/data/2.5/weather?q=kruisfontein,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Talnakh. http://api.openweathermap.org/data/2.5/weather?q=talnakh,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kodiak. http://api.openweathermap.org/data/2.5/weather?q=kodiak,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: East London. http://api.openweathermap.org/data/2.5/weather?q=east london,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Hamilton. http://api.openweathermap.org/data/2.5/weather?q=hamilton,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Sibiti. http://api.openweathermap.org/data/2.5/weather?q=sibiti,cg&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 252
    City: Bredasdorp. http://api.openweathermap.org/data/2.5/weather?q=bredasdorp,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rawson. http://api.openweathermap.org/data/2.5/weather?q=rawson,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 253
    City: Alice Springs. http://api.openweathermap.org/data/2.5/weather?q=alice springs,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 254
    City: Bredasdorp. http://api.openweathermap.org/data/2.5/weather?q=bredasdorp,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Verkhoyansk. http://api.openweathermap.org/data/2.5/weather?q=verkhoyansk,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Port Alfred. http://api.openweathermap.org/data/2.5/weather?q=port alfred,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Pozo Colorado. http://api.openweathermap.org/data/2.5/weather?q=pozo colorado,py&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 255
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Port Alfred. http://api.openweathermap.org/data/2.5/weather?q=port alfred,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Georgetown. http://api.openweathermap.org/data/2.5/weather?q=georgetown,sh&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Padang. http://api.openweathermap.org/data/2.5/weather?q=padang,id&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Maua. http://api.openweathermap.org/data/2.5/weather?q=maua,ke&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 256
    City: The Pas. http://api.openweathermap.org/data/2.5/weather?q=the pas,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 257
    City: West Wendover. http://api.openweathermap.org/data/2.5/weather?q=west wendover,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 258
    City: Iqaluit. http://api.openweathermap.org/data/2.5/weather?q=iqaluit,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 259
    City: Shimoda. http://api.openweathermap.org/data/2.5/weather?q=shimoda,jp&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 260
    City: Butaritari. http://api.openweathermap.org/data/2.5/weather?q=butaritari,ki&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Brae. http://api.openweathermap.org/data/2.5/weather?q=brae,gb&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 261
    City: Paamiut. http://api.openweathermap.org/data/2.5/weather?q=paamiut,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 262
    City: Souillac. http://api.openweathermap.org/data/2.5/weather?q=souillac,mu&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Puerto Ayora. http://api.openweathermap.org/data/2.5/weather?q=puerto ayora,ec&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Provideniya. http://api.openweathermap.org/data/2.5/weather?q=provideniya,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 263
    City: Wattegama. http://api.openweathermap.org/data/2.5/weather?q=wattegama,lk&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 264
    City: Dubbo. http://api.openweathermap.org/data/2.5/weather?q=dubbo,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 265
    City: Necochea. http://api.openweathermap.org/data/2.5/weather?q=necochea,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ponta do Sol. http://api.openweathermap.org/data/2.5/weather?q=ponta do sol,cv&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Storforshei. http://api.openweathermap.org/data/2.5/weather?q=storforshei,no&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 266
    City: Bilma. http://api.openweathermap.org/data/2.5/weather?q=bilma,ne&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 267
    City: Rio Grande. http://api.openweathermap.org/data/2.5/weather?q=rio grande,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Atuona. http://api.openweathermap.org/data/2.5/weather?q=atuona,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ulaangom. http://api.openweathermap.org/data/2.5/weather?q=ulaangom,mn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 268
    City: Tanete. http://api.openweathermap.org/data/2.5/weather?q=tanete,id&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 269
    City: Cidreira. http://api.openweathermap.org/data/2.5/weather?q=cidreira,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Jamestown. http://api.openweathermap.org/data/2.5/weather?q=jamestown,sh&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Vila Franca do Campo. http://api.openweathermap.org/data/2.5/weather?q=vila franca do campo,pt&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Dong Hoi. http://api.openweathermap.org/data/2.5/weather?q=dong hoi,vn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 270
    City: Hermanus. http://api.openweathermap.org/data/2.5/weather?q=hermanus,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Elmadag. http://api.openweathermap.org/data/2.5/weather?q=elmadag,tr&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 271
    City: Talaya. http://api.openweathermap.org/data/2.5/weather?q=talaya,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 272
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kaputa. http://api.openweathermap.org/data/2.5/weather?q=kaputa,zm&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 273
    City: Egvekinot. http://api.openweathermap.org/data/2.5/weather?q=egvekinot,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 274
    City: Hilo. http://api.openweathermap.org/data/2.5/weather?q=hilo,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rumoi. http://api.openweathermap.org/data/2.5/weather?q=rumoi,jp&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 275
    City: Sulina. http://api.openweathermap.org/data/2.5/weather?q=sulina,ro&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 276
    City: Ancud. http://api.openweathermap.org/data/2.5/weather?q=ancud,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bambanglipuro. http://api.openweathermap.org/data/2.5/weather?q=bambanglipuro,id&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 277
    City: Punta Arenas. http://api.openweathermap.org/data/2.5/weather?q=punta arenas,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Georgetown. http://api.openweathermap.org/data/2.5/weather?q=georgetown,sh&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Neryungri. http://api.openweathermap.org/data/2.5/weather?q=neryungri,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 278
    City: Benguela. http://api.openweathermap.org/data/2.5/weather?q=benguela,ao&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 279
    City: Kruisfontein. http://api.openweathermap.org/data/2.5/weather?q=kruisfontein,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kapaa. http://api.openweathermap.org/data/2.5/weather?q=kapaa,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Necochea. http://api.openweathermap.org/data/2.5/weather?q=necochea,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Georgetown. http://api.openweathermap.org/data/2.5/weather?q=georgetown,gy&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Qasigiannguit. http://api.openweathermap.org/data/2.5/weather?q=qasigiannguit,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 280
    City: Jamestown. http://api.openweathermap.org/data/2.5/weather?q=jamestown,sh&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bluff. http://api.openweathermap.org/data/2.5/weather?q=bluff,nz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Hobart. http://api.openweathermap.org/data/2.5/weather?q=hobart,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Punta Arenas. http://api.openweathermap.org/data/2.5/weather?q=punta arenas,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Nanortalik. http://api.openweathermap.org/data/2.5/weather?q=nanortalik,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Alyangula. http://api.openweathermap.org/data/2.5/weather?q=alyangula,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Cape Town. http://api.openweathermap.org/data/2.5/weather?q=cape town,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bambous Virieux. http://api.openweathermap.org/data/2.5/weather?q=bambous virieux,mu&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Esperanza. http://api.openweathermap.org/data/2.5/weather?q=esperanza,ph&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 281
    City: Port Elizabeth. http://api.openweathermap.org/data/2.5/weather?q=port elizabeth,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kudymkar. http://api.openweathermap.org/data/2.5/weather?q=kudymkar,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 282
    City: Tomatlan. http://api.openweathermap.org/data/2.5/weather?q=tomatlan,mx&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 283
    City: Puerto Ayora. http://api.openweathermap.org/data/2.5/weather?q=puerto ayora,ec&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Taoudenni. http://api.openweathermap.org/data/2.5/weather?q=taoudenni,ml&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Tasiilaq. http://api.openweathermap.org/data/2.5/weather?q=tasiilaq,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Avarua. http://api.openweathermap.org/data/2.5/weather?q=avarua,ck&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Port Pirie. http://api.openweathermap.org/data/2.5/weather?q=port pirie,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 284
    City: Barahona. http://api.openweathermap.org/data/2.5/weather?q=barahona,do&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 285
    City: Kjollefjord. http://api.openweathermap.org/data/2.5/weather?q=kjollefjord,no&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 286
    City: Thompson. http://api.openweathermap.org/data/2.5/weather?q=thompson,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Puerto Ayora. http://api.openweathermap.org/data/2.5/weather?q=puerto ayora,ec&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Port Elizabeth. http://api.openweathermap.org/data/2.5/weather?q=port elizabeth,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Khatanga. http://api.openweathermap.org/data/2.5/weather?q=khatanga,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 287
    City: Kodiak. http://api.openweathermap.org/data/2.5/weather?q=kodiak,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Pangkalanbuun. http://api.openweathermap.org/data/2.5/weather?q=pangkalanbuun,id&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 288
    City: Paciran. http://api.openweathermap.org/data/2.5/weather?q=paciran,id&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 289
    City: Souillac. http://api.openweathermap.org/data/2.5/weather?q=souillac,mu&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Yeppoon. http://api.openweathermap.org/data/2.5/weather?q=yeppoon,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 290
    City: Oyama. http://api.openweathermap.org/data/2.5/weather?q=oyama,jp&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 291
    City: Callaway. http://api.openweathermap.org/data/2.5/weather?q=callaway,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 292
    City: Yellowknife. http://api.openweathermap.org/data/2.5/weather?q=yellowknife,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ponta do Sol. http://api.openweathermap.org/data/2.5/weather?q=ponta do sol,cv&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Hilo. http://api.openweathermap.org/data/2.5/weather?q=hilo,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ballina. http://api.openweathermap.org/data/2.5/weather?q=ballina,ie&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 293
    City: Salalah. http://api.openweathermap.org/data/2.5/weather?q=salalah,om&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Cape Town. http://api.openweathermap.org/data/2.5/weather?q=cape town,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Atbasar. http://api.openweathermap.org/data/2.5/weather?q=atbasar,kz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 294
    City: Lakes Entrance. http://api.openweathermap.org/data/2.5/weather?q=lakes entrance,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 295
    City: Havre-Saint-Pierre. http://api.openweathermap.org/data/2.5/weather?q=havre-saint-pierre,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Jamestown. http://api.openweathermap.org/data/2.5/weather?q=jamestown,sh&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Severo-Kurilsk. http://api.openweathermap.org/data/2.5/weather?q=severo-kurilsk,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Butaritari. http://api.openweathermap.org/data/2.5/weather?q=butaritari,ki&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Port Alfred. http://api.openweathermap.org/data/2.5/weather?q=port alfred,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kiruna. http://api.openweathermap.org/data/2.5/weather?q=kiruna,se&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 296
    City: Lagoa. http://api.openweathermap.org/data/2.5/weather?q=lagoa,pt&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Punta Arenas. http://api.openweathermap.org/data/2.5/weather?q=punta arenas,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Loandjili. http://api.openweathermap.org/data/2.5/weather?q=loandjili,cg&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 297
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Richards Bay. http://api.openweathermap.org/data/2.5/weather?q=richards bay,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Khatanga. http://api.openweathermap.org/data/2.5/weather?q=khatanga,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Karratha. http://api.openweathermap.org/data/2.5/weather?q=karratha,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 298
    City: New Norfolk. http://api.openweathermap.org/data/2.5/weather?q=new norfolk,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Nikolskoye. http://api.openweathermap.org/data/2.5/weather?q=nikolskoye,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bredasdorp. http://api.openweathermap.org/data/2.5/weather?q=bredasdorp,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Daru. http://api.openweathermap.org/data/2.5/weather?q=daru,pg&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 299
    City: Vila Velha. http://api.openweathermap.org/data/2.5/weather?q=vila velha,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 300
    City: Aleksandrov Gay. http://api.openweathermap.org/data/2.5/weather?q=aleksandrov gay,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 301
    City: Tessalit. http://api.openweathermap.org/data/2.5/weather?q=tessalit,ml&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 302
    City: Arraial do Cabo. http://api.openweathermap.org/data/2.5/weather?q=arraial do cabo,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Tweed. http://api.openweathermap.org/data/2.5/weather?q=tweed,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 303
    City: Albany. http://api.openweathermap.org/data/2.5/weather?q=albany,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Jiuquan. http://api.openweathermap.org/data/2.5/weather?q=jiuquan,cn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 304
    City: Stokmarknes. http://api.openweathermap.org/data/2.5/weather?q=stokmarknes,no&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 305
    City: Champerico. http://api.openweathermap.org/data/2.5/weather?q=champerico,gt&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 306
    City: Hermanus. http://api.openweathermap.org/data/2.5/weather?q=hermanus,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Cacapava do Sul. http://api.openweathermap.org/data/2.5/weather?q=cacapava do sul,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 307
    City: Albany. http://api.openweathermap.org/data/2.5/weather?q=albany,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bluff. http://api.openweathermap.org/data/2.5/weather?q=bluff,nz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ribeira Grande. http://api.openweathermap.org/data/2.5/weather?q=ribeira grande,pt&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Flinders. http://api.openweathermap.org/data/2.5/weather?q=flinders,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Tasiilaq. http://api.openweathermap.org/data/2.5/weather?q=tasiilaq,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Quatre Cocos. http://api.openweathermap.org/data/2.5/weather?q=quatre cocos,mu&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 308
    City: Kapaa. http://api.openweathermap.org/data/2.5/weather?q=kapaa,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Pedernales. http://api.openweathermap.org/data/2.5/weather?q=pedernales,ec&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 309
    City: Vila Velha. http://api.openweathermap.org/data/2.5/weather?q=vila velha,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Vaini. http://api.openweathermap.org/data/2.5/weather?q=vaini,to&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Nizwa. http://api.openweathermap.org/data/2.5/weather?q=nizwa,om&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 310
    City: Kamenka. http://api.openweathermap.org/data/2.5/weather?q=kamenka,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 311
    City: Kardamaina. http://api.openweathermap.org/data/2.5/weather?q=kardamaina,gr&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 312
    City: Lebu. http://api.openweathermap.org/data/2.5/weather?q=lebu,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Saskylakh. http://api.openweathermap.org/data/2.5/weather?q=saskylakh,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Collie. http://api.openweathermap.org/data/2.5/weather?q=collie,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 313
    City: Vaini. http://api.openweathermap.org/data/2.5/weather?q=vaini,to&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Jamestown. http://api.openweathermap.org/data/2.5/weather?q=jamestown,sh&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Barcelos. http://api.openweathermap.org/data/2.5/weather?q=barcelos,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 314
    City: Mangan. http://api.openweathermap.org/data/2.5/weather?q=mangan,in&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 315
    City: Bangolo. http://api.openweathermap.org/data/2.5/weather?q=bangolo,ci&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 316
    City: New Norfolk. http://api.openweathermap.org/data/2.5/weather?q=new norfolk,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Derzhavinsk. http://api.openweathermap.org/data/2.5/weather?q=derzhavinsk,kz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 317
    City: Znojmo. http://api.openweathermap.org/data/2.5/weather?q=znojmo,cz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 318
    City: Acapulco. http://api.openweathermap.org/data/2.5/weather?q=acapulco,mx&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 319
    City: Konya. http://api.openweathermap.org/data/2.5/weather?q=konya,tr&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 320
    City: Talnakh. http://api.openweathermap.org/data/2.5/weather?q=talnakh,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Port Elizabeth. http://api.openweathermap.org/data/2.5/weather?q=port elizabeth,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Upernavik. http://api.openweathermap.org/data/2.5/weather?q=upernavik,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Synya. http://api.openweathermap.org/data/2.5/weather?q=synya,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 321
    City: Hilo. http://api.openweathermap.org/data/2.5/weather?q=hilo,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Albany. http://api.openweathermap.org/data/2.5/weather?q=albany,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Mahebourg. http://api.openweathermap.org/data/2.5/weather?q=mahebourg,mu&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Hirara. http://api.openweathermap.org/data/2.5/weather?q=hirara,jp&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Juneau. http://api.openweathermap.org/data/2.5/weather?q=juneau,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 322
    City: Varhaug. http://api.openweathermap.org/data/2.5/weather?q=varhaug,no&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 323
    City: Turukhansk. http://api.openweathermap.org/data/2.5/weather?q=turukhansk,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 324
    City: Bone. http://api.openweathermap.org/data/2.5/weather?q=bone,id&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 325
    City: Qaanaaq. http://api.openweathermap.org/data/2.5/weather?q=qaanaaq,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Arraial do Cabo. http://api.openweathermap.org/data/2.5/weather?q=arraial do cabo,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Victoria. http://api.openweathermap.org/data/2.5/weather?q=victoria,sc&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Georgetown. http://api.openweathermap.org/data/2.5/weather?q=georgetown,sh&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ribeira Grande. http://api.openweathermap.org/data/2.5/weather?q=ribeira grande,pt&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Aykhal. http://api.openweathermap.org/data/2.5/weather?q=aykhal,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Dingle. http://api.openweathermap.org/data/2.5/weather?q=dingle,ie&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 326
    City: Clyde River. http://api.openweathermap.org/data/2.5/weather?q=clyde river,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 327
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bluff. http://api.openweathermap.org/data/2.5/weather?q=bluff,nz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Troitskoye. http://api.openweathermap.org/data/2.5/weather?q=troitskoye,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 328
    City: Butaritari. http://api.openweathermap.org/data/2.5/weather?q=butaritari,ki&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Serov. http://api.openweathermap.org/data/2.5/weather?q=serov,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 329
    City: Beringovskiy. http://api.openweathermap.org/data/2.5/weather?q=beringovskiy,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 330
    City: Narsaq. http://api.openweathermap.org/data/2.5/weather?q=narsaq,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 331
    City: Angoche. http://api.openweathermap.org/data/2.5/weather?q=angoche,mz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 332
    City: Hot Springs. http://api.openweathermap.org/data/2.5/weather?q=hot springs,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 333
    City: Vestmanna. http://api.openweathermap.org/data/2.5/weather?q=vestmanna,fo&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 334
    City: Mbuji-Mayi. http://api.openweathermap.org/data/2.5/weather?q=mbuji-mayi,cd&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 335
    City: Jacareacanga. http://api.openweathermap.org/data/2.5/weather?q=jacareacanga,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 336
    City: Tuktoyaktuk. http://api.openweathermap.org/data/2.5/weather?q=tuktoyaktuk,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Qaanaaq. http://api.openweathermap.org/data/2.5/weather?q=qaanaaq,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Caiaponia. http://api.openweathermap.org/data/2.5/weather?q=caiaponia,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 337
    City: Punta Arenas. http://api.openweathermap.org/data/2.5/weather?q=punta arenas,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Vaini. http://api.openweathermap.org/data/2.5/weather?q=vaini,to&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rexburg. http://api.openweathermap.org/data/2.5/weather?q=rexburg,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 338
    City: Jamestown. http://api.openweathermap.org/data/2.5/weather?q=jamestown,sh&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Nikolskoye. http://api.openweathermap.org/data/2.5/weather?q=nikolskoye,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Spas-Demensk. http://api.openweathermap.org/data/2.5/weather?q=spas-demensk,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 339
    City: Hobart. http://api.openweathermap.org/data/2.5/weather?q=hobart,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Hilo. http://api.openweathermap.org/data/2.5/weather?q=hilo,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Longyearbyen. http://api.openweathermap.org/data/2.5/weather?q=longyearbyen,sj&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 340
    City: Watertown. http://api.openweathermap.org/data/2.5/weather?q=watertown,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 341
    City: Aginskoye. http://api.openweathermap.org/data/2.5/weather?q=aginskoye,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 342
    City: Hilo. http://api.openweathermap.org/data/2.5/weather?q=hilo,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Quatre Cocos. http://api.openweathermap.org/data/2.5/weather?q=quatre cocos,mu&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Barrow. http://api.openweathermap.org/data/2.5/weather?q=barrow,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Raymondville. http://api.openweathermap.org/data/2.5/weather?q=raymondville,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 343
    City: Vanimo. http://api.openweathermap.org/data/2.5/weather?q=vanimo,pg&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 344
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Dunedin. http://api.openweathermap.org/data/2.5/weather?q=dunedin,nz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 345
    City: Finschhafen. http://api.openweathermap.org/data/2.5/weather?q=finschhafen,pg&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 346
    City: Mugla. http://api.openweathermap.org/data/2.5/weather?q=mugla,tr&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 347
    City: Geraldton. http://api.openweathermap.org/data/2.5/weather?q=geraldton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Westport. http://api.openweathermap.org/data/2.5/weather?q=westport,ie&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Leshukonskoye. http://api.openweathermap.org/data/2.5/weather?q=leshukonskoye,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 348
    City: Hasaki. http://api.openweathermap.org/data/2.5/weather?q=hasaki,jp&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Carutapera. http://api.openweathermap.org/data/2.5/weather?q=carutapera,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 349
    City: Cape Town. http://api.openweathermap.org/data/2.5/weather?q=cape town,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Narsaq. http://api.openweathermap.org/data/2.5/weather?q=narsaq,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Avarua. http://api.openweathermap.org/data/2.5/weather?q=avarua,ck&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kaitangata. http://api.openweathermap.org/data/2.5/weather?q=kaitangata,nz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 350
    City: Upernavik. http://api.openweathermap.org/data/2.5/weather?q=upernavik,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Oistins. http://api.openweathermap.org/data/2.5/weather?q=oistins,bb&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 351
    City: Ribeira Grande. http://api.openweathermap.org/data/2.5/weather?q=ribeira grande,pt&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Tasiilaq. http://api.openweathermap.org/data/2.5/weather?q=tasiilaq,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Souillac. http://api.openweathermap.org/data/2.5/weather?q=souillac,mu&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Harper. http://api.openweathermap.org/data/2.5/weather?q=harper,lr&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 352
    City: Raudeberg. http://api.openweathermap.org/data/2.5/weather?q=raudeberg,no&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 353
    City: Palmer. http://api.openweathermap.org/data/2.5/weather?q=palmer,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 354
    City: Mersing. http://api.openweathermap.org/data/2.5/weather?q=mersing,my&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 355
    City: Pinega. http://api.openweathermap.org/data/2.5/weather?q=pinega,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 356
    City: Kaeo. http://api.openweathermap.org/data/2.5/weather?q=kaeo,nz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 357
    City: Vanimo. http://api.openweathermap.org/data/2.5/weather?q=vanimo,pg&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Avarua. http://api.openweathermap.org/data/2.5/weather?q=avarua,ck&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Berlevag. http://api.openweathermap.org/data/2.5/weather?q=berlevag,no&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 358
    City: Mahebourg. http://api.openweathermap.org/data/2.5/weather?q=mahebourg,mu&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bonnyville. http://api.openweathermap.org/data/2.5/weather?q=bonnyville,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 359
    City: Punta Arenas. http://api.openweathermap.org/data/2.5/weather?q=punta arenas,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Jamestown. http://api.openweathermap.org/data/2.5/weather?q=jamestown,sh&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ulladulla. http://api.openweathermap.org/data/2.5/weather?q=ulladulla,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 360
    City: Tasiilaq. http://api.openweathermap.org/data/2.5/weather?q=tasiilaq,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Mangrol. http://api.openweathermap.org/data/2.5/weather?q=mangrol,in&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 361
    City: Atuona. http://api.openweathermap.org/data/2.5/weather?q=atuona,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rodrigues Alves. http://api.openweathermap.org/data/2.5/weather?q=rodrigues alves,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 362
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Chokurdakh. http://api.openweathermap.org/data/2.5/weather?q=chokurdakh,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Port Hardy. http://api.openweathermap.org/data/2.5/weather?q=port hardy,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 363
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kapaa. http://api.openweathermap.org/data/2.5/weather?q=kapaa,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Khatanga. http://api.openweathermap.org/data/2.5/weather?q=khatanga,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Cape Town. http://api.openweathermap.org/data/2.5/weather?q=cape town,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Cherskiy. http://api.openweathermap.org/data/2.5/weather?q=cherskiy,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Qaanaaq. http://api.openweathermap.org/data/2.5/weather?q=qaanaaq,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Makakilo City. http://api.openweathermap.org/data/2.5/weather?q=makakilo city,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 364
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Terra Santa. http://api.openweathermap.org/data/2.5/weather?q=terra santa,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 365
    City: Qaanaaq. http://api.openweathermap.org/data/2.5/weather?q=qaanaaq,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Hermanus. http://api.openweathermap.org/data/2.5/weather?q=hermanus,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Vaini. http://api.openweathermap.org/data/2.5/weather?q=vaini,to&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Doka. http://api.openweathermap.org/data/2.5/weather?q=doka,sd&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 366
    City: Stavern. http://api.openweathermap.org/data/2.5/weather?q=stavern,no&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 367
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Norman Wells. http://api.openweathermap.org/data/2.5/weather?q=norman wells,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 368
    City: Barrow. http://api.openweathermap.org/data/2.5/weather?q=barrow,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Margate. http://api.openweathermap.org/data/2.5/weather?q=margate,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 369
    City: Bredasdorp. http://api.openweathermap.org/data/2.5/weather?q=bredasdorp,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Mar del Plata. http://api.openweathermap.org/data/2.5/weather?q=mar del plata,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bluff. http://api.openweathermap.org/data/2.5/weather?q=bluff,nz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Porto-Vecchio. http://api.openweathermap.org/data/2.5/weather?q=porto-vecchio,fr&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 370
    City: Kapaa. http://api.openweathermap.org/data/2.5/weather?q=kapaa,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Vaini. http://api.openweathermap.org/data/2.5/weather?q=vaini,to&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Carnarvon. http://api.openweathermap.org/data/2.5/weather?q=carnarvon,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Lucapa. http://api.openweathermap.org/data/2.5/weather?q=lucapa,ao&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 371
    City: Bluff. http://api.openweathermap.org/data/2.5/weather?q=bluff,nz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Hilo. http://api.openweathermap.org/data/2.5/weather?q=hilo,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Punta Arenas. http://api.openweathermap.org/data/2.5/weather?q=punta arenas,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Am Timan. http://api.openweathermap.org/data/2.5/weather?q=am timan,td&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 372
    City: Tarauaca. http://api.openweathermap.org/data/2.5/weather?q=tarauaca,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 373
    City: Ancud. http://api.openweathermap.org/data/2.5/weather?q=ancud,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Poum. http://api.openweathermap.org/data/2.5/weather?q=poum,nc&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 374
    City: Punta Arenas. http://api.openweathermap.org/data/2.5/weather?q=punta arenas,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Shaowu. http://api.openweathermap.org/data/2.5/weather?q=shaowu,cn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 375
    City: Chernyshevskiy. http://api.openweathermap.org/data/2.5/weather?q=chernyshevskiy,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 376
    City: Saint-Augustin. http://api.openweathermap.org/data/2.5/weather?q=saint-augustin,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 377
    City: Punta Arenas. http://api.openweathermap.org/data/2.5/weather?q=punta arenas,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Esperance. http://api.openweathermap.org/data/2.5/weather?q=esperance,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Nanortalik. http://api.openweathermap.org/data/2.5/weather?q=nanortalik,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Saint-Philippe. http://api.openweathermap.org/data/2.5/weather?q=saint-philippe,re&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Tres Arroyos. http://api.openweathermap.org/data/2.5/weather?q=tres arroyos,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 378
    City: Qaanaaq. http://api.openweathermap.org/data/2.5/weather?q=qaanaaq,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Salsomaggiore Terme. http://api.openweathermap.org/data/2.5/weather?q=salsomaggiore terme,it&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 379
    City: Faanui. http://api.openweathermap.org/data/2.5/weather?q=faanui,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Praia. http://api.openweathermap.org/data/2.5/weather?q=praia,cv&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 380
    City: Fukue. http://api.openweathermap.org/data/2.5/weather?q=fukue,jp&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 381
    City: Sindou. http://api.openweathermap.org/data/2.5/weather?q=sindou,bf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 382
    City: Mount Gambier. http://api.openweathermap.org/data/2.5/weather?q=mount gambier,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 383
    City: Barrow. http://api.openweathermap.org/data/2.5/weather?q=barrow,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Half Moon Bay. http://api.openweathermap.org/data/2.5/weather?q=half moon bay,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 384
    City: Paamiut. http://api.openweathermap.org/data/2.5/weather?q=paamiut,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Atuona. http://api.openweathermap.org/data/2.5/weather?q=atuona,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: College. http://api.openweathermap.org/data/2.5/weather?q=college,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 385
    City: Kodiak. http://api.openweathermap.org/data/2.5/weather?q=kodiak,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Saint-Philippe. http://api.openweathermap.org/data/2.5/weather?q=saint-philippe,re&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Padang. http://api.openweathermap.org/data/2.5/weather?q=padang,id&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Itarema. http://api.openweathermap.org/data/2.5/weather?q=itarema,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 386
    City: Barrow. http://api.openweathermap.org/data/2.5/weather?q=barrow,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Muisne. http://api.openweathermap.org/data/2.5/weather?q=muisne,ec&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 387
    City: Sitka. http://api.openweathermap.org/data/2.5/weather?q=sitka,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 388
    City: Atuona. http://api.openweathermap.org/data/2.5/weather?q=atuona,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Jamestown. http://api.openweathermap.org/data/2.5/weather?q=jamestown,sh&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Erenhot. http://api.openweathermap.org/data/2.5/weather?q=erenhot,cn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 389
    City: Port Alfred. http://api.openweathermap.org/data/2.5/weather?q=port alfred,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Upernavik. http://api.openweathermap.org/data/2.5/weather?q=upernavik,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Geraldton. http://api.openweathermap.org/data/2.5/weather?q=geraldton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Port Alfred. http://api.openweathermap.org/data/2.5/weather?q=port alfred,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kita. http://api.openweathermap.org/data/2.5/weather?q=kita,ml&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 390
    City: Tarakan. http://api.openweathermap.org/data/2.5/weather?q=tarakan,id&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 391
    City: College. http://api.openweathermap.org/data/2.5/weather?q=college,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Padang. http://api.openweathermap.org/data/2.5/weather?q=padang,id&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Nola. http://api.openweathermap.org/data/2.5/weather?q=nola,cf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 392
    City: Rio Claro. http://api.openweathermap.org/data/2.5/weather?q=rio claro,tt&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 393
    City: Mayumba. http://api.openweathermap.org/data/2.5/weather?q=mayumba,ga&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 394
    City: Tazovskiy. http://api.openweathermap.org/data/2.5/weather?q=tazovskiy,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Hami. http://api.openweathermap.org/data/2.5/weather?q=hami,cn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 395
    City: Genhe. http://api.openweathermap.org/data/2.5/weather?q=genhe,cn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 396
    City: Saskylakh. http://api.openweathermap.org/data/2.5/weather?q=saskylakh,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kamphaeng Phet. http://api.openweathermap.org/data/2.5/weather?q=kamphaeng phet,th&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 397
    City: Salalah. http://api.openweathermap.org/data/2.5/weather?q=salalah,om&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Vaini. http://api.openweathermap.org/data/2.5/weather?q=vaini,to&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Hobyo. http://api.openweathermap.org/data/2.5/weather?q=hobyo,so&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 398
    City: Jalu. http://api.openweathermap.org/data/2.5/weather?q=jalu,ly&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Alagir. http://api.openweathermap.org/data/2.5/weather?q=alagir,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 399
    City: Alihe. http://api.openweathermap.org/data/2.5/weather?q=alihe,cn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 400
    City: Georgetown. http://api.openweathermap.org/data/2.5/weather?q=georgetown,sh&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Tuktoyaktuk. http://api.openweathermap.org/data/2.5/weather?q=tuktoyaktuk,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Broken Hill. http://api.openweathermap.org/data/2.5/weather?q=broken hill,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 401
    City: Ribeira Grande. http://api.openweathermap.org/data/2.5/weather?q=ribeira grande,pt&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Hobart. http://api.openweathermap.org/data/2.5/weather?q=hobart,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Hithadhoo. http://api.openweathermap.org/data/2.5/weather?q=hithadhoo,mv&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Talnakh. http://api.openweathermap.org/data/2.5/weather?q=talnakh,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Thompson. http://api.openweathermap.org/data/2.5/weather?q=thompson,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Sindi. http://api.openweathermap.org/data/2.5/weather?q=sindi,in&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 402
    City: Petropavlovsk-Kamchatskiy. http://api.openweathermap.org/data/2.5/weather?q=petropavlovsk-kamchatskiy,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 403
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ribeira Grande. http://api.openweathermap.org/data/2.5/weather?q=ribeira grande,pt&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Antofagasta. http://api.openweathermap.org/data/2.5/weather?q=antofagasta,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 404
    City: Norman Wells. http://api.openweathermap.org/data/2.5/weather?q=norman wells,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: İskenderun. http://api.openweathermap.org/data/2.5/weather?q=iskenderun,tr&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 405
    City: Bredasdorp. http://api.openweathermap.org/data/2.5/weather?q=bredasdorp,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Arraial do Cabo. http://api.openweathermap.org/data/2.5/weather?q=arraial do cabo,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Puerto Ayora. http://api.openweathermap.org/data/2.5/weather?q=puerto ayora,ec&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Batagay-Alyta. http://api.openweathermap.org/data/2.5/weather?q=batagay-alyta,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 406
    City: Dikson. http://api.openweathermap.org/data/2.5/weather?q=dikson,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Georgetown. http://api.openweathermap.org/data/2.5/weather?q=georgetown,sh&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Poum. http://api.openweathermap.org/data/2.5/weather?q=poum,nc&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kodiak. http://api.openweathermap.org/data/2.5/weather?q=kodiak,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bobbili. http://api.openweathermap.org/data/2.5/weather?q=bobbili,in&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 407
    City: Svetlogorsk. http://api.openweathermap.org/data/2.5/weather?q=svetlogorsk,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 408
    City: Pisco. http://api.openweathermap.org/data/2.5/weather?q=pisco,pe&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 409
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Hasaki. http://api.openweathermap.org/data/2.5/weather?q=hasaki,jp&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Barrow. http://api.openweathermap.org/data/2.5/weather?q=barrow,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Punta Arenas. http://api.openweathermap.org/data/2.5/weather?q=punta arenas,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Carnarvon. http://api.openweathermap.org/data/2.5/weather?q=carnarvon,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Tuktoyaktuk. http://api.openweathermap.org/data/2.5/weather?q=tuktoyaktuk,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Klaksvik. http://api.openweathermap.org/data/2.5/weather?q=klaksvik,fo&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 410
    City: Merauke. http://api.openweathermap.org/data/2.5/weather?q=merauke,id&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 411
    City: Tracy. http://api.openweathermap.org/data/2.5/weather?q=tracy,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 412
    City: Araouane. http://api.openweathermap.org/data/2.5/weather?q=araouane,ml&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 413
    City: Castro. http://api.openweathermap.org/data/2.5/weather?q=castro,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bluff. http://api.openweathermap.org/data/2.5/weather?q=bluff,nz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Nyrany. http://api.openweathermap.org/data/2.5/weather?q=nyrany,cz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 414
    City: Carnarvon. http://api.openweathermap.org/data/2.5/weather?q=carnarvon,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Teya. http://api.openweathermap.org/data/2.5/weather?q=teya,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 415
    City: Albany. http://api.openweathermap.org/data/2.5/weather?q=albany,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Hithadhoo. http://api.openweathermap.org/data/2.5/weather?q=hithadhoo,mv&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Punta Arenas. http://api.openweathermap.org/data/2.5/weather?q=punta arenas,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bandarbeyla. http://api.openweathermap.org/data/2.5/weather?q=bandarbeyla,so&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 416
    City: Progreso. http://api.openweathermap.org/data/2.5/weather?q=progreso,mx&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 417
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kodiak. http://api.openweathermap.org/data/2.5/weather?q=kodiak,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Cayenne. http://api.openweathermap.org/data/2.5/weather?q=cayenne,gf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 418
    City: Vysokogornyy. http://api.openweathermap.org/data/2.5/weather?q=vysokogornyy,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 419
    City: Albany. http://api.openweathermap.org/data/2.5/weather?q=albany,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bethel. http://api.openweathermap.org/data/2.5/weather?q=bethel,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Hobart. http://api.openweathermap.org/data/2.5/weather?q=hobart,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Biak. http://api.openweathermap.org/data/2.5/weather?q=biak,id&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Chokwe. http://api.openweathermap.org/data/2.5/weather?q=chokwe,mz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 420
    City: Nguruka. http://api.openweathermap.org/data/2.5/weather?q=nguruka,tz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 421
    City: Raudeberg. http://api.openweathermap.org/data/2.5/weather?q=raudeberg,no&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Hilo. http://api.openweathermap.org/data/2.5/weather?q=hilo,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Shiyan. http://api.openweathermap.org/data/2.5/weather?q=shiyan,cn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 422
    City: Dikson. http://api.openweathermap.org/data/2.5/weather?q=dikson,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Punta Arenas. http://api.openweathermap.org/data/2.5/weather?q=punta arenas,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kodiak. http://api.openweathermap.org/data/2.5/weather?q=kodiak,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Geraldton. http://api.openweathermap.org/data/2.5/weather?q=geraldton,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Sao Filipe. http://api.openweathermap.org/data/2.5/weather?q=sao filipe,cv&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bredasdorp. http://api.openweathermap.org/data/2.5/weather?q=bredasdorp,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Padang. http://api.openweathermap.org/data/2.5/weather?q=padang,id&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Gaoyou. http://api.openweathermap.org/data/2.5/weather?q=gaoyou,cn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 423
    City: Lagunillas. http://api.openweathermap.org/data/2.5/weather?q=lagunillas,mx&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 424
    City: Albany. http://api.openweathermap.org/data/2.5/weather?q=albany,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Tuktoyaktuk. http://api.openweathermap.org/data/2.5/weather?q=tuktoyaktuk,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kodiak. http://api.openweathermap.org/data/2.5/weather?q=kodiak,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Westport. http://api.openweathermap.org/data/2.5/weather?q=westport,ie&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Hobart. http://api.openweathermap.org/data/2.5/weather?q=hobart,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Lincoln. http://api.openweathermap.org/data/2.5/weather?q=lincoln,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 425
    City: Ketchikan. http://api.openweathermap.org/data/2.5/weather?q=ketchikan,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 426
    City: Praia da Vitoria. http://api.openweathermap.org/data/2.5/weather?q=praia da vitoria,pt&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 427
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bluff. http://api.openweathermap.org/data/2.5/weather?q=bluff,nz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Port Alfred. http://api.openweathermap.org/data/2.5/weather?q=port alfred,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Mahebourg. http://api.openweathermap.org/data/2.5/weather?q=mahebourg,mu&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Jamestown. http://api.openweathermap.org/data/2.5/weather?q=jamestown,sh&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Nelson Bay. http://api.openweathermap.org/data/2.5/weather?q=nelson bay,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 428
    City: Albany. http://api.openweathermap.org/data/2.5/weather?q=albany,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Darhan. http://api.openweathermap.org/data/2.5/weather?q=darhan,mn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 429
    City: Avarua. http://api.openweathermap.org/data/2.5/weather?q=avarua,ck&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Nanakuli. http://api.openweathermap.org/data/2.5/weather?q=nanakuli,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 430
    City: Bluff. http://api.openweathermap.org/data/2.5/weather?q=bluff,nz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Staryy Nadym. http://api.openweathermap.org/data/2.5/weather?q=staryy nadym,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 431
    City: Broome. http://api.openweathermap.org/data/2.5/weather?q=broome,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 432
    City: Punta Arenas. http://api.openweathermap.org/data/2.5/weather?q=punta arenas,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kapaa. http://api.openweathermap.org/data/2.5/weather?q=kapaa,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Mercedes. http://api.openweathermap.org/data/2.5/weather?q=mercedes,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 433
    City: Port Hardy. http://api.openweathermap.org/data/2.5/weather?q=port hardy,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Doka. http://api.openweathermap.org/data/2.5/weather?q=doka,sd&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Eureka. http://api.openweathermap.org/data/2.5/weather?q=eureka,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Aden. http://api.openweathermap.org/data/2.5/weather?q=aden,ye&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Storm Lake. http://api.openweathermap.org/data/2.5/weather?q=storm lake,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 434
    City: Bonnyville. http://api.openweathermap.org/data/2.5/weather?q=bonnyville,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Alta Floresta. http://api.openweathermap.org/data/2.5/weather?q=alta floresta,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 435
    City: La Ronge. http://api.openweathermap.org/data/2.5/weather?q=la ronge,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Yining. http://api.openweathermap.org/data/2.5/weather?q=yining,cn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 436
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Korla. http://api.openweathermap.org/data/2.5/weather?q=korla,cn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 437
    City: Tacuarembo. http://api.openweathermap.org/data/2.5/weather?q=tacuarembo,uy&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 438
    City: Atuona. http://api.openweathermap.org/data/2.5/weather?q=atuona,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bathsheba. http://api.openweathermap.org/data/2.5/weather?q=bathsheba,bb&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 439
    City: Vila Franca do Campo. http://api.openweathermap.org/data/2.5/weather?q=vila franca do campo,pt&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Jamestown. http://api.openweathermap.org/data/2.5/weather?q=jamestown,sh&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Torbay. http://api.openweathermap.org/data/2.5/weather?q=torbay,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Mtwango. http://api.openweathermap.org/data/2.5/weather?q=mtwango,tz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 440
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Cape Town. http://api.openweathermap.org/data/2.5/weather?q=cape town,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Amazar. http://api.openweathermap.org/data/2.5/weather?q=amazar,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 441
    City: Zhezkazgan. http://api.openweathermap.org/data/2.5/weather?q=zhezkazgan,kz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 442
    City: Margate. http://api.openweathermap.org/data/2.5/weather?q=margate,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Atuona. http://api.openweathermap.org/data/2.5/weather?q=atuona,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Lake Havasu City. http://api.openweathermap.org/data/2.5/weather?q=lake havasu city,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 443
    City: New Norfolk. http://api.openweathermap.org/data/2.5/weather?q=new norfolk,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: New Norfolk. http://api.openweathermap.org/data/2.5/weather?q=new norfolk,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Tasiilaq. http://api.openweathermap.org/data/2.5/weather?q=tasiilaq,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Tortoli. http://api.openweathermap.org/data/2.5/weather?q=tortoli,it&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 444
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Cayenne. http://api.openweathermap.org/data/2.5/weather?q=cayenne,gf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Jamestown. http://api.openweathermap.org/data/2.5/weather?q=jamestown,sh&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Jiangkou. http://api.openweathermap.org/data/2.5/weather?q=jiangkou,cn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 445
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Noumea. http://api.openweathermap.org/data/2.5/weather?q=noumea,nc&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 446
    City: Kaitangata. http://api.openweathermap.org/data/2.5/weather?q=kaitangata,nz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Otofuke. http://api.openweathermap.org/data/2.5/weather?q=otofuke,jp&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 447
    City: Cape Town. http://api.openweathermap.org/data/2.5/weather?q=cape town,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Whitianga. http://api.openweathermap.org/data/2.5/weather?q=whitianga,nz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 448
    City: Jamestown. http://api.openweathermap.org/data/2.5/weather?q=jamestown,sh&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bathsheba. http://api.openweathermap.org/data/2.5/weather?q=bathsheba,bb&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Eisenerz. http://api.openweathermap.org/data/2.5/weather?q=eisenerz,at&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 449
    City: Wajima. http://api.openweathermap.org/data/2.5/weather?q=wajima,jp&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 450
    City: Saint George. http://api.openweathermap.org/data/2.5/weather?q=saint george,bm&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 451
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Dikson. http://api.openweathermap.org/data/2.5/weather?q=dikson,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Hermanus. http://api.openweathermap.org/data/2.5/weather?q=hermanus,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Saint-Louis. http://api.openweathermap.org/data/2.5/weather?q=saint-louis,sn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 452
    City: Vaini. http://api.openweathermap.org/data/2.5/weather?q=vaini,to&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kapaa. http://api.openweathermap.org/data/2.5/weather?q=kapaa,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Longyearbyen. http://api.openweathermap.org/data/2.5/weather?q=longyearbyen,sj&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Vestmannaeyjar. http://api.openweathermap.org/data/2.5/weather?q=vestmannaeyjar,is&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 453
    City: Kalmunai. http://api.openweathermap.org/data/2.5/weather?q=kalmunai,lk&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 454
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Yellowknife. http://api.openweathermap.org/data/2.5/weather?q=yellowknife,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: East London. http://api.openweathermap.org/data/2.5/weather?q=east london,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bredasdorp. http://api.openweathermap.org/data/2.5/weather?q=bredasdorp,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Barrow. http://api.openweathermap.org/data/2.5/weather?q=barrow,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Abha. http://api.openweathermap.org/data/2.5/weather?q=abha,sa&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 455
    City: Mayo. http://api.openweathermap.org/data/2.5/weather?q=mayo,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Port Macquarie. http://api.openweathermap.org/data/2.5/weather?q=port macquarie,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 456
    City: Soledad. http://api.openweathermap.org/data/2.5/weather?q=soledad,co&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 457
    City: Puerto del Rosario. http://api.openweathermap.org/data/2.5/weather?q=puerto del rosario,es&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 458
    City: Hamilton. http://api.openweathermap.org/data/2.5/weather?q=hamilton,bm&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Casper. http://api.openweathermap.org/data/2.5/weather?q=casper,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 459
    City: Bambous Virieux. http://api.openweathermap.org/data/2.5/weather?q=bambous virieux,mu&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bredasdorp. http://api.openweathermap.org/data/2.5/weather?q=bredasdorp,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Castro. http://api.openweathermap.org/data/2.5/weather?q=castro,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Pisco. http://api.openweathermap.org/data/2.5/weather?q=pisco,pe&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ribeira Grande. http://api.openweathermap.org/data/2.5/weather?q=ribeira grande,pt&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Sioux Lookout. http://api.openweathermap.org/data/2.5/weather?q=sioux lookout,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 460
    City: The Valley. http://api.openweathermap.org/data/2.5/weather?q=the valley,ai&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 461
    City: San Borja. http://api.openweathermap.org/data/2.5/weather?q=san borja,bo&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 462
    City: Khatanga. http://api.openweathermap.org/data/2.5/weather?q=khatanga,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Gao. http://api.openweathermap.org/data/2.5/weather?q=gao,ml&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 463
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Arraial do Cabo. http://api.openweathermap.org/data/2.5/weather?q=arraial do cabo,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Cape Town. http://api.openweathermap.org/data/2.5/weather?q=cape town,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Touros. http://api.openweathermap.org/data/2.5/weather?q=touros,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 464
    City: Bluff. http://api.openweathermap.org/data/2.5/weather?q=bluff,nz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Cape Town. http://api.openweathermap.org/data/2.5/weather?q=cape town,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kapaa. http://api.openweathermap.org/data/2.5/weather?q=kapaa,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Dingle. http://api.openweathermap.org/data/2.5/weather?q=dingle,ie&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Tiarei. http://api.openweathermap.org/data/2.5/weather?q=tiarei,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 465
    City: Seoul. http://api.openweathermap.org/data/2.5/weather?q=seoul,kr&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 466
    City: Llanes. http://api.openweathermap.org/data/2.5/weather?q=llanes,es&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 467
    City: Lorengau. http://api.openweathermap.org/data/2.5/weather?q=lorengau,pg&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Cherskiy. http://api.openweathermap.org/data/2.5/weather?q=cherskiy,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Takoradi. http://api.openweathermap.org/data/2.5/weather?q=takoradi,gh&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 468
    City: Touros. http://api.openweathermap.org/data/2.5/weather?q=touros,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Cidreira. http://api.openweathermap.org/data/2.5/weather?q=cidreira,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Antofagasta. http://api.openweathermap.org/data/2.5/weather?q=antofagasta,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Cockburn Town. http://api.openweathermap.org/data/2.5/weather?q=cockburn town,tc&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Breves. http://api.openweathermap.org/data/2.5/weather?q=breves,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 469
    City: Hasaki. http://api.openweathermap.org/data/2.5/weather?q=hasaki,jp&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Tuatapere. http://api.openweathermap.org/data/2.5/weather?q=tuatapere,nz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 470
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Nuevo Progreso. http://api.openweathermap.org/data/2.5/weather?q=nuevo progreso,mx&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 471
    City: Butaritari. http://api.openweathermap.org/data/2.5/weather?q=butaritari,ki&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Chuy. http://api.openweathermap.org/data/2.5/weather?q=chuy,uy&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Thompson. http://api.openweathermap.org/data/2.5/weather?q=thompson,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Khatanga. http://api.openweathermap.org/data/2.5/weather?q=khatanga,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Santa Rosalia. http://api.openweathermap.org/data/2.5/weather?q=santa rosalia,mx&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 472
    City: Vila Velha. http://api.openweathermap.org/data/2.5/weather?q=vila velha,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Dikson. http://api.openweathermap.org/data/2.5/weather?q=dikson,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Jumla. http://api.openweathermap.org/data/2.5/weather?q=jumla,np&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 473
    City: Avarua. http://api.openweathermap.org/data/2.5/weather?q=avarua,ck&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Carnarvon. http://api.openweathermap.org/data/2.5/weather?q=carnarvon,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Belorechensk. http://api.openweathermap.org/data/2.5/weather?q=belorechensk,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 474
    City: Nikolskoye. http://api.openweathermap.org/data/2.5/weather?q=nikolskoye,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Mahebourg. http://api.openweathermap.org/data/2.5/weather?q=mahebourg,mu&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kaitangata. http://api.openweathermap.org/data/2.5/weather?q=kaitangata,nz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Punta Arenas. http://api.openweathermap.org/data/2.5/weather?q=punta arenas,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Nikolskoye. http://api.openweathermap.org/data/2.5/weather?q=nikolskoye,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Cape Town. http://api.openweathermap.org/data/2.5/weather?q=cape town,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Atuona. http://api.openweathermap.org/data/2.5/weather?q=atuona,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Manacapuru. http://api.openweathermap.org/data/2.5/weather?q=manacapuru,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 475
    City: Pyaozerskiy. http://api.openweathermap.org/data/2.5/weather?q=pyaozerskiy,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 476
    City: Mahebourg. http://api.openweathermap.org/data/2.5/weather?q=mahebourg,mu&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kaitangata. http://api.openweathermap.org/data/2.5/weather?q=kaitangata,nz&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Lompoc. http://api.openweathermap.org/data/2.5/weather?q=lompoc,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 477
    City: Puerto Rico. http://api.openweathermap.org/data/2.5/weather?q=puerto rico,co&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 478
    City: Atuona. http://api.openweathermap.org/data/2.5/weather?q=atuona,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Saint-Pierre. http://api.openweathermap.org/data/2.5/weather?q=saint-pierre,pm&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Mandalgovi. http://api.openweathermap.org/data/2.5/weather?q=mandalgovi,mn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 479
    City: Avarua. http://api.openweathermap.org/data/2.5/weather?q=avarua,ck&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Progreso. http://api.openweathermap.org/data/2.5/weather?q=progreso,mx&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Puerto Ayora. http://api.openweathermap.org/data/2.5/weather?q=puerto ayora,ec&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bethel. http://api.openweathermap.org/data/2.5/weather?q=bethel,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bhainsdehi. http://api.openweathermap.org/data/2.5/weather?q=bhainsdehi,in&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 480
    City: Kahului. http://api.openweathermap.org/data/2.5/weather?q=kahului,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Katiola. http://api.openweathermap.org/data/2.5/weather?q=katiola,ci&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 481
    City: Faanui. http://api.openweathermap.org/data/2.5/weather?q=faanui,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Luderitz. http://api.openweathermap.org/data/2.5/weather?q=luderitz,na&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 482
    City: Mahebourg. http://api.openweathermap.org/data/2.5/weather?q=mahebourg,mu&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ambilobe. http://api.openweathermap.org/data/2.5/weather?q=ambilobe,mg&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 483
    City: Avarua. http://api.openweathermap.org/data/2.5/weather?q=avarua,ck&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Margate. http://api.openweathermap.org/data/2.5/weather?q=margate,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Siavonga. http://api.openweathermap.org/data/2.5/weather?q=siavonga,zm&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 484
    City: Hilo. http://api.openweathermap.org/data/2.5/weather?q=hilo,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Albany. http://api.openweathermap.org/data/2.5/weather?q=albany,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kruisfontein. http://api.openweathermap.org/data/2.5/weather?q=kruisfontein,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Longyearbyen. http://api.openweathermap.org/data/2.5/weather?q=longyearbyen,sj&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Lompoc. http://api.openweathermap.org/data/2.5/weather?q=lompoc,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Lamlash. http://api.openweathermap.org/data/2.5/weather?q=lamlash,gb&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 485
    City: Sorland. http://api.openweathermap.org/data/2.5/weather?q=sorland,no&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Novonukutskiy. http://api.openweathermap.org/data/2.5/weather?q=novonukutskiy,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 486
    City: Georgetown. http://api.openweathermap.org/data/2.5/weather?q=georgetown,sh&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Puerto Ayora. http://api.openweathermap.org/data/2.5/weather?q=puerto ayora,ec&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kotdwara. http://api.openweathermap.org/data/2.5/weather?q=kotdwara,in&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 487
    City: East London. http://api.openweathermap.org/data/2.5/weather?q=east london,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Luang Prabang. http://api.openweathermap.org/data/2.5/weather?q=luang prabang,la&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 488
    City: Chokurdakh. http://api.openweathermap.org/data/2.5/weather?q=chokurdakh,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Busselton. http://api.openweathermap.org/data/2.5/weather?q=busselton,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Toma. http://api.openweathermap.org/data/2.5/weather?q=toma,bf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 489
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Hobart. http://api.openweathermap.org/data/2.5/weather?q=hobart,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Rikitea. http://api.openweathermap.org/data/2.5/weather?q=rikitea,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Hithadhoo. http://api.openweathermap.org/data/2.5/weather?q=hithadhoo,mv&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ilulissat. http://api.openweathermap.org/data/2.5/weather?q=ilulissat,gl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ust-Maya. http://api.openweathermap.org/data/2.5/weather?q=ust-maya,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 490
    City: Chauk. http://api.openweathermap.org/data/2.5/weather?q=chauk,mm&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 491
    City: Yerofey Pavlovich. http://api.openweathermap.org/data/2.5/weather?q=yerofey pavlovich,ru&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 492
    City: Cabo San Lucas. http://api.openweathermap.org/data/2.5/weather?q=cabo san lucas,mx&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 493
    City: Jamestown. http://api.openweathermap.org/data/2.5/weather?q=jamestown,sh&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Torbay. http://api.openweathermap.org/data/2.5/weather?q=torbay,ca&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kapaa. http://api.openweathermap.org/data/2.5/weather?q=kapaa,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Punta Arenas. http://api.openweathermap.org/data/2.5/weather?q=punta arenas,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bathsheba. http://api.openweathermap.org/data/2.5/weather?q=bathsheba,bb&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Esperance. http://api.openweathermap.org/data/2.5/weather?q=esperance,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Faanui. http://api.openweathermap.org/data/2.5/weather?q=faanui,pf&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bafq. http://api.openweathermap.org/data/2.5/weather?q=bafq,ir&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 494
    City: Bethel. http://api.openweathermap.org/data/2.5/weather?q=bethel,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Bambous Virieux. http://api.openweathermap.org/data/2.5/weather?q=bambous virieux,mu&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Labuhan. http://api.openweathermap.org/data/2.5/weather?q=labuhan,id&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 495
    City: Lebu. http://api.openweathermap.org/data/2.5/weather?q=lebu,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Souillac. http://api.openweathermap.org/data/2.5/weather?q=souillac,mu&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Castro. http://api.openweathermap.org/data/2.5/weather?q=castro,cl&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Moissala. http://api.openweathermap.org/data/2.5/weather?q=moissala,td&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 496
    City: Albany. http://api.openweathermap.org/data/2.5/weather?q=albany,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Hobart. http://api.openweathermap.org/data/2.5/weather?q=hobart,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kawalu. http://api.openweathermap.org/data/2.5/weather?q=kawalu,id&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 497
    City: Vaini. http://api.openweathermap.org/data/2.5/weather?q=vaini,to&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Port Elizabeth. http://api.openweathermap.org/data/2.5/weather?q=port elizabeth,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ushuaia. http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Kapaa. http://api.openweathermap.org/data/2.5/weather?q=kapaa,us&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Port Alfred. http://api.openweathermap.org/data/2.5/weather?q=port alfred,za&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Carnarvon. http://api.openweathermap.org/data/2.5/weather?q=carnarvon,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Flinders. http://api.openweathermap.org/data/2.5/weather?q=flinders,au&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    City: Ban Nahin. http://api.openweathermap.org/data/2.5/weather?q=ban nahin,la&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 498
    City: Campos. http://api.openweathermap.org/data/2.5/weather?q=campos,br&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 499
    City: Sandy Bay. http://api.openweathermap.org/data/2.5/weather?q=sandy bay,hn&units=imperial&APPID=3114f788e259d786b212c15706c3035b
    Status code: 200. DF length is now: 500
    ------------------------------
    Data Retrieval Complete
    ------------------------------






<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>city_name</th>
      <th>country_code</th>
      <th>rand_lat</th>
      <th>rand_lng</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Temp (F)</th>
      <th>Humidity (%)</th>
      <th>Cloudiness (%)</th>
      <th>Wind Speed (mph)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>maceio</td>
      <td>br</td>
      <td>-15.16</td>
      <td>-30.15</td>
      <td>-9.67</td>
      <td>-35.74</td>
      <td>77</td>
      <td>73</td>
      <td>75</td>
      <td>10.29</td>
    </tr>
    <tr>
      <th>1</th>
      <td>bluff</td>
      <td>nz</td>
      <td>-85.82</td>
      <td>177.34</td>
      <td>-46.6</td>
      <td>168.33</td>
      <td>48.75</td>
      <td>100</td>
      <td>92</td>
      <td>22.88</td>
    </tr>
    <tr>
      <th>2</th>
      <td>punta arenas</td>
      <td>cl</td>
      <td>-70.67</td>
      <td>-90.81</td>
      <td>-53.16</td>
      <td>-70.91</td>
      <td>41.95</td>
      <td>95</td>
      <td>20</td>
      <td>16.11</td>
    </tr>
    <tr>
      <th>3</th>
      <td>taoudenni</td>
      <td>ml</td>
      <td>23.51</td>
      <td>-4.55</td>
      <td>22.68</td>
      <td>-3.98</td>
      <td>110.22</td>
      <td>9</td>
      <td>0</td>
      <td>2.98</td>
    </tr>
    <tr>
      <th>4</th>
      <td>bredasdorp</td>
      <td>za</td>
      <td>-82.17</td>
      <td>15.44</td>
      <td>-34.53</td>
      <td>20.04</td>
      <td>57.2</td>
      <td>93</td>
      <td>12</td>
      <td>9.69</td>
    </tr>
  </tbody>
</table>
</div>




```python
#sanity check
len(cities_df)
```




    500




```python
# Save the DataFrame as a csv
cities_df.to_csv("Output/weatherpy_data.csv", encoding="utf-8", index=False)
```


```python
# Build a scatter plot City Latitude vs. Temperature
sns.set_style('ticks')
sns.set(style="darkgrid")
fig, ax = plt.subplots()
p = sns.regplot(x="Latitude", y="Temp (F)", data=cities_df, fit_reg=False).set_title('Temp (F) by Latitude')

# Save the figure
plt.savefig("Output/lat_v_temp.png")

# Show plot
plt.show()
```


![png](output_6_0.png)



```python

# Build a scatter plot City Latitude vs. Humidity# Build
sns.set_style('ticks')
sns.set(style="darkgrid")
fig, ax = plt.subplots()
p = sns.regplot(x="Latitude", y="Humidity (%)", data=cities_df, fit_reg=False).set_title('Humidity (%) by Latitude')

# Save the figure
plt.savefig("Output/lat_v_humidity.png")

# Show plot
plt.show()
```


![png](output_7_0.png)



```python
# Build a scatter plot City Latitude vs. Cloudiness
sns.set_style('ticks')
sns.set(style="darkgrid")
fig, ax = plt.subplots()
p = sns.regplot(x="Latitude", y="Cloudiness (%)", data=cities_df, fit_reg=False).set_title('Cloud Cover (%) by Latitude')

# Save the figure
plt.savefig("Output/lat_v_cloud.png")

# Show plot
plt.show()
```


![png](output_8_0.png)



```python
# Build a scatter plot City Latitude vs. Wind Speed
sns.set_style('ticks')
sns.set(style="darkgrid")
fig, ax = plt.subplots()
p = sns.regplot(x="Latitude", y="Wind Speed (mph)", data=cities_df, fit_reg=False).set_title('Wind Speed (mph) by Latitude')

# Save the figure
plt.savefig("Output/lat_v_wind.png")

# Show plot
plt.show()
```


![png](output_9_0.png)
