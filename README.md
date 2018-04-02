

```python
import pandas as pd
import numpy as np
import urllib
import seaborn as sns
import openweathermapy.core as owm
import matplotlib.pyplot as plt
import json
import requests
import datetime

#config
from weather_config import api_key
```


```python
# Open World Cities CSV
world_cities = pd.read_csv('worldcities.csv')
```


```python
# Create Random Subset of World Cities
random_sample_cities = world_cities.sample(n = 700)
reset_index_random_cities = random_sample_cities.reset_index()
random_cities = reset_index_random_cities[['Country', 'City', 'Latitude', 'Longitude']]
random_cities.head()
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
      <th>Country</th>
      <th>City</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>mx</td>
      <td>tlaxco</td>
      <td>20.433333</td>
      <td>-98.016667</td>
    </tr>
    <tr>
      <th>1</th>
      <td>de</td>
      <td>bergen</td>
      <td>52.816667</td>
      <td>9.966667</td>
    </tr>
    <tr>
      <th>2</th>
      <td>us</td>
      <td>boston</td>
      <td>42.358333</td>
      <td>-71.060278</td>
    </tr>
    <tr>
      <th>3</th>
      <td>no</td>
      <td>hundorp</td>
      <td>61.550000</td>
      <td>9.900000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>cz</td>
      <td>hustopece</td>
      <td>48.941028</td>
      <td>16.733530</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Combining City and Country so API returns correct city
random_cities.head()
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
      <th>Country</th>
      <th>City</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>mx</td>
      <td>tlaxco</td>
      <td>20.433333</td>
      <td>-98.016667</td>
    </tr>
    <tr>
      <th>1</th>
      <td>de</td>
      <td>bergen</td>
      <td>52.816667</td>
      <td>9.966667</td>
    </tr>
    <tr>
      <th>2</th>
      <td>us</td>
      <td>boston</td>
      <td>42.358333</td>
      <td>-71.060278</td>
    </tr>
    <tr>
      <th>3</th>
      <td>no</td>
      <td>hundorp</td>
      <td>61.550000</td>
      <td>9.900000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>cz</td>
      <td>hustopece</td>
      <td>48.941028</td>
      <td>16.733530</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Create settings
settings = {"units": "imperial", "appid": api_key}
```


```python
# Get current weather
cities = random_cities['City']
random_world_cities = []
world_countries = []
clouds = []
date = []
humidity = []
latitudes = []
longitudes = []
max_temp = []
wind_speeds = []
cities_no_info = []
no_name_cities = []

for city in cities:
    if len(random_world_cities) < 500:
        try:
            url = "http://api.openweathermap.org/data/2.5/weather?"
            query_url = url + "appid=" + api_key + "&q=" + city
            weather_response = requests.get(query_url)
            weather_json = weather_response.json()
            current_weather = owm.get_current(city, **settings)
            city_name = weather_json['name']
            city_country = weather_json['sys']['country']
            cloud = weather_json['clouds']['all']
            weather_date = weather_json['dt']
            city_humidity = weather_json['main']['humidity']
            city_lat = weather_json['coord']['lat']
            city_lon = weather_json['coord']['lon']
            city_temp = weather_json['main']['temp_max']
            city_wind = weather_json['wind']['speed']
            if city not in cities:
                try:
                    random_world_cities.append(city_name)
                    world_countries.append(city_country)
                    clouds.append(cloud)
                    date.append(weather_date)
                    humidity.append(city_humidity)
                    latitudes.append(city_lat)
                    longitudes.append(city_lon)
                    max_temp.append(city_temp)
                    wind_speeds.append(city_wind)
                    print("Processing Record " + str(len(random_world_cities)) + " : " + city)
                    print(query_url)
                except KeyError:
                    no_name_cities.append(city)
        except urllib.error.HTTPError as e:
            if e:
                cities_no_info.append(str(city) + ":" + "No info")
```

    Processing Record 1 : tlaxco
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tlaxco
    Processing Record 2 : boston
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=boston
    Processing Record 3 : hundorp
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=hundorp
    Processing Record 4 : hustopece
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=hustopece
    Processing Record 5 : movilita
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=movilita
    Processing Record 6 : sint-genesius-rode
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sint-genesius-rode
    Processing Record 7 : trebic
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=trebic
    Processing Record 8 : la marque
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=la marque
    Processing Record 9 : poltavka
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=poltavka
    Processing Record 10 : powder springs
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=powder springs
    Processing Record 11 : orleans
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=orleans
    Processing Record 12 : lomianki
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=lomianki
    Processing Record 13 : leu
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=leu
    Processing Record 14 : szentkiraly
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=szentkiraly
    Processing Record 15 : mersing
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mersing
    Processing Record 16 : krasnozerskoye
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=krasnozerskoye
    Processing Record 17 : krasnovka
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=krasnovka
    Processing Record 18 : dampol
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=dampol
    Processing Record 19 : balboa
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=balboa
    Processing Record 20 : gojo
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=gojo
    Processing Record 21 : pniv
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pniv
    Processing Record 22 : beachwood
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=beachwood
    Processing Record 23 : zlatograd
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=zlatograd
    Processing Record 24 : naranjito
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=naranjito
    Processing Record 25 : schiller park
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=schiller park
    Processing Record 26 : zabid
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=zabid
    Processing Record 27 : salym
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=salym
    Processing Record 28 : kameyama
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kameyama
    Processing Record 29 : am timan
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=am timan
    Processing Record 30 : seon
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=seon
    Processing Record 31 : country club
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=country club
    Processing Record 32 : kelso
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kelso
    Processing Record 33 : nazarovo
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=nazarovo
    Processing Record 34 : bourne
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bourne
    Processing Record 35 : minalabac
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=minalabac
    Processing Record 36 : weeze
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=weeze
    Processing Record 37 : colonesti
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=colonesti
    Processing Record 38 : mayrtup
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mayrtup
    Processing Record 39 : el giral
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=el giral
    Processing Record 40 : belaya kholunitsa
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=belaya kholunitsa
    Processing Record 41 : casambalangan
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=casambalangan
    Processing Record 42 : jirikov
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=jirikov
    Processing Record 43 : little baguio
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=little baguio
    Processing Record 44 : new bohol
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=new bohol
    Processing Record 45 : sobreda
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sobreda
    Processing Record 46 : rosarito
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=rosarito
    Processing Record 47 : evreux
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=evreux
    Processing Record 48 : shamkhal-termen
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=shamkhal-termen
    Processing Record 49 : concepcion de buenos aires
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=concepcion de buenos aires
    Processing Record 50 : vorselaar
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=vorselaar
    Processing Record 51 : elk plain
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=elk plain
    Processing Record 52 : kokubu
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kokubu
    Processing Record 53 : zitlaltepec
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=zitlaltepec
    Processing Record 54 : onojo
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=onojo
    Processing Record 55 : payao
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=payao
    Processing Record 56 : ormenis
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ormenis
    Processing Record 57 : schouweiler
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=schouweiler
    Processing Record 58 : borba
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=borba
    Processing Record 59 : pervomaysk
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pervomaysk
    Processing Record 60 : kodarma
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kodarma
    Processing Record 61 : huautla
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=huautla
    Processing Record 62 : liciada
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=liciada
    Processing Record 63 : kirchheim
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kirchheim
    Processing Record 64 : nicolas ruiz
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=nicolas ruiz
    Processing Record 65 : salekhard
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=salekhard
    Processing Record 66 : sallaumines
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sallaumines
    Processing Record 67 : narang
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=narang
    Processing Record 68 : karasburg
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=karasburg
    Processing Record 69 : santa flavia
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=santa flavia
    Processing Record 70 : vinzili
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=vinzili
    Processing Record 71 : arnia
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=arnia
    Processing Record 72 : crystal lake
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=crystal lake
    Processing Record 73 : volzhskiy
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=volzhskiy
    Processing Record 74 : kitim
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kitim
    Processing Record 75 : pueblo nuevo
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pueblo nuevo
    Processing Record 76 : pali
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pali
    Processing Record 77 : ennepetal
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ennepetal
    Processing Record 78 : warner robins
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=warner robins
    Processing Record 79 : dragomiresti-vale
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=dragomiresti-vale
    Processing Record 80 : andi
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=andi
    Processing Record 81 : shalu
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=shalu
    Processing Record 82 : andujar
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=andujar
    Processing Record 83 : baykit
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=baykit
    Processing Record 84 : moyamba
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=moyamba
    Processing Record 85 : capreni
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=capreni
    Processing Record 86 : kuybysheve
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kuybysheve
    Processing Record 87 : latnaya
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=latnaya
    Processing Record 88 : eeklo
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=eeklo
    Processing Record 89 : quiniput
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=quiniput
    Processing Record 90 : gualmatan
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=gualmatan
    Processing Record 91 : altinopolis
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=altinopolis
    Processing Record 92 : hanyang
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=hanyang
    Processing Record 93 : carmen de viboral
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=carmen de viboral
    Processing Record 94 : goryachevodskiy
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=goryachevodskiy
    Processing Record 95 : arkalgud
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=arkalgud
    Processing Record 96 : san basilio
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=san basilio
    Processing Record 97 : pushkinskiye gory
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pushkinskiye gory
    Processing Record 98 : megali khora
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=megali khora
    Processing Record 99 : kilifi
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kilifi
    Processing Record 100 : vise
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=vise
    Processing Record 101 : kalas
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kalas
    Processing Record 102 : palma
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=palma
    Processing Record 103 : novosil
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=novosil
    Processing Record 104 : san pablo
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=san pablo
    Processing Record 105 : lille
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=lille
    Processing Record 106 : klosterneuburg
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=klosterneuburg
    Processing Record 107 : lena
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=lena
    Processing Record 108 : scortaru nou
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=scortaru nou
    Processing Record 109 : citrus heights
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=citrus heights
    Processing Record 110 : catende
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=catende
    Processing Record 111 : maharajganj
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=maharajganj
    Processing Record 112 : borisovka
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=borisovka
    Processing Record 113 : bergem
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bergem
    Processing Record 114 : san gabriel
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=san gabriel
    Processing Record 115 : sao jose
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sao jose
    Processing Record 116 : china
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=china
    Processing Record 117 : mihaesti
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mihaesti
    Processing Record 118 : navoi
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=navoi
    Processing Record 119 : mongui
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mongui
    Processing Record 120 : maguinao
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=maguinao
    Processing Record 121 : horizontina
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=horizontina
    Processing Record 122 : hollywood
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=hollywood
    Processing Record 123 : ljubuski
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ljubuski
    Processing Record 124 : reshetnikovo
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=reshetnikovo
    Processing Record 125 : neiva
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=neiva
    Processing Record 126 : san giljan
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=san giljan
    Processing Record 127 : neuville-en-ferrain
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=neuville-en-ferrain
    Processing Record 128 : villeurbanne
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=villeurbanne
    Processing Record 129 : litein
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=litein
    Processing Record 130 : ripiceni
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ripiceni
    Processing Record 131 : cumana
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=cumana
    Processing Record 132 : talibon
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=talibon
    Processing Record 133 : le passage
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=le passage
    Processing Record 134 : nor armavir
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=nor armavir
    Processing Record 135 : albany
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=albany
    Processing Record 136 : diksmuide
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=diksmuide
    Processing Record 137 : bacsay
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bacsay
    Processing Record 138 : uruapan
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=uruapan
    Processing Record 139 : doschatoye
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=doschatoye
    Processing Record 140 : rancho grande
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=rancho grande
    Processing Record 141 : perth
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=perth
    Processing Record 142 : bohmte
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bohmte
    Processing Record 143 : krasna
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=krasna
    Processing Record 144 : paulesti
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=paulesti
    Processing Record 145 : rajapur
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=rajapur
    Processing Record 146 : borzna
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=borzna
    Processing Record 147 : nova odesa
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=nova odesa
    Processing Record 148 : tangi
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tangi
    Processing Record 149 : simbahan
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=simbahan
    Processing Record 150 : vichy
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=vichy
    Processing Record 151 : frolovo
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=frolovo
    Processing Record 152 : pansoy
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pansoy
    Processing Record 153 : vienne
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=vienne
    Processing Record 154 : vasilati
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=vasilati
    Processing Record 155 : nakhabino
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=nakhabino
    Processing Record 156 : crystal
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=crystal
    Processing Record 157 : sovetsk
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sovetsk
    Processing Record 158 : becov
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=becov
    Processing Record 159 : geldermalsen
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=geldermalsen
    Processing Record 160 : shalushka
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=shalushka
    Processing Record 161 : jablonec nad jizerou
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=jablonec nad jizerou
    Processing Record 162 : wolfurt
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=wolfurt
    Processing Record 163 : dotnuva
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=dotnuva
    Processing Record 164 : tirnavos
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tirnavos
    Processing Record 165 : gradinari
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=gradinari
    Processing Record 166 : chai badan
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=chai badan
    Processing Record 167 : rumburk
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=rumburk
    Processing Record 168 : latacunga
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=latacunga
    Processing Record 169 : floresta
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=floresta
    Processing Record 170 : mayorga
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mayorga
    Processing Record 171 : chavakkad
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=chavakkad
    Processing Record 172 : namasuba
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=namasuba
    Processing Record 173 : yonibana
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=yonibana
    Processing Record 174 : cavan
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=cavan
    Processing Record 175 : monjas
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=monjas
    Processing Record 176 : agpangi
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=agpangi
    Processing Record 177 : apsheronsk
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=apsheronsk
    Processing Record 178 : korschenbroich
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=korschenbroich
    Processing Record 179 : hanting
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=hanting
    Processing Record 180 : rio amarillo
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=rio amarillo
    Processing Record 181 : abrau-dyurso
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=abrau-dyurso
    Processing Record 182 : point fortin
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=point fortin
    Processing Record 183 : calatagan
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=calatagan
    Processing Record 184 : mandal
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mandal
    Processing Record 185 : salisbury
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=salisbury
    Processing Record 186 : saginaw
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=saginaw
    Processing Record 187 : klishkivtsi
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=klishkivtsi
    Processing Record 188 : norashen
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=norashen
    Processing Record 189 : islamabad
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=islamabad
    Processing Record 190 : ludhiana
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ludhiana
    Processing Record 191 : roxas
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=roxas
    Processing Record 192 : schrobenhausen
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=schrobenhausen
    Processing Record 193 : troina
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=troina
    Processing Record 194 : pontianak
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pontianak
    Processing Record 195 : guadalupe
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=guadalupe
    Processing Record 196 : ejido hermosillo
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ejido hermosillo
    Processing Record 197 : bruchkobel
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bruchkobel
    Processing Record 198 : san francisco
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=san francisco
    Processing Record 199 : herndon
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=herndon
    Processing Record 200 : montataire
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=montataire
    Processing Record 201 : capari
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=capari
    Processing Record 202 : bijnor
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bijnor
    Processing Record 203 : vacszentlaszlo
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=vacszentlaszlo
    Processing Record 204 : guindapunan
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=guindapunan
    Processing Record 205 : queen victoria
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=queen victoria
    Processing Record 206 : vidnoye
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=vidnoye
    Processing Record 207 : mohan
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mohan
    Processing Record 208 : amagasaki
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=amagasaki
    Processing Record 209 : manavadar
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=manavadar
    Processing Record 210 : moga
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=moga
    Processing Record 211 : jamu mare
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=jamu mare
    Processing Record 212 : guebwiller
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=guebwiller
    Processing Record 213 : bogacs
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bogacs
    Processing Record 214 : lifford
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=lifford
    Processing Record 215 : orchard homes
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=orchard homes
    Processing Record 216 : sudzha
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sudzha
    Processing Record 217 : nau
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=nau
    Processing Record 218 : gleisdorf
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=gleisdorf
    Processing Record 219 : pembroke
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pembroke
    Processing Record 220 : oroville
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=oroville
    Processing Record 221 : huaicheng
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=huaicheng
    Processing Record 222 : colombia
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=colombia
    Processing Record 223 : saanen
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=saanen
    Processing Record 224 : kambileyevskoye
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kambileyevskoye
    Processing Record 225 : ebolowa
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ebolowa
    Processing Record 226 : bodden town
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bodden town
    Processing Record 227 : yeadon
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=yeadon
    Processing Record 228 : artyom
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=artyom
    Processing Record 229 : balogo
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=balogo
    Processing Record 230 : athis-mons
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=athis-mons
    Processing Record 231 : beckum
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=beckum
    Processing Record 232 : kastrakion
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kastrakion
    Processing Record 233 : ubata
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ubata
    Processing Record 234 : ilaskhan-yurt
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ilaskhan-yurt
    Processing Record 235 : abramut
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=abramut
    Processing Record 236 : hasanpur
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=hasanpur
    Processing Record 237 : vicam
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=vicam
    Processing Record 238 : quatro barras
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=quatro barras
    Processing Record 239 : aspen hill
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=aspen hill
    Processing Record 240 : saint-pol-sur-mer
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=saint-pol-sur-mer
    Processing Record 241 : utran
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=utran
    Processing Record 242 : mill creek
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mill creek
    Processing Record 243 : mount pleasant
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mount pleasant
    Processing Record 244 : cucuteni
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=cucuteni
    Processing Record 245 : klyuchevskiy
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=klyuchevskiy
    Processing Record 246 : emod
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=emod
    Processing Record 247 : abiramam
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=abiramam
    Processing Record 248 : yaguaron
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=yaguaron
    Processing Record 249 : nasunogan
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=nasunogan
    Processing Record 250 : yanaul
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=yanaul
    Processing Record 251 : madrid
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=madrid
    Processing Record 252 : santhia
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=santhia
    Processing Record 253 : new milford
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=new milford
    Processing Record 254 : kasaragod
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kasaragod
    Processing Record 255 : mingguang
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mingguang
    Processing Record 256 : pearland
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pearland
    Processing Record 257 : phalauda
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=phalauda
    Processing Record 258 : fucecchio
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=fucecchio
    Processing Record 259 : san luis potosi
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=san luis potosi
    Processing Record 260 : bilwang
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bilwang
    Processing Record 261 : helsingborg
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=helsingborg
    Processing Record 262 : maumere
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=maumere
    Processing Record 263 : carmaux
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=carmaux
    Processing Record 264 : la solana
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=la solana
    Processing Record 265 : san vicente
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=san vicente
    Processing Record 266 : chodziez
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=chodziez
    Processing Record 267 : dortmund
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=dortmund
    Processing Record 268 : turzovka
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=turzovka
    Processing Record 269 : ballyshannon
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ballyshannon
    Processing Record 270 : eberndorf
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=eberndorf
    Processing Record 271 : mesagne
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mesagne
    Processing Record 272 : loans
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=loans
    Processing Record 273 : malang
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=malang
    Processing Record 274 : areosa
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=areosa
    Processing Record 275 : saiha
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=saiha
    Processing Record 276 : meridian
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=meridian
    Processing Record 277 : barra bonita
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=barra bonita
    Processing Record 278 : zlin
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=zlin
    Processing Record 279 : pandaan
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pandaan
    Processing Record 280 : claver
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=claver
    Processing Record 281 : sibaguan
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sibaguan
    Processing Record 282 : nagbalaye
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=nagbalaye
    Processing Record 283 : jesenice
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=jesenice
    Processing Record 284 : mombasa
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mombasa
    Processing Record 285 : barhiya
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=barhiya
    Processing Record 286 : cerknica
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=cerknica
    Processing Record 287 : mani
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mani
    Processing Record 288 : challans
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=challans
    Processing Record 289 : oktyabrskoye
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=oktyabrskoye
    Processing Record 290 : lucca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=lucca
    Processing Record 291 : palmares
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=palmares
    Processing Record 292 : tassin-la-demi-lune
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tassin-la-demi-lune
    Processing Record 293 : columbia
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=columbia
    Processing Record 294 : aiken
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=aiken
    Processing Record 295 : kantang
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kantang
    Processing Record 296 : rizia
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=rizia
    Processing Record 297 : lianga
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=lianga
    Processing Record 298 : farum
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=farum
    Processing Record 299 : nikisiani
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=nikisiani
    Processing Record 300 : armenia
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=armenia
    Processing Record 301 : worth
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=worth
    Processing Record 302 : botolan
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=botolan
    Processing Record 303 : horadiz
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=horadiz
    Processing Record 304 : vaini
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=vaini
    Processing Record 305 : fuling
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=fuling
    Processing Record 306 : eilenburg
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=eilenburg
    Processing Record 307 : sam phran
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sam phran
    Processing Record 308 : portel
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=portel
    Processing Record 309 : alwar
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=alwar
    Processing Record 310 : fairfield
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=fairfield
    Processing Record 311 : lisa
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=lisa
    Processing Record 312 : plymouth
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=plymouth
    Processing Record 313 : ranfurly
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ranfurly
    Processing Record 314 : tochio
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tochio
    Processing Record 315 : radnevo
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=radnevo
    Processing Record 316 : traiguen
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=traiguen
    Processing Record 317 : mulhouse
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mulhouse
    Processing Record 318 : ozu
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ozu
    Processing Record 319 : dzepciste
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=dzepciste
    Processing Record 320 : tamuin
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tamuin
    Processing Record 321 : shido
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=shido
    Processing Record 322 : san bonifacio
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=san bonifacio
    Processing Record 323 : sarandi
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sarandi
    Processing Record 324 : neral
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=neral
    Processing Record 325 : topoloveni
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=topoloveni
    Processing Record 326 : caravaggio
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=caravaggio
    Processing Record 327 : baie-mahault
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=baie-mahault
    Processing Record 328 : le pre-saint-gervais
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=le pre-saint-gervais
    Processing Record 329 : schopfheim
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=schopfheim
    Processing Record 330 : merk
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=merk
    Processing Record 331 : blajel
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=blajel
    Processing Record 332 : sonkovo
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sonkovo
    Processing Record 333 : pontedera
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pontedera
    Processing Record 334 : pidsandawan
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pidsandawan
    Processing Record 335 : moita
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=moita
    Processing Record 336 : gulmarg
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=gulmarg
    Processing Record 337 : chilia veche
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=chilia veche
    Processing Record 338 : taminango
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=taminango
    Processing Record 339 : crasna
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=crasna
    Processing Record 340 : bazpur
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bazpur
    Processing Record 341 : sokna
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sokna
    Processing Record 342 : los amates
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=los amates
    Processing Record 343 : sehithwa
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sehithwa
    Processing Record 344 : lasam
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=lasam
    Processing Record 345 : stara synyava
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=stara synyava
    Processing Record 346 : gohand
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=gohand
    Processing Record 347 : stiubieni
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=stiubieni
    Processing Record 348 : svirstroy
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=svirstroy
    Processing Record 349 : rumenka
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=rumenka
    Processing Record 350 : gebre guracha
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=gebre guracha
    Processing Record 351 : negara
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=negara
    Processing Record 352 : airdrie
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=airdrie
    Processing Record 353 : bocholt
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bocholt
    Processing Record 354 : jubbal
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=jubbal
    Processing Record 355 : yaqui
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=yaqui
    Processing Record 356 : gempol
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=gempol
    Processing Record 357 : sarwar
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sarwar
    Processing Record 358 : cuxhaven
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=cuxhaven
    Processing Record 359 : remetea
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=remetea
    Processing Record 360 : bethausen
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bethausen
    Processing Record 361 : natchitoches
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=natchitoches
    Processing Record 362 : segou
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=segou
    Processing Record 363 : nytva
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=nytva
    Processing Record 364 : pesqueria
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pesqueria
    Processing Record 365 : matraderecske
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=matraderecske
    Processing Record 366 : seesen
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=seesen
    Processing Record 367 : corrente
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=corrente
    Processing Record 368 : wigan
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=wigan
    Processing Record 369 : lala
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=lala
    Processing Record 370 : shaami-yurt
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=shaami-yurt
    Processing Record 371 : amtala
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=amtala
    Processing Record 372 : okegawa
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=okegawa
    Processing Record 373 : ixtahuacan
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ixtahuacan
    Processing Record 374 : haj ve slezsku
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=haj ve slezsku
    Processing Record 375 : curuguaty
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=curuguaty
    Processing Record 376 : vorgashor
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=vorgashor
    Processing Record 377 : lo arado
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=lo arado
    Processing Record 378 : vereshchagino
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=vereshchagino
    Processing Record 379 : ciocanesti
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ciocanesti
    Processing Record 380 : labrador
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=labrador
    Processing Record 381 : pinawa
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pinawa
    Processing Record 382 : krosno odrzanskie
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=krosno odrzanskie
    Processing Record 383 : pangal
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pangal
    Processing Record 384 : mus
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mus
    Processing Record 385 : pocinhos
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pocinhos
    Processing Record 386 : taperoa
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=taperoa
    Processing Record 387 : puma
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=puma
    Processing Record 388 : padilla
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=padilla
    Processing Record 389 : pitogo
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pitogo
    Processing Record 390 : saint-andre
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=saint-andre
    Processing Record 391 : yershichi
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=yershichi
    Processing Record 392 : sao miguel do araguaia
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sao miguel do araguaia
    Processing Record 393 : entrerrios
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=entrerrios
    Processing Record 394 : miamisburg
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=miamisburg
    Processing Record 395 : pinillos
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pinillos
    Processing Record 396 : rimbey
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=rimbey
    Processing Record 397 : bar bigha
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bar bigha
    Processing Record 398 : wisch
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=wisch
    Processing Record 399 : amucao
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=amucao
    Processing Record 400 : north royalton
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=north royalton
    Processing Record 401 : ustek
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ustek
    Processing Record 402 : molaoi
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=molaoi
    Processing Record 403 : bhigvan
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bhigvan
    Processing Record 404 : tootsi
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tootsi
    Processing Record 405 : stefan cel mare
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=stefan cel mare
    Processing Record 406 : la tijera
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=la tijera
    Processing Record 407 : gainsborough
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=gainsborough
    Processing Record 408 : scotts valley
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=scotts valley
    Processing Record 409 : bucsa
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bucsa
    Processing Record 410 : san isidro
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=san isidro
    Processing Record 411 : fond du lac
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=fond du lac
    Processing Record 412 : humpolec
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=humpolec
    Processing Record 413 : khombole
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=khombole
    Processing Record 414 : fleron
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=fleron
    Processing Record 415 : cockenzie
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=cockenzie
    Processing Record 416 : hlatikulu
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=hlatikulu
    Processing Record 417 : pardesiyya
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pardesiyya
    Processing Record 418 : para de minas
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=para de minas
    Processing Record 419 : dispur
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=dispur
    Processing Record 420 : dobroteasa
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=dobroteasa
    Processing Record 421 : teocuitatlan de corona
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=teocuitatlan de corona
    Processing Record 422 : leduc
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=leduc
    Processing Record 423 : castro
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=castro
    Processing Record 424 : asmar
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=asmar
    Processing Record 425 : tuquerres
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tuquerres
    Processing Record 426 : west warwick
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=west warwick
    Processing Record 427 : oxchuc
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=oxchuc
    Processing Record 428 : yaval
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=yaval
    Processing Record 429 : pappinisseri
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pappinisseri
    Processing Record 430 : uvaly
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=uvaly
    Processing Record 431 : praskoveya
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=praskoveya
    Processing Record 432 : bertrange
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bertrange
    Processing Record 433 : ramanathapuram
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ramanathapuram
    Processing Record 434 : pemagatsel
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pemagatsel
    Processing Record 435 : guider
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=guider
    Processing Record 436 : oak harbor
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=oak harbor
    Processing Record 437 : remetea mare
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=remetea mare
    Processing Record 438 : arboleda
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=arboleda
    Processing Record 439 : kardonikskaya
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kardonikskaya
    Processing Record 440 : kamina
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kamina
    Processing Record 441 : detchino
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=detchino
    Processing Record 442 : jatibarang
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=jatibarang
    Processing Record 443 : sanmartin
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sanmartin
    Processing Record 444 : terracina
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=terracina
    Processing Record 445 : safidon
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=safidon
    Processing Record 446 : monching
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=monching
    Processing Record 447 : vsetaty
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=vsetaty
    Processing Record 448 : san fernando
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=san fernando
    Processing Record 449 : hagondange
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=hagondange
    Processing Record 450 : aleksandrovskaya
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=aleksandrovskaya
    Processing Record 451 : erbach
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=erbach
    Processing Record 452 : zavoi
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=zavoi
    Processing Record 453 : chapulco
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=chapulco
    Processing Record 454 : kamenka
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kamenka
    Processing Record 455 : vaghodia
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=vaghodia
    Processing Record 456 : tabuating
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tabuating
    Processing Record 457 : steyr
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=steyr
    Processing Record 458 : mo i rana
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mo i rana
    Processing Record 459 : dabat
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=dabat
    Processing Record 460 : algarrobo
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=algarrobo
    Processing Record 461 : garcia
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=garcia
    Processing Record 462 : barceloneta
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=barceloneta
    Processing Record 463 : baramula
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=baramula
    Processing Record 464 : egypt lake-leto
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=egypt lake-leto
    Processing Record 465 : lungesti
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=lungesti
    Processing Record 466 : san cayetano
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=san cayetano
    Processing Record 467 : taseyevo
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=taseyevo
    Processing Record 468 : novi sanzhary
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=novi sanzhary
    Processing Record 469 : bad langensalza
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bad langensalza
    Processing Record 470 : hirapur
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=hirapur
    Processing Record 471 : valdobbiadene
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=valdobbiadene
    Processing Record 472 : svetogorsk
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=svetogorsk
    Processing Record 473 : amot
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=amot
    Processing Record 474 : pedroucos
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pedroucos
    Processing Record 475 : faragau
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=faragau
    Processing Record 476 : rocca priora
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=rocca priora
    Processing Record 477 : kirovsk
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kirovsk
    Processing Record 478 : dengzhou
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=dengzhou
    Processing Record 479 : bambui
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bambui
    Processing Record 480 : saryozek
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=saryozek
    Processing Record 481 : gereykhanovskoye
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=gereykhanovskoye
    Processing Record 482 : beibei
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=beibei
    Processing Record 483 : acala
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=acala
    Processing Record 484 : tokod
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tokod
    Processing Record 485 : cakovec
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=cakovec
    Processing Record 486 : faribault
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=faribault
    Processing Record 487 : shelburne
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=shelburne
    Processing Record 488 : meghrashen
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=meghrashen
    Processing Record 489 : meteti
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=meteti
    Processing Record 490 : lazuri
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=lazuri
    Processing Record 491 : guangyuan
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=guangyuan
    Processing Record 492 : riobamba
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=riobamba
    Processing Record 493 : svolvaer
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=svolvaer
    Processing Record 494 : rondon
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=rondon
    Processing Record 495 : tuku
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tuku
    Processing Record 496 : gladstone
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=gladstone
    Processing Record 497 : arsenal
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=arsenal
    Processing Record 498 : angers
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=angers
    Processing Record 499 : new london
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=new london
    Processing Record 500 : canals
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=canals



```python
# Count to see how many entries I have, the goal is 500
len(random_world_cities)
```




    500




```python
random_world_cities_df = pd.DataFrame(random_world_cities)
renamed_random_world_cities_df = random_world_cities_df.rename(columns={0 : "City"})

world_countries_df = pd.DataFrame(world_countries)
renamed_world_countries_df = world_countries_df.rename(columns={0 : "Country"})

clouds_df = pd.DataFrame(clouds)
renamed_clouds_df = clouds_df.rename(columns={0 : "Cloudiness"})

date_df = pd.DataFrame(date)
renamed_date_df = date_df.rename(columns={0 : "Date"})

humidity_df = pd.DataFrame(humidity)
renamed_humidity_df = humidity_df.rename(columns={0 : "Humidity"})

latitudes_df = pd.DataFrame(latitudes)
renamed_latitudes_df = latitudes_df.rename(columns={0 : "Latitude"})

longitudes_df = pd.DataFrame(longitudes)
renamed_longitudes_df = longitudes_df.rename(columns={0 : "Longitude"})

max_temp_df = pd.DataFrame(max_temp)
renamed_max_temp_df = max_temp_df.rename(columns={0 : "Max Temp"})

wind_speeds_df = pd.DataFrame(wind_speeds)
renamed_wind_speeds_df = wind_speeds_df.rename(columns={0 : "Wind Speeds"})
```


```python
# Join all DataFrames
first_join = renamed_random_world_cities_df.join(renamed_world_countries_df)
second_join = first_join.join(renamed_clouds_df)
third_join = second_join.join(renamed_date_df)
fourth_join = third_join.join(renamed_humidity_df)
fifth_join = fourth_join.join(renamed_latitudes_df)
sixth_join = fifth_join.join(renamed_longitudes_df)
seveth_join = sixth_join.join(renamed_max_temp_df)
city_api_df = seveth_join.join(renamed_wind_speeds_df)
city_api_df.head()
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
      <th>City</th>
      <th>Country</th>
      <th>Cloudiness</th>
      <th>Date</th>
      <th>Humidity</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Max Temp</th>
      <th>Wind Speeds</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Tlaxco</td>
      <td>MX</td>
      <td>75</td>
      <td>1522691100</td>
      <td>27</td>
      <td>19.61</td>
      <td>-98.12</td>
      <td>296.150</td>
      <td>5.10</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Boston</td>
      <td>US</td>
      <td>90</td>
      <td>1522692900</td>
      <td>80</td>
      <td>42.36</td>
      <td>-71.06</td>
      <td>277.150</td>
      <td>2.10</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Hundorp</td>
      <td>NO</td>
      <td>8</td>
      <td>1522693909</td>
      <td>78</td>
      <td>61.55</td>
      <td>9.94</td>
      <td>265.058</td>
      <td>1.12</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Hustopece</td>
      <td>CZ</td>
      <td>0</td>
      <td>1522692000</td>
      <td>69</td>
      <td>48.94</td>
      <td>16.74</td>
      <td>280.150</td>
      <td>3.10</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Movilita</td>
      <td>RO</td>
      <td>0</td>
      <td>1522692000</td>
      <td>46</td>
      <td>44.65</td>
      <td>26.48</td>
      <td>284.150</td>
      <td>2.10</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Save the pandas dataframe on a CSV, so I do not have to go through all the steps above again
city_api_df.to_csv('world_cities_information.csv')
```


```python
# Upload the csv
world_cities_csv = pd.read_csv('world_cities_information.csv')
world_cities_info_df = world_cities_csv[['City', 'Country', 'Cloudiness', 'Date', 'Humidity', 'Latitude', 'Longitude', 'Max Temp', 'Wind Speeds']]
world_cities_info_df.head()
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
      <th>City</th>
      <th>Country</th>
      <th>Cloudiness</th>
      <th>Date</th>
      <th>Humidity</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Max Temp</th>
      <th>Wind Speeds</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Tlaxco</td>
      <td>MX</td>
      <td>75</td>
      <td>1522691100</td>
      <td>27</td>
      <td>19.61</td>
      <td>-98.12</td>
      <td>296.150</td>
      <td>5.10</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Boston</td>
      <td>US</td>
      <td>90</td>
      <td>1522692900</td>
      <td>80</td>
      <td>42.36</td>
      <td>-71.06</td>
      <td>277.150</td>
      <td>2.10</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Hundorp</td>
      <td>NO</td>
      <td>8</td>
      <td>1522693909</td>
      <td>78</td>
      <td>61.55</td>
      <td>9.94</td>
      <td>265.058</td>
      <td>1.12</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Hustopece</td>
      <td>CZ</td>
      <td>0</td>
      <td>1522692000</td>
      <td>69</td>
      <td>48.94</td>
      <td>16.74</td>
      <td>280.150</td>
      <td>3.10</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Movilita</td>
      <td>RO</td>
      <td>0</td>
      <td>1522692000</td>
      <td>46</td>
      <td>44.65</td>
      <td>26.48</td>
      <td>284.150</td>
      <td>2.10</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Check if cities are different
non_duplicated_cities = []
duplicated_cities = []

for x in world_cities_info_df['City']:
    if x not in non_duplicated_cities:
        non_duplicated_cities.append(x)
    else:
        duplicated_cities.append(x)
        
duplicated_cities
```




    []




```python
# Defining the time and date for the graphs before
now = datetime.datetime.now()
current_datetime = now.strftime("%Y-%m-%d %H:%M")
```


```python
# Temperature (F) vs. Latitude
fig = sns.regplot(x = world_cities_info_df['Latitude'], y = world_cities_info_df['Max Temp'], fit_reg=False)
fig.set(xlabel = "Latitude", ylabel = "Max Temperature", title ="City Latitude vs Max Temperature (as of " + current_datetime + ")")
fig.figure.set_size_inches(9, 7)
sns.set_style("darkgrid")
plt.savefig('City Latitude vs Max Temperature.png')
```


![png](output_13_0.png)



```python
# Latitude vs. Humidity
fig = sns.regplot(x = world_cities_info_df['Latitude'], y = world_cities_info_df['Humidity'], fit_reg=False)
fig.set(xlabel = "Latitude", ylabel = "Humidity (%)", title ="City Latitude vs Humidity (as of " + current_datetime + ")")
fig.figure.set_size_inches(9, 7)
sns.set_style("darkgrid")
plt.savefig('City Latitude vs Humidity.png')
```


![png](output_14_0.png)



```python
# Cloudiness (%) vs. Latitude Plot
fig = sns.regplot(x = world_cities_info_df['Latitude'], y = world_cities_info_df['Cloudiness'], fit_reg=False)
fig.set(xlabel = "Latitude", ylabel = "Cloudiness (%)", title ="City Latitude vs Cloudiness (as of " + current_datetime + ")")
fig.figure.set_size_inches(9, 7)
sns.set_style("darkgrid")
plt.savefig('City Latitude vs Cloudiness.png')
```


![png](output_15_0.png)



```python
# Wind Speed (mph) vs. Latitude
fig = sns.regplot(x = world_cities_info_df['Latitude'], y = world_cities_info_df['Wind Speeds'], fit_reg=False)
fig.set(xlabel = "Latitude", ylabel = "Wind Speeds (MPH)", title ="City Latitude vs Wind Speeds (as of " + current_datetime + ")")
fig.figure.set_size_inches(9, 7)
sns.set_style("darkgrid")
plt.savefig('City Latitude vs Wind Speeds.png')
```


![png](output_16_0.png)

