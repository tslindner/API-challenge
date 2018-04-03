
<h1>WeatherPy</h1>

<big><b>Analysis</big></b><br>
<big><b>Observable Trend 1:  The temperature is hottest near the equator.<br></big></b>
<big><b>Observable Trend 2:  The temperature decreases farther away from the equator at a much faster rate in the northern hemisphere due to the tilt of the earth at this time of year.<br></big></b>
<big><b>Observable Trend 3:  The southern hemisphere is much more humid.</big></b>


```python
# Import Dependencies
import json
import requests
import random
import datetime
import pandas as pd
import matplotlib.pyplot as plt

from citipy import citipy
from config import api_key
```

<h1>Generate Cities List and Perform API Calls </h1>


```python
# Cities list, which will include country code
cities_list = []

# List of cities that citipy returns, but that don't have entries in OWM
worthless_cities = []

# Lists which will be used to build the dataframe
city = []
country = []
latitude = []
longitude = []
date = []
temp = []
humidity = []
cloudiness = []
windspeed = []

# OpenWeatherMap API
url = "http://api.openweathermap.org/data/2.5/weather?"

# Initialize count (need 500 cities with no duplicates that all play nice with OpenWeatherMap)
count = 0

# I'm curious to see how many get dropped where.
total_loops = 0
non_dupe_drop_loop = 0

# Big ole while loop that does everything.  Generate random coords, find cities, get data, skip anything that doesn't work.
while count < 5000:
    total_loops += 1
    
    # Final address for each loop's city
    address = []
    
    # Generation of random coords
    lat_loop = random.randint(-90, 90)
    long_loop = random.randint(-180, 180)
    
    # Find nearest city
    city_loop = citipy.nearest_city(lat_loop, long_loop)
    
    # Retrieving city name and country code from lat/long pair
    city_name = city_loop.city_name
    country_code = city_loop.country_code
    
    # Finalize address per loop
    address.append(city_name)
    address.append(country_code)
    
    # Check to make sure there are no dupe addresses
    if address not in cities_list and address not in worthless_cities:
        
        # Query url using current loop's city and specifying units as imperial
        query_url = f'{url}appid={api_key}&q={address[0]},{address[1]}&units=imperial'
        
        # Call, store response as JSON obj
        response = requests.get(query_url)
        json = response.json()
        
        # Many of the cities in citipy don't have any entries in openweathermap. I don't want empty values
        try:
            # Date retrieval, unix, UTC
            date_loop = json['dt']
            
            # For use in the charts later, convert into something readable
            date_loop = datetime.datetime.fromtimestamp(
            int(date_loop)
            ).strftime('%Y-%m-%d %H:%M:%S')

            # Temp retrieval, F
            temp_loop = json['main']['temp_max']

            # Humidity retrieval, %
            humidity_loop = json['main']['humidity']

            # Cloudiness retrieval, %
            cloud_loop = json['clouds']['all']

            # WindSpeed retrieval, mph
            wind_loop = json['wind']['speed']
            
            # Replace random coords with city's coords
            lat_loop = json['coord']['lat']
            long_loop = json['coord']['lon']

            # If the API call was successful in all attempts, add each value to its respective list
            city.append(city_name)
            country.append(country_code)
            latitude.append(lat_loop)
            longitude.append(long_loop)
            date.append(date_loop)
            temp.append(temp_loop)
            humidity.append(humidity_loop)
            cloudiness.append(cloud_loop)
            windspeed.append(wind_loop)
            cities_list.append(address)
                            
            # Hide my API key
            url_front = query_url[:53]
            url_back = query_url[85:]
            censor = ('*' * 32)
            printable_url = url_front + censor + url_back
            
            count += 1

            # Print
            print(f'Currently calling city number {count}:')
            print(f'{city_name}, {country_code}')
            print(printable_url)
            print('')

        except:
            non_dupe_drop_loop += 1
            worthless_cities.append(address)
            continue
    
    else:
        continue
```

    Currently calling city number 1:
    teya, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=teya,ru&units=imperial
    
    Currently calling city number 2:
    vaini, to
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vaini,to&units=imperial
    
    Currently calling city number 3:
    albany, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=albany,au&units=imperial
    
    Currently calling city number 4:
    barrow, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=barrow,us&units=imperial
    
    Currently calling city number 5:
    faanui, pf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=faanui,pf&units=imperial
    
    Currently calling city number 6:
    arraial do cabo, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=arraial do cabo,br&units=imperial
    
    Currently calling city number 7:
    punta arenas, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=punta arenas,cl&units=imperial
    
    Currently calling city number 8:
    xingyi, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=xingyi,cn&units=imperial
    
    Currently calling city number 9:
    esperance, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=esperance,au&units=imperial
    
    Currently calling city number 10:
    zheleznodorozhnyy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zheleznodorozhnyy,ru&units=imperial
    
    Currently calling city number 11:
    kambove, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kambove,cd&units=imperial
    
    Currently calling city number 12:
    new norfolk, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=new norfolk,au&units=imperial
    
    Currently calling city number 13:
    busselton, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=busselton,au&units=imperial
    
    Currently calling city number 14:
    jamestown, sh
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jamestown,sh&units=imperial
    
    Currently calling city number 15:
    mar del plata, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mar del plata,ar&units=imperial
    
    Currently calling city number 16:
    mondlo, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mondlo,za&units=imperial
    
    Currently calling city number 17:
    nouadhibou, mr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nouadhibou,mr&units=imperial
    
    Currently calling city number 18:
    yellowknife, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yellowknife,ca&units=imperial
    
    Currently calling city number 19:
    ostrovnoy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ostrovnoy,ru&units=imperial
    
    Currently calling city number 20:
    tigil, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tigil,ru&units=imperial
    
    Currently calling city number 21:
    bandarbeyla, so
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bandarbeyla,so&units=imperial
    
    Currently calling city number 22:
    bluff, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bluff,nz&units=imperial
    
    Currently calling city number 23:
    beringovskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=beringovskiy,ru&units=imperial
    
    Currently calling city number 24:
    aasiaat, gl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aasiaat,gl&units=imperial
    
    Currently calling city number 25:
    chokurdakh, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chokurdakh,ru&units=imperial
    
    Currently calling city number 26:
    pingliang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pingliang,cn&units=imperial
    
    Currently calling city number 27:
    henties bay, na
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=henties bay,na&units=imperial
    
    Currently calling city number 28:
    cape town, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cape town,za&units=imperial
    
    Currently calling city number 29:
    stokmarknes, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=stokmarknes,no&units=imperial
    
    Currently calling city number 30:
    bo, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bo,no&units=imperial
    
    Currently calling city number 31:
    narsaq, gl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=narsaq,gl&units=imperial
    
    Currently calling city number 32:
    kabo, cf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kabo,cf&units=imperial
    
    Currently calling city number 33:
    zhigansk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zhigansk,ru&units=imperial
    
    Currently calling city number 34:
    port alfred, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=port alfred,za&units=imperial
    
    Currently calling city number 35:
    cockburn town, bs
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cockburn town,bs&units=imperial
    
    Currently calling city number 36:
    kaeo, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kaeo,nz&units=imperial
    
    Currently calling city number 37:
    olinda, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=olinda,br&units=imperial
    
    Currently calling city number 38:
    sovetskaya, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sovetskaya,ru&units=imperial
    
    Currently calling city number 39:
    mehamn, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mehamn,no&units=imperial
    
    Currently calling city number 40:
    souillac, mu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=souillac,mu&units=imperial
    
    Currently calling city number 41:
    ancud, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ancud,cl&units=imperial
    
    Currently calling city number 42:
    hermanus, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hermanus,za&units=imperial
    
    Currently calling city number 43:
    ushuaia, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ushuaia,ar&units=imperial
    
    Currently calling city number 44:
    east london, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=east london,za&units=imperial
    
    Currently calling city number 45:
    qujing, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=qujing,cn&units=imperial
    
    Currently calling city number 46:
    port elizabeth, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=port elizabeth,za&units=imperial
    
    Currently calling city number 47:
    norman wells, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=norman wells,ca&units=imperial
    
    Currently calling city number 48:
    ocos, gt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ocos,gt&units=imperial
    
    Currently calling city number 49:
    safford, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=safford,us&units=imperial
    
    Currently calling city number 50:
    georgetown, sh
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=georgetown,sh&units=imperial
    
    Currently calling city number 51:
    cherskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cherskiy,ru&units=imperial
    
    Currently calling city number 52:
    ondangwa, na
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ondangwa,na&units=imperial
    
    Currently calling city number 53:
    salalah, om
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=salalah,om&units=imperial
    
    Currently calling city number 54:
    hithadhoo, mv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hithadhoo,mv&units=imperial
    
    Currently calling city number 55:
    kapaa, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kapaa,us&units=imperial
    
    Currently calling city number 56:
    saint-augustin, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saint-augustin,ca&units=imperial
    
    Currently calling city number 57:
    saint-philippe, re
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saint-philippe,re&units=imperial
    
    Currently calling city number 58:
    touros, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=touros,br&units=imperial
    
    Currently calling city number 59:
    nikolskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nikolskoye,ru&units=imperial
    
    Currently calling city number 60:
    kamenka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kamenka,ru&units=imperial
    
    Currently calling city number 61:
    qaanaaq, gl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=qaanaaq,gl&units=imperial
    
    Currently calling city number 62:
    taltal, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=taltal,cl&units=imperial
    
    Currently calling city number 63:
    amahai, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=amahai,id&units=imperial
    
    Currently calling city number 64:
    manggar, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=manggar,id&units=imperial
    
    Currently calling city number 65:
    grafton, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=grafton,us&units=imperial
    
    Currently calling city number 66:
    xapuri, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=xapuri,br&units=imperial
    
    Currently calling city number 67:
    port macquarie, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=port macquarie,au&units=imperial
    
    Currently calling city number 68:
    nishihara, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nishihara,jp&units=imperial
    
    Currently calling city number 69:
    wuzhou, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wuzhou,cn&units=imperial
    
    Currently calling city number 70:
    gavle, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gavle,se&units=imperial
    
    Currently calling city number 71:
    khatanga, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=khatanga,ru&units=imperial
    
    Currently calling city number 72:
    flin flon, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=flin flon,ca&units=imperial
    
    Currently calling city number 73:
    carnarvon, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=carnarvon,au&units=imperial
    
    Currently calling city number 74:
    calvinia, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=calvinia,za&units=imperial
    
    Currently calling city number 75:
    lavrentiya, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lavrentiya,ru&units=imperial
    
    Currently calling city number 76:
    rikitea, pf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rikitea,pf&units=imperial
    
    Currently calling city number 77:
    san cristobal, ec
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san cristobal,ec&units=imperial
    
    Currently calling city number 78:
    tignere, cm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tignere,cm&units=imperial
    
    Currently calling city number 79:
    bodmin, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bodmin,gb&units=imperial
    
    Currently calling city number 80:
    bambanglipuro, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bambanglipuro,id&units=imperial
    
    Currently calling city number 81:
    provideniya, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=provideniya,ru&units=imperial
    
    Currently calling city number 82:
    kaitangata, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kaitangata,nz&units=imperial
    
    Currently calling city number 83:
    la ronge, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=la ronge,ca&units=imperial
    
    Currently calling city number 84:
    paamiut, gl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=paamiut,gl&units=imperial
    
    Currently calling city number 85:
    iqaluit, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=iqaluit,ca&units=imperial
    
    Currently calling city number 86:
    ilulissat, gl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ilulissat,gl&units=imperial
    
    Currently calling city number 87:
    misasi, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=misasi,tz&units=imperial
    
    Currently calling city number 88:
    bethel, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bethel,us&units=imperial
    
    Currently calling city number 89:
    tongchuan, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tongchuan,cn&units=imperial
    
    Currently calling city number 90:
    saint george, bm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saint george,bm&units=imperial
    
    Currently calling city number 91:
    biak, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=biak,id&units=imperial
    
    Currently calling city number 92:
    noumea, nc
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=noumea,nc&units=imperial
    
    Currently calling city number 93:
    maun, bw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=maun,bw&units=imperial
    
    Currently calling city number 94:
    saskylakh, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saskylakh,ru&units=imperial
    
    Currently calling city number 95:
    pundaguitan, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pundaguitan,ph&units=imperial
    
    Currently calling city number 96:
    karratha, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=karratha,au&units=imperial
    
    Currently calling city number 97:
    thompson, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=thompson,ca&units=imperial
    
    Currently calling city number 98:
    nalut, ly
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nalut,ly&units=imperial
    
    Currently calling city number 99:
    erzin, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=erzin,ru&units=imperial
    
    Currently calling city number 100:
    pevek, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pevek,ru&units=imperial
    
    Currently calling city number 101:
    los llanos de aridane, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=los llanos de aridane,es&units=imperial
    
    Currently calling city number 102:
    batouri, cm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=batouri,cm&units=imperial
    
    Currently calling city number 103:
    cabanas, cu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cabanas,cu&units=imperial
    
    Currently calling city number 104:
    olga, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=olga,ru&units=imperial
    
    Currently calling city number 105:
    egvekinot, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=egvekinot,ru&units=imperial
    
    Currently calling city number 106:
    chulym, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chulym,ru&units=imperial
    
    Currently calling city number 107:
    puerto ayacucho, ve
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=puerto ayacucho,ve&units=imperial
    
    Currently calling city number 108:
    makakilo city, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=makakilo city,us&units=imperial
    
    Currently calling city number 109:
    bredasdorp, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bredasdorp,za&units=imperial
    
    Currently calling city number 110:
    sikonge, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sikonge,tz&units=imperial
    
    Currently calling city number 111:
    tuatapere, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tuatapere,nz&units=imperial
    
    Currently calling city number 112:
    khorixas, na
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=khorixas,na&units=imperial
    
    Currently calling city number 113:
    santa maria, cv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santa maria,cv&units=imperial
    
    Currently calling city number 114:
    butaritari, ki
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=butaritari,ki&units=imperial
    
    Currently calling city number 115:
    talnakh, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=talnakh,ru&units=imperial
    
    Currently calling city number 116:
    baykit, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=baykit,ru&units=imperial
    
    Currently calling city number 117:
    atuona, pf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=atuona,pf&units=imperial
    
    Currently calling city number 118:
    mount isa, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mount isa,au&units=imperial
    
    Currently calling city number 119:
    thinadhoo, mv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=thinadhoo,mv&units=imperial
    
    Currently calling city number 120:
    poya, nc
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=poya,nc&units=imperial
    
    Currently calling city number 121:
    hofn, is
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hofn,is&units=imperial
    
    Currently calling city number 122:
    mahebourg, mu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mahebourg,mu&units=imperial
    
    Currently calling city number 123:
    santa rosalia, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santa rosalia,mx&units=imperial
    
    Currently calling city number 124:
    soldotna, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=soldotna,us&units=imperial
    
    Currently calling city number 125:
    mount gambier, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mount gambier,au&units=imperial
    
    Currently calling city number 126:
    poum, nc
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=poum,nc&units=imperial
    
    Currently calling city number 127:
    severo-kurilsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=severo-kurilsk,ru&units=imperial
    
    Currently calling city number 128:
    hobart, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hobart,au&units=imperial
    
    Currently calling city number 129:
    cabo san lucas, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cabo san lucas,mx&units=imperial
    
    Currently calling city number 130:
    la celia, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=la celia,co&units=imperial
    
    Currently calling city number 131:
    lipin bor, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lipin bor,ru&units=imperial
    
    Currently calling city number 132:
    tasiilaq, gl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tasiilaq,gl&units=imperial
    
    Currently calling city number 133:
    campoverde, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=campoverde,pe&units=imperial
    
    Currently calling city number 134:
    castro, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=castro,cl&units=imperial
    
    Currently calling city number 135:
    waingapu, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=waingapu,id&units=imperial
    
    Currently calling city number 136:
    kodiak, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kodiak,us&units=imperial
    
    Currently calling city number 137:
    martapura, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=martapura,id&units=imperial
    
    Currently calling city number 138:
    upernavik, gl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=upernavik,gl&units=imperial
    
    Currently calling city number 139:
    lindi, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lindi,tz&units=imperial
    
    Currently calling city number 140:
    chimbote, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chimbote,pe&units=imperial
    
    Currently calling city number 141:
    kysyl-syr, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kysyl-syr,ru&units=imperial
    
    Currently calling city number 142:
    port lincoln, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=port lincoln,au&units=imperial
    
    Currently calling city number 143:
    puerto ayora, ec
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=puerto ayora,ec&units=imperial
    
    Currently calling city number 144:
    mersing, my
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mersing,my&units=imperial
    
    Currently calling city number 145:
    maputo, mz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=maputo,mz&units=imperial
    
    Currently calling city number 146:
    klaksvik, fo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=klaksvik,fo&units=imperial
    
    Currently calling city number 147:
    havre-saint-pierre, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=havre-saint-pierre,ca&units=imperial
    
    Currently calling city number 148:
    chuy, uy
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chuy,uy&units=imperial
    
    Currently calling city number 149:
    bikaner, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bikaner,in&units=imperial
    
    Currently calling city number 150:
    harper, lr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=harper,lr&units=imperial
    
    Currently calling city number 151:
    muisne, ec
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=muisne,ec&units=imperial
    
    Currently calling city number 152:
    keflavik, is
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=keflavik,is&units=imperial
    
    Currently calling city number 153:
    vostok, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vostok,ru&units=imperial
    
    Currently calling city number 154:
    itarema, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=itarema,br&units=imperial
    
    Currently calling city number 155:
    somerset, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=somerset,us&units=imperial
    
    Currently calling city number 156:
    isangel, vu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=isangel,vu&units=imperial
    
    Currently calling city number 157:
    paita, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=paita,pe&units=imperial
    
    Currently calling city number 158:
    kiama, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kiama,au&units=imperial
    
    Currently calling city number 159:
    sitka, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sitka,us&units=imperial
    
    Currently calling city number 160:
    lufilufi, ws
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lufilufi,ws&units=imperial
    
    Currently calling city number 161:
    victoria, sc
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=victoria,sc&units=imperial
    
    Currently calling city number 162:
    ponta do sol, cv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ponta do sol,cv&units=imperial
    
    Currently calling city number 163:
    grand-santi, gf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=grand-santi,gf&units=imperial
    
    Currently calling city number 164:
    okhotsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=okhotsk,ru&units=imperial
    
    Currently calling city number 165:
    talara, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=talara,pe&units=imperial
    
    Currently calling city number 166:
    khovu-aksy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=khovu-aksy,ru&units=imperial
    
    Currently calling city number 167:
    saldanha, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saldanha,za&units=imperial
    
    Currently calling city number 168:
    tiksi, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tiksi,ru&units=imperial
    
    Currently calling city number 169:
    vitre, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vitre,fr&units=imperial
    
    Currently calling city number 170:
    alta floresta, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alta floresta,br&units=imperial
    
    Currently calling city number 171:
    avarua, ck
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=avarua,ck&units=imperial
    
    Currently calling city number 172:
    sambava, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sambava,mg&units=imperial
    
    Currently calling city number 173:
    vieux-habitants, gp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vieux-habitants,gp&units=imperial
    
    Currently calling city number 174:
    port augusta, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=port augusta,au&units=imperial
    
    Currently calling city number 175:
    varhaug, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=varhaug,no&units=imperial
    
    Currently calling city number 176:
    tuktoyaktuk, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tuktoyaktuk,ca&units=imperial
    
    Currently calling city number 177:
    nha trang, vn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nha trang,vn&units=imperial
    
    Currently calling city number 178:
    cedral, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cedral,mx&units=imperial
    
    Currently calling city number 179:
    ahipara, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ahipara,nz&units=imperial
    
    Currently calling city number 180:
    nome, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nome,us&units=imperial
    
    Currently calling city number 181:
    pitimbu, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pitimbu,br&units=imperial
    
    Currently calling city number 182:
    valparaiso, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=valparaiso,cl&units=imperial
    
    Currently calling city number 183:
    belyy yar, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=belyy yar,ru&units=imperial
    
    Currently calling city number 184:
    katsuura, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=katsuura,jp&units=imperial
    
    Currently calling city number 185:
    podgornoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=podgornoye,ru&units=imperial
    
    Currently calling city number 186:
    lorengau, pg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lorengau,pg&units=imperial
    
    Currently calling city number 187:
    castries, lc
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=castries,lc&units=imperial
    
    Currently calling city number 188:
    shellbrook, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shellbrook,ca&units=imperial
    
    Currently calling city number 189:
    sandpoint, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sandpoint,us&units=imperial
    
    Currently calling city number 190:
    williston, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=williston,us&units=imperial
    
    Currently calling city number 191:
    dikson, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dikson,ru&units=imperial
    
    Currently calling city number 192:
    huilong, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=huilong,cn&units=imperial
    
    Currently calling city number 193:
    nanortalik, gl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nanortalik,gl&units=imperial
    
    Currently calling city number 194:
    yumen, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yumen,cn&units=imperial
    
    Currently calling city number 195:
    torbay, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=torbay,ca&units=imperial
    
    Currently calling city number 196:
    jalu, ly
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jalu,ly&units=imperial
    
    Currently calling city number 197:
    kutum, sd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kutum,sd&units=imperial
    
    Currently calling city number 198:
    ailigandi, pa
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ailigandi,pa&units=imperial
    
    Currently calling city number 199:
    cururupu, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cururupu,br&units=imperial
    
    Currently calling city number 200:
    xuchang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=xuchang,cn&units=imperial
    
    Currently calling city number 201:
    puerto escondido, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=puerto escondido,mx&units=imperial
    
    Currently calling city number 202:
    belaya kholunitsa, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=belaya kholunitsa,ru&units=imperial
    
    Currently calling city number 203:
    vila franca do campo, pt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vila franca do campo,pt&units=imperial
    
    Currently calling city number 204:
    jasper, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jasper,ca&units=imperial
    
    Currently calling city number 205:
    sigli, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sigli,id&units=imperial
    
    Currently calling city number 206:
    mahasamund, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mahasamund,in&units=imperial
    
    Currently calling city number 207:
    trinidad, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=trinidad,bo&units=imperial
    
    Currently calling city number 208:
    san patricio, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san patricio,mx&units=imperial
    
    Currently calling city number 209:
    alofi, nu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alofi,nu&units=imperial
    
    Currently calling city number 210:
    fresnillo, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fresnillo,mx&units=imperial
    
    Currently calling city number 211:
    xinyu, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=xinyu,cn&units=imperial
    
    Currently calling city number 212:
    grindavik, is
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=grindavik,is&units=imperial
    
    Currently calling city number 213:
    kalakamati, bw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kalakamati,bw&units=imperial
    
    Currently calling city number 214:
    leningradskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=leningradskiy,ru&units=imperial
    
    Currently calling city number 215:
    sibolga, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sibolga,id&units=imperial
    
    Currently calling city number 216:
    jumla, np
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jumla,np&units=imperial
    
    Currently calling city number 217:
    cabedelo, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cabedelo,br&units=imperial
    
    Currently calling city number 218:
    ketchikan, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ketchikan,us&units=imperial
    
    Currently calling city number 219:
    matamba, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=matamba,tz&units=imperial
    
    Currently calling city number 220:
    kushiro, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kushiro,jp&units=imperial
    
    Currently calling city number 221:
    marti, cu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=marti,cu&units=imperial
    
    Currently calling city number 222:
    itacoatiara, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=itacoatiara,br&units=imperial
    
    Currently calling city number 223:
    cidreira, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cidreira,br&units=imperial
    
    Currently calling city number 224:
    idukki, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=idukki,in&units=imperial
    
    Currently calling city number 225:
    gazojak, tm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gazojak,tm&units=imperial
    
    Currently calling city number 226:
    bambous virieux, mu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bambous virieux,mu&units=imperial
    
    Currently calling city number 227:
    strezhevoy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=strezhevoy,ru&units=imperial
    
    Currently calling city number 228:
    murray, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=murray,us&units=imperial
    
    Currently calling city number 229:
    ngunguru, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ngunguru,nz&units=imperial
    
    Currently calling city number 230:
    lompoc, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lompoc,us&units=imperial
    
    Currently calling city number 231:
    margate, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=margate,za&units=imperial
    
    Currently calling city number 232:
    the valley, ai
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=the valley,ai&units=imperial
    
    Currently calling city number 233:
    okha, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=okha,ru&units=imperial
    
    Currently calling city number 234:
    kahului, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kahului,us&units=imperial
    
    Currently calling city number 235:
    uglegorsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=uglegorsk,ru&units=imperial
    
    Currently calling city number 236:
    kandi, bj
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kandi,bj&units=imperial
    
    Currently calling city number 237:
    carutapera, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=carutapera,br&units=imperial
    
    Currently calling city number 238:
    iracoubo, gf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=iracoubo,gf&units=imperial
    
    Currently calling city number 239:
    bhasawar, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bhasawar,in&units=imperial
    
    Currently calling city number 240:
    hinche, ht
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hinche,ht&units=imperial
    
    Currently calling city number 241:
    tha mai, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tha mai,th&units=imperial
    
    Currently calling city number 242:
    high level, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=high level,ca&units=imperial
    
    Currently calling city number 243:
    sao francisco, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sao francisco,br&units=imperial
    
    Currently calling city number 244:
    chapais, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chapais,ca&units=imperial
    
    Currently calling city number 245:
    ginir, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ginir,et&units=imperial
    
    Currently calling city number 246:
    sechura, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sechura,pe&units=imperial
    
    Currently calling city number 247:
    ubinskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ubinskoye,ru&units=imperial
    
    Currently calling city number 248:
    virginia beach, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=virginia beach,us&units=imperial
    
    Currently calling city number 249:
    aguimes, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aguimes,es&units=imperial
    
    Currently calling city number 250:
    wanaka, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wanaka,nz&units=imperial
    
    Currently calling city number 251:
    aklavik, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aklavik,ca&units=imperial
    
    Currently calling city number 252:
    chenzhou, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chenzhou,cn&units=imperial
    
    Currently calling city number 253:
    skibbereen, ie
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=skibbereen,ie&units=imperial
    
    Currently calling city number 254:
    clyde river, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=clyde river,ca&units=imperial
    
    Currently calling city number 255:
    agogo, gh
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=agogo,gh&units=imperial
    
    Currently calling city number 256:
    mayo, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mayo,ca&units=imperial
    
    Currently calling city number 257:
    dunedin, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dunedin,nz&units=imperial
    
    Currently calling city number 258:
    fare, pf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fare,pf&units=imperial
    
    Currently calling city number 259:
    fougamou, ga
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fougamou,ga&units=imperial
    
    Currently calling city number 260:
    taree, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=taree,au&units=imperial
    
    Currently calling city number 261:
    te anau, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=te anau,nz&units=imperial
    
    Currently calling city number 262:
    lalmohan, bd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lalmohan,bd&units=imperial
    
    Currently calling city number 263:
    lebu, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lebu,cl&units=imperial
    
    Currently calling city number 264:
    nemuro, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nemuro,jp&units=imperial
    
    Currently calling city number 265:
    munster, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=munster,de&units=imperial
    
    Currently calling city number 266:
    yar-sale, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yar-sale,ru&units=imperial
    
    Currently calling city number 267:
    coihaique, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=coihaique,cl&units=imperial
    
    Currently calling city number 268:
    shintomi, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shintomi,jp&units=imperial
    
    Currently calling city number 269:
    pokhara, np
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pokhara,np&units=imperial
    
    Currently calling city number 270:
    newport, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=newport,us&units=imperial
    
    Currently calling city number 271:
    port blair, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=port blair,in&units=imperial
    
    Currently calling city number 272:
    amuntai, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=amuntai,id&units=imperial
    
    Currently calling city number 273:
    manaure, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=manaure,co&units=imperial
    
    Currently calling city number 274:
    hasaki, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hasaki,jp&units=imperial
    
    Currently calling city number 275:
    dingle, ie
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dingle,ie&units=imperial
    
    Currently calling city number 276:
    tarakan, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tarakan,id&units=imperial
    
    Currently calling city number 277:
    tiznit, ma
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tiznit,ma&units=imperial
    
    Currently calling city number 278:
    atar, mr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=atar,mr&units=imperial
    
    Currently calling city number 279:
    broome, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=broome,au&units=imperial
    
    Currently calling city number 280:
    anadyr, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=anadyr,ru&units=imperial
    
    Currently calling city number 281:
    sainte-maxime, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sainte-maxime,fr&units=imperial
    
    Currently calling city number 282:
    ikom, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ikom,ng&units=imperial
    
    Currently calling city number 283:
    iquitos, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=iquitos,pe&units=imperial
    
    Currently calling city number 284:
    kavaratti, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kavaratti,in&units=imperial
    
    Currently calling city number 285:
    cartagena, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cartagena,es&units=imperial
    
    Currently calling city number 286:
    sao sebastiao do paraiso, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sao sebastiao do paraiso,br&units=imperial
    
    Currently calling city number 287:
    sunrise manor, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sunrise manor,us&units=imperial
    
    Currently calling city number 288:
    ranong, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ranong,th&units=imperial
    
    Currently calling city number 289:
    talas, kg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=talas,kg&units=imperial
    
    Currently calling city number 290:
    springbok, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=springbok,za&units=imperial
    
    Currently calling city number 291:
    portel, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=portel,br&units=imperial
    
    Currently calling city number 292:
    tazovskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tazovskiy,ru&units=imperial
    
    Currently calling city number 293:
    honiara, sb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=honiara,sb&units=imperial
    
    Currently calling city number 294:
    mantua, cu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mantua,cu&units=imperial
    
    Currently calling city number 295:
    aykhal, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aykhal,ru&units=imperial
    
    Currently calling city number 296:
    vega de alatorre, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vega de alatorre,mx&units=imperial
    
    Currently calling city number 297:
    kiruna, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kiruna,se&units=imperial
    
    Currently calling city number 298:
    qostanay, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=qostanay,kz&units=imperial
    
    Currently calling city number 299:
    huarmey, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=huarmey,pe&units=imperial
    
    Currently calling city number 300:
    geraldton, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=geraldton,au&units=imperial
    
    Currently calling city number 301:
    nuqui, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nuqui,co&units=imperial
    
    Currently calling city number 302:
    yingkou, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yingkou,cn&units=imperial
    
    Currently calling city number 303:
    altay, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=altay,cn&units=imperial
    
    Currently calling city number 304:
    oktyabrskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=oktyabrskoye,ru&units=imperial
    
    Currently calling city number 305:
    imbituba, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=imbituba,br&units=imperial
    
    Currently calling city number 306:
    valley city, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=valley city,us&units=imperial
    
    Currently calling city number 307:
    gedinne, be
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gedinne,be&units=imperial
    
    Currently calling city number 308:
    boralday, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=boralday,kz&units=imperial
    
    Currently calling city number 309:
    soyo, ao
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=soyo,ao&units=imperial
    
    Currently calling city number 310:
    northam, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=northam,au&units=imperial
    
    Currently calling city number 311:
    chiredzi, zw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chiredzi,zw&units=imperial
    
    Currently calling city number 312:
    kailua, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kailua,us&units=imperial
    
    Currently calling city number 313:
    selkirk, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=selkirk,ca&units=imperial
    
    Currently calling city number 314:
    tessalit, ml
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tessalit,ml&units=imperial
    
    Currently calling city number 315:
    garmsar, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=garmsar,ir&units=imperial
    
    Currently calling city number 316:
    oistins, bb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=oistins,bb&units=imperial
    
    Currently calling city number 317:
    concarneau, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=concarneau,fr&units=imperial
    
    Currently calling city number 318:
    koslan, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=koslan,ru&units=imperial
    
    Currently calling city number 319:
    brownwood, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=brownwood,us&units=imperial
    
    Currently calling city number 320:
    mitu, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mitu,co&units=imperial
    
    Currently calling city number 321:
    arona, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=arona,es&units=imperial
    
    Currently calling city number 322:
    tolosa, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tolosa,es&units=imperial
    
    Currently calling city number 323:
    morehead, pg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=morehead,pg&units=imperial
    
    Currently calling city number 324:
    srednekolymsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=srednekolymsk,ru&units=imperial
    
    Currently calling city number 325:
    cheltenham, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cheltenham,gb&units=imperial
    
    Currently calling city number 326:
    abalak, ne
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=abalak,ne&units=imperial
    
    Currently calling city number 327:
    kadyy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kadyy,ru&units=imperial
    
    Currently calling city number 328:
    portland, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=portland,au&units=imperial
    
    Currently calling city number 329:
    lethem, gy
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lethem,gy&units=imperial
    
    Currently calling city number 330:
    antofagasta, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=antofagasta,cl&units=imperial
    
    Currently calling city number 331:
    solnechnyy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=solnechnyy,ru&units=imperial
    
    Currently calling city number 332:
    grozesti, ro
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=grozesti,ro&units=imperial
    
    Currently calling city number 333:
    vila velha, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vila velha,br&units=imperial
    
    Currently calling city number 334:
    villa carlos paz, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=villa carlos paz,ar&units=imperial
    
    Currently calling city number 335:
    tobias barreto, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tobias barreto,br&units=imperial
    
    Currently calling city number 336:
    ceres, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ceres,za&units=imperial
    
    Currently calling city number 337:
    sinnamary, gf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sinnamary,gf&units=imperial
    
    Currently calling city number 338:
    tarsus, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tarsus,tr&units=imperial
    
    Currently calling city number 339:
    hilo, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hilo,us&units=imperial
    
    Currently calling city number 340:
    sabang, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sabang,id&units=imperial
    
    Currently calling city number 341:
    petropavlovsk-kamchatskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=petropavlovsk-kamchatskiy,ru&units=imperial
    
    Currently calling city number 342:
    naze, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=naze,jp&units=imperial
    
    Currently calling city number 343:
    drabiv, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=drabiv,ua&units=imperial
    
    Currently calling city number 344:
    bollnas, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bollnas,se&units=imperial
    
    Currently calling city number 345:
    lagoa, pt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lagoa,pt&units=imperial
    
    Currently calling city number 346:
    zeya, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zeya,ru&units=imperial
    
    Currently calling city number 347:
    shitanjing, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shitanjing,cn&units=imperial
    
    Currently calling city number 348:
    phalaborwa, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=phalaborwa,za&units=imperial
    
    Currently calling city number 349:
    santo augusto, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santo augusto,br&units=imperial
    
    Currently calling city number 350:
    kaitong, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kaitong,cn&units=imperial
    
    Currently calling city number 351:
    sulangan, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sulangan,ph&units=imperial
    
    Currently calling city number 352:
    vestmannaeyjar, is
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vestmannaeyjar,is&units=imperial
    
    Currently calling city number 353:
    chudniv, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chudniv,ua&units=imperial
    
    Currently calling city number 354:
    vetluga, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vetluga,ru&units=imperial
    
    Currently calling city number 355:
    tautira, pf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tautira,pf&units=imperial
    
    Currently calling city number 356:
    dzhebariki-khaya, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dzhebariki-khaya,ru&units=imperial
    
    Currently calling city number 357:
    kant, kg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kant,kg&units=imperial
    
    Currently calling city number 358:
    ribeira grande, pt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ribeira grande,pt&units=imperial
    
    Currently calling city number 359:
    portales, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=portales,us&units=imperial
    
    Currently calling city number 360:
    oussouye, sn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=oussouye,sn&units=imperial
    
    Currently calling city number 361:
    luderitz, na
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=luderitz,na&units=imperial
    
    Currently calling city number 362:
    deputatskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=deputatskiy,ru&units=imperial
    
    Currently calling city number 363:
    vysoke, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vysoke,ua&units=imperial
    
    Currently calling city number 364:
    havelock, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=havelock,us&units=imperial
    
    Currently calling city number 365:
    moussoro, td
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=moussoro,td&units=imperial
    
    Currently calling city number 366:
    balikpapan, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=balikpapan,id&units=imperial
    
    Currently calling city number 367:
    batemans bay, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=batemans bay,au&units=imperial
    
    Currently calling city number 368:
    melilla, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=melilla,es&units=imperial
    
    Currently calling city number 369:
    brcko, ba
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=brcko,ba&units=imperial
    
    Currently calling city number 370:
    russkaya polyana, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=russkaya polyana,ru&units=imperial
    
    Currently calling city number 371:
    kieta, pg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kieta,pg&units=imperial
    
    Currently calling city number 372:
    tuy hoa, vn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tuy hoa,vn&units=imperial
    
    Currently calling city number 373:
    orsha, by
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=orsha,by&units=imperial
    
    Currently calling city number 374:
    sergeyevka, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sergeyevka,kz&units=imperial
    
    Currently calling city number 375:
    yuci, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yuci,cn&units=imperial
    
    Currently calling city number 376:
    fortuna, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fortuna,us&units=imperial
    
    Currently calling city number 377:
    shimoda, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shimoda,jp&units=imperial
    
    Currently calling city number 378:
    hambantota, lk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hambantota,lk&units=imperial
    
    Currently calling city number 379:
    quatre cocos, mu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=quatre cocos,mu&units=imperial
    
    Currently calling city number 380:
    tevriz, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tevriz,ru&units=imperial
    
    Currently calling city number 381:
    guarapari, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=guarapari,br&units=imperial
    
    Currently calling city number 382:
    pisco, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pisco,pe&units=imperial
    
    Currently calling city number 383:
    san vicente, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san vicente,ph&units=imperial
    
    Currently calling city number 384:
    el triunfo, ec
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=el triunfo,ec&units=imperial
    
    Currently calling city number 385:
    shache, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shache,cn&units=imperial
    
    Currently calling city number 386:
    praia da vitoria, pt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=praia da vitoria,pt&units=imperial
    
    Currently calling city number 387:
    brainerd, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=brainerd,us&units=imperial
    
    Currently calling city number 388:
    korem, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=korem,et&units=imperial
    
    Currently calling city number 389:
    kande, tg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kande,tg&units=imperial
    
    Currently calling city number 390:
    homer, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=homer,us&units=imperial
    
    Currently calling city number 391:
    sergiyevsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sergiyevsk,ru&units=imperial
    
    Currently calling city number 392:
    aswan, eg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aswan,eg&units=imperial
    
    Currently calling city number 393:
    acapulco, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=acapulco,mx&units=imperial
    
    Currently calling city number 394:
    dudinka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dudinka,ru&units=imperial
    
    Currently calling city number 395:
    san ramon, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san ramon,bo&units=imperial
    
    Currently calling city number 396:
    suez, eg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=suez,eg&units=imperial
    
    Currently calling city number 397:
    flinders, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=flinders,au&units=imperial
    
    Currently calling city number 398:
    is, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=is,ru&units=imperial
    
    Currently calling city number 399:
    cumpas, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cumpas,mx&units=imperial
    
    Currently calling city number 400:
    antigonish, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=antigonish,ca&units=imperial
    
    Currently calling city number 401:
    boende, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=boende,cd&units=imperial
    
    Currently calling city number 402:
    chegdomyn, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chegdomyn,ru&units=imperial
    
    Currently calling city number 403:
    kargasok, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kargasok,ru&units=imperial
    
    Currently calling city number 404:
    lichtenfels, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lichtenfels,de&units=imperial
    
    Currently calling city number 405:
    jishou, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jishou,cn&units=imperial
    
    Currently calling city number 406:
    kultuk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kultuk,ru&units=imperial
    
    Currently calling city number 407:
    bilibino, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bilibino,ru&units=imperial
    
    Currently calling city number 408:
    tilichiki, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tilichiki,ru&units=imperial
    
    Currently calling city number 409:
    orje, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=orje,no&units=imperial
    
    Currently calling city number 410:
    cap malheureux, mu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cap malheureux,mu&units=imperial
    
    Currently calling city number 411:
    mwinilunga, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mwinilunga,zm&units=imperial
    
    Currently calling city number 412:
    mucurapo, tt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mucurapo,tt&units=imperial
    
    Currently calling city number 413:
    saint-pierre, pm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saint-pierre,pm&units=imperial
    
    Currently calling city number 414:
    naryan-mar, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=naryan-mar,ru&units=imperial
    
    Currently calling city number 415:
    iskateley, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=iskateley,ru&units=imperial
    
    Currently calling city number 416:
    meulaboh, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=meulaboh,id&units=imperial
    
    Currently calling city number 417:
    ulladulla, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ulladulla,au&units=imperial
    
    Currently calling city number 418:
    cacu, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cacu,br&units=imperial
    
    Currently calling city number 419:
    maragogi, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=maragogi,br&units=imperial
    
    Currently calling city number 420:
    mackay, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mackay,au&units=imperial
    
    Currently calling city number 421:
    coquimbo, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=coquimbo,cl&units=imperial
    
    Currently calling city number 422:
    darlawn, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=darlawn,in&units=imperial
    
    Currently calling city number 423:
    cananeia, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cananeia,br&units=imperial
    
    Currently calling city number 424:
    batagay-alyta, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=batagay-alyta,ru&units=imperial
    
    Currently calling city number 425:
    camacha, pt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=camacha,pt&units=imperial
    
    Currently calling city number 426:
    port hedland, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=port hedland,au&units=imperial
    
    Currently calling city number 427:
    gizo, sb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gizo,sb&units=imperial
    
    Currently calling city number 428:
    kruisfontein, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kruisfontein,za&units=imperial
    
    Currently calling city number 429:
    krivosheino, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=krivosheino,ru&units=imperial
    
    Currently calling city number 430:
    ibipeba, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ibipeba,br&units=imperial
    
    Currently calling city number 431:
    papillion, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=papillion,us&units=imperial
    
    Currently calling city number 432:
    nizhniy chir, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nizhniy chir,ru&units=imperial
    
    Currently calling city number 433:
    port hardy, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=port hardy,ca&units=imperial
    
    Currently calling city number 434:
    wattegama, lk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wattegama,lk&units=imperial
    
    Currently calling city number 435:
    inuvik, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=inuvik,ca&units=imperial
    
    Currently calling city number 436:
    chicama, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chicama,pe&units=imperial
    
    Currently calling city number 437:
    clovis, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=clovis,us&units=imperial
    
    Currently calling city number 438:
    cayenne, gf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cayenne,gf&units=imperial
    
    Currently calling city number 439:
    fort-shevchenko, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fort-shevchenko,kz&units=imperial
    
    Currently calling city number 440:
    belmonte, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=belmonte,br&units=imperial
    
    Currently calling city number 441:
    awbari, ly
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=awbari,ly&units=imperial
    
    Currently calling city number 442:
    dombarovskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dombarovskiy,ru&units=imperial
    
    Currently calling city number 443:
    alice springs, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alice springs,au&units=imperial
    
    Currently calling city number 444:
    bontang, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bontang,id&units=imperial
    
    Currently calling city number 445:
    mbanza-ngungu, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mbanza-ngungu,cd&units=imperial
    
    Currently calling city number 446:
    roma, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=roma,au&units=imperial
    
    Currently calling city number 447:
    port-gentil, ga
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=port-gentil,ga&units=imperial
    
    Currently calling city number 448:
    rio gallegos, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rio gallegos,ar&units=imperial
    
    Currently calling city number 449:
    dongsheng, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dongsheng,cn&units=imperial
    
    Currently calling city number 450:
    natal, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=natal,br&units=imperial
    
    Currently calling city number 451:
    rocha, uy
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rocha,uy&units=imperial
    
    Currently calling city number 452:
    belawan, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=belawan,id&units=imperial
    
    Currently calling city number 453:
    vao, nc
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vao,nc&units=imperial
    
    Currently calling city number 454:
    manicaragua, cu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=manicaragua,cu&units=imperial
    
    Currently calling city number 455:
    boa vista, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=boa vista,br&units=imperial
    
    Currently calling city number 456:
    san andres, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san andres,co&units=imperial
    
    Currently calling city number 457:
    teguldet, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=teguldet,ru&units=imperial
    
    Currently calling city number 458:
    ejea de los caballeros, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ejea de los caballeros,es&units=imperial
    
    Currently calling city number 459:
    kisesa, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kisesa,tz&units=imperial
    
    Currently calling city number 460:
    fuyang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fuyang,cn&units=imperial
    
    Currently calling city number 461:
    nsoko, sz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nsoko,sz&units=imperial
    
    Currently calling city number 462:
    pine bluff, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pine bluff,us&units=imperial
    
    Currently calling city number 463:
    glendive, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=glendive,us&units=imperial
    
    Currently calling city number 464:
    montrose, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=montrose,us&units=imperial
    
    Currently calling city number 465:
    batsfjord, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=batsfjord,no&units=imperial
    
    Currently calling city number 466:
    gonbad-e qabus, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gonbad-e qabus,ir&units=imperial
    
    Currently calling city number 467:
    longyearbyen, sj
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=longyearbyen,sj&units=imperial
    
    Currently calling city number 468:
    bonavista, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bonavista,ca&units=imperial
    
    Currently calling city number 469:
    sarangani, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sarangani,ph&units=imperial
    
    Currently calling city number 470:
    mendeleyevo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mendeleyevo,ru&units=imperial
    
    Currently calling city number 471:
    erzurum, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=erzurum,tr&units=imperial
    
    Currently calling city number 472:
    martigny, ch
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=martigny,ch&units=imperial
    
    Currently calling city number 473:
    san angelo, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san angelo,us&units=imperial
    
    Currently calling city number 474:
    constitucion, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=constitucion,mx&units=imperial
    
    Currently calling city number 475:
    kati, ml
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kati,ml&units=imperial
    
    Currently calling city number 476:
    malyye derbety, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=malyye derbety,ru&units=imperial
    
    Currently calling city number 477:
    marawi, sd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=marawi,sd&units=imperial
    
    Currently calling city number 478:
    guerrero negro, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=guerrero negro,mx&units=imperial
    
    Currently calling city number 479:
    berlevag, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=berlevag,no&units=imperial
    
    Currently calling city number 480:
    abu kamal, sy
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=abu kamal,sy&units=imperial
    
    Currently calling city number 481:
    rostovka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rostovka,ru&units=imperial
    
    Currently calling city number 482:
    sohag, eg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sohag,eg&units=imperial
    
    Currently calling city number 483:
    tabuk, sa
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tabuk,sa&units=imperial
    
    Currently calling city number 484:
    saiki, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saiki,jp&units=imperial
    
    Currently calling city number 485:
    schruns, at
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=schruns,at&units=imperial
    
    Currently calling city number 486:
    sao filipe, cv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sao filipe,cv&units=imperial
    
    Currently calling city number 487:
    prince albert, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=prince albert,ca&units=imperial
    
    Currently calling city number 488:
    lubango, ao
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lubango,ao&units=imperial
    
    Currently calling city number 489:
    qasigiannguit, gl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=qasigiannguit,gl&units=imperial
    
    Currently calling city number 490:
    sayyan, ye
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sayyan,ye&units=imperial
    
    Currently calling city number 491:
    maceio, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=maceio,br&units=imperial
    
    Currently calling city number 492:
    adrar, dz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=adrar,dz&units=imperial
    
    Currently calling city number 493:
    moyo, ug
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=moyo,ug&units=imperial
    
    Currently calling city number 494:
    barreirinha, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=barreirinha,br&units=imperial
    
    Currently calling city number 495:
    takoradi, gh
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=takoradi,gh&units=imperial
    
    Currently calling city number 496:
    kone, nc
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kone,nc&units=imperial
    
    Currently calling city number 497:
    kedrovyy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kedrovyy,ru&units=imperial
    
    Currently calling city number 498:
    muros, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=muros,es&units=imperial
    
    Currently calling city number 499:
    amot, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=amot,no&units=imperial
    
    Currently calling city number 500:
    kuah, my
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kuah,my&units=imperial
    
    Currently calling city number 501:
    srandakan, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=srandakan,id&units=imperial
    
    Currently calling city number 502:
    sorland, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sorland,no&units=imperial
    
    Currently calling city number 503:
    soria, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=soria,es&units=imperial
    
    Currently calling city number 504:
    la palma, pa
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=la palma,pa&units=imperial
    
    Currently calling city number 505:
    boksitogorsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=boksitogorsk,ru&units=imperial
    
    Currently calling city number 506:
    nastola, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nastola,fi&units=imperial
    
    Currently calling city number 507:
    moindou, nc
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=moindou,nc&units=imperial
    
    Currently calling city number 508:
    terrace, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=terrace,ca&units=imperial
    
    Currently calling city number 509:
    nantucket, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nantucket,us&units=imperial
    
    Currently calling city number 510:
    nuuk, gl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nuuk,gl&units=imperial
    
    Currently calling city number 511:
    fatehpur, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fatehpur,pk&units=imperial
    
    Currently calling city number 512:
    christchurch, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=christchurch,nz&units=imperial
    
    Currently calling city number 513:
    nichinan, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nichinan,jp&units=imperial
    
    Currently calling city number 514:
    can tho, vn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=can tho,vn&units=imperial
    
    Currently calling city number 515:
    dergachi, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dergachi,ru&units=imperial
    
    Currently calling city number 516:
    kavieng, pg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kavieng,pg&units=imperial
    
    Currently calling city number 517:
    marshall, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=marshall,us&units=imperial
    
    Currently calling city number 518:
    villamontes, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=villamontes,bo&units=imperial
    
    Currently calling city number 519:
    kaya, bf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kaya,bf&units=imperial
    
    Currently calling city number 520:
    hayesville, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hayesville,us&units=imperial
    
    Currently calling city number 521:
    tutoia, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tutoia,br&units=imperial
    
    Currently calling city number 522:
    palana, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=palana,ru&units=imperial
    
    Currently calling city number 523:
    port keats, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=port keats,au&units=imperial
    
    Currently calling city number 524:
    hobyo, so
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hobyo,so&units=imperial
    
    Currently calling city number 525:
    qingdao, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=qingdao,cn&units=imperial
    
    Currently calling city number 526:
    ambilobe, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ambilobe,mg&units=imperial
    
    Currently calling city number 527:
    waipawa, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=waipawa,nz&units=imperial
    
    Currently calling city number 528:
    mahibadhoo, mv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mahibadhoo,mv&units=imperial
    
    Currently calling city number 529:
    choma, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=choma,zm&units=imperial
    
    Currently calling city number 530:
    sofiyivka, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sofiyivka,ua&units=imperial
    
    Currently calling city number 531:
    svetlogorsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=svetlogorsk,ru&units=imperial
    
    Currently calling city number 532:
    inirida, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=inirida,co&units=imperial
    
    Currently calling city number 533:
    marfino, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=marfino,ru&units=imperial
    
    Currently calling city number 534:
    stornoway, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=stornoway,gb&units=imperial
    
    Currently calling city number 535:
    kununurra, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kununurra,au&units=imperial
    
    Currently calling city number 536:
    ust-kulom, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ust-kulom,ru&units=imperial
    
    Currently calling city number 537:
    byron bay, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=byron bay,au&units=imperial
    
    Currently calling city number 538:
    rawson, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rawson,ar&units=imperial
    
    Currently calling city number 539:
    guaymas, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=guaymas,mx&units=imperial
    
    Currently calling city number 540:
    balaguer, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=balaguer,es&units=imperial
    
    Currently calling city number 541:
    ankazoabo, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ankazoabo,mg&units=imperial
    
    Currently calling city number 542:
    conde, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=conde,br&units=imperial
    
    Currently calling city number 543:
    kathmandu, np
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kathmandu,np&units=imperial
    
    Currently calling city number 544:
    jacareacanga, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jacareacanga,br&units=imperial
    
    Currently calling city number 545:
    mangochi, mw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mangochi,mw&units=imperial
    
    Currently calling city number 546:
    ankazobe, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ankazobe,mg&units=imperial
    
    Currently calling city number 547:
    chumikan, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chumikan,ru&units=imperial
    
    Currently calling city number 548:
    aparecida do taboado, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aparecida do taboado,br&units=imperial
    
    Currently calling city number 549:
    sungaipenuh, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sungaipenuh,id&units=imperial
    
    Currently calling city number 550:
    gunjur, gm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gunjur,gm&units=imperial
    
    Currently calling city number 551:
    prince rupert, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=prince rupert,ca&units=imperial
    
    Currently calling city number 552:
    bulgan, mn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bulgan,mn&units=imperial
    
    Currently calling city number 553:
    pangnirtung, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pangnirtung,ca&units=imperial
    
    Currently calling city number 554:
    gamba, ga
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gamba,ga&units=imperial
    
    Currently calling city number 555:
    husavik, is
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=husavik,is&units=imperial
    
    Currently calling city number 556:
    wakkanai, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wakkanai,jp&units=imperial
    
    Currently calling city number 557:
    yeniseysk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yeniseysk,ru&units=imperial
    
    Currently calling city number 558:
    brae, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=brae,gb&units=imperial
    
    Currently calling city number 559:
    tepalcatepec, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tepalcatepec,mx&units=imperial
    
    Currently calling city number 560:
    siderno, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=siderno,it&units=imperial
    
    Currently calling city number 561:
    semnan, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=semnan,ir&units=imperial
    
    Currently calling city number 562:
    carbonia, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=carbonia,it&units=imperial
    
    Currently calling city number 563:
    vardo, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vardo,no&units=imperial
    
    Currently calling city number 564:
    chute-aux-outardes, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chute-aux-outardes,ca&units=imperial
    
    Currently calling city number 565:
    minab, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=minab,ir&units=imperial
    
    Currently calling city number 566:
    livingston, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=livingston,us&units=imperial
    
    Currently calling city number 567:
    mercedes, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mercedes,ar&units=imperial
    
    Currently calling city number 568:
    ambanja, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ambanja,mg&units=imperial
    
    Currently calling city number 569:
    yagodnoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yagodnoye,ru&units=imperial
    
    Currently calling city number 570:
    khuchni, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=khuchni,ru&units=imperial
    
    Currently calling city number 571:
    lourdes, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lourdes,fr&units=imperial
    
    Currently calling city number 572:
    california city, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=california city,us&units=imperial
    
    Currently calling city number 573:
    sulina, ro
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sulina,ro&units=imperial
    
    Currently calling city number 574:
    mogadishu, so
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mogadishu,so&units=imperial
    
    Currently calling city number 575:
    ust-tsilma, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ust-tsilma,ru&units=imperial
    
    Currently calling city number 576:
    raudeberg, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=raudeberg,no&units=imperial
    
    Currently calling city number 577:
    goderich, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=goderich,ca&units=imperial
    
    Currently calling city number 578:
    karachi, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=karachi,pk&units=imperial
    
    Currently calling city number 579:
    wajima, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wajima,jp&units=imperial
    
    Currently calling city number 580:
    nanakuli, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nanakuli,us&units=imperial
    
    Currently calling city number 581:
    dauphin, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dauphin,ca&units=imperial
    
    Currently calling city number 582:
    turayf, sa
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=turayf,sa&units=imperial
    
    Currently calling city number 583:
    taoudenni, ml
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=taoudenni,ml&units=imperial
    
    Currently calling city number 584:
    yulara, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yulara,au&units=imperial
    
    Currently calling city number 585:
    kodinsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kodinsk,ru&units=imperial
    
    Currently calling city number 586:
    gashua, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gashua,ng&units=imperial
    
    Currently calling city number 587:
    manchar, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=manchar,in&units=imperial
    
    Currently calling city number 588:
    sao jose da coroa grande, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sao jose da coroa grande,br&units=imperial
    
    Currently calling city number 589:
    namibe, ao
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=namibe,ao&units=imperial
    
    Currently calling city number 590:
    tizimin, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tizimin,mx&units=imperial
    
    Currently calling city number 591:
    caravelas, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=caravelas,br&units=imperial
    
    Currently calling city number 592:
    rosetta, eg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rosetta,eg&units=imperial
    
    Currently calling city number 593:
    iralaya, hn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=iralaya,hn&units=imperial
    
    Currently calling city number 594:
    straumen, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=straumen,no&units=imperial
    
    Currently calling city number 595:
    brandon, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=brandon,us&units=imperial
    
    Currently calling city number 596:
    ambon, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ambon,id&units=imperial
    
    Currently calling city number 597:
    barcelos, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=barcelos,br&units=imperial
    
    Currently calling city number 598:
    ust-kut, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ust-kut,ru&units=imperial
    
    Currently calling city number 599:
    urengoy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=urengoy,ru&units=imperial
    
    Currently calling city number 600:
    ozernovskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ozernovskiy,ru&units=imperial
    
    Currently calling city number 601:
    vestmanna, fo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vestmanna,fo&units=imperial
    
    Currently calling city number 602:
    luena, ao
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=luena,ao&units=imperial
    
    Currently calling city number 603:
    tombouctou, ml
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tombouctou,ml&units=imperial
    
    Currently calling city number 604:
    kirakira, sb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kirakira,sb&units=imperial
    
    Currently calling city number 605:
    lokosovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lokosovo,ru&units=imperial
    
    Currently calling city number 606:
    mastic beach, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mastic beach,us&units=imperial
    
    Currently calling city number 607:
    angoche, mz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=angoche,mz&units=imperial
    
    Currently calling city number 608:
    port moresby, pg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=port moresby,pg&units=imperial
    
    Currently calling city number 609:
    aksarka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aksarka,ru&units=imperial
    
    Currently calling city number 610:
    manoel urbano, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=manoel urbano,br&units=imperial
    
    Currently calling city number 611:
    hervey bay, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hervey bay,au&units=imperial
    
    Currently calling city number 612:
    neiafu, to
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=neiafu,to&units=imperial
    
    Currently calling city number 613:
    dingzhou, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dingzhou,cn&units=imperial
    
    Currently calling city number 614:
    nyurba, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nyurba,ru&units=imperial
    
    Currently calling city number 615:
    bilma, ne
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bilma,ne&units=imperial
    
    Currently calling city number 616:
    skjervoy, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=skjervoy,no&units=imperial
    
    Currently calling city number 617:
    kapit, my
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kapit,my&units=imperial
    
    Currently calling city number 618:
    saint-denis, re
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saint-denis,re&units=imperial
    
    Currently calling city number 619:
    pimentel, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pimentel,pe&units=imperial
    
    Currently calling city number 620:
    totness, sr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=totness,sr&units=imperial
    
    Currently calling city number 621:
    arauca, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=arauca,co&units=imperial
    
    Currently calling city number 622:
    galle, lk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=galle,lk&units=imperial
    
    Currently calling city number 623:
    cabras, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cabras,it&units=imperial
    
    Currently calling city number 624:
    preeceville, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=preeceville,ca&units=imperial
    
    Currently calling city number 625:
    sopelana, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sopelana,es&units=imperial
    
    Currently calling city number 626:
    nelson bay, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nelson bay,au&units=imperial
    
    Currently calling city number 627:
    maxixe, mz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=maxixe,mz&units=imperial
    
    Currently calling city number 628:
    mitsamiouli, km
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mitsamiouli,km&units=imperial
    
    Currently calling city number 629:
    balkhash, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=balkhash,kz&units=imperial
    
    Currently calling city number 630:
    gasa, bt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gasa,bt&units=imperial
    
    Currently calling city number 631:
    si bun ruang, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=si bun ruang,th&units=imperial
    
    Currently calling city number 632:
    cairns, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cairns,au&units=imperial
    
    Currently calling city number 633:
    smidovich, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=smidovich,ru&units=imperial
    
    Currently calling city number 634:
    yerbogachen, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yerbogachen,ru&units=imperial
    
    Currently calling city number 635:
    karatuzskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=karatuzskoye,ru&units=imperial
    
    Currently calling city number 636:
    presidencia roque saenz pena, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=presidencia roque saenz pena,ar&units=imperial
    
    Currently calling city number 637:
    goulburn, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=goulburn,au&units=imperial
    
    Currently calling city number 638:
    beidao, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=beidao,cn&units=imperial
    
    Currently calling city number 639:
    serabu, sl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=serabu,sl&units=imperial
    
    Currently calling city number 640:
    georgiyevka, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=georgiyevka,kz&units=imperial
    
    Currently calling city number 641:
    bouca, cf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bouca,cf&units=imperial
    
    Currently calling city number 642:
    vysokogornyy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vysokogornyy,ru&units=imperial
    
    Currently calling city number 643:
    sivaki, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sivaki,ru&units=imperial
    
    Currently calling city number 644:
    amga, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=amga,ru&units=imperial
    
    Currently calling city number 645:
    bonthe, sl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bonthe,sl&units=imperial
    
    Currently calling city number 646:
    north platte, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=north platte,us&units=imperial
    
    Currently calling city number 647:
    nyagan, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nyagan,ru&units=imperial
    
    Currently calling city number 648:
    qaqortoq, gl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=qaqortoq,gl&units=imperial
    
    Currently calling city number 649:
    ereymentau, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ereymentau,kz&units=imperial
    
    Currently calling city number 650:
    parabel, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=parabel,ru&units=imperial
    
    Currently calling city number 651:
    sisimiut, gl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sisimiut,gl&units=imperial
    
    Currently calling city number 652:
    sao felix do xingu, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sao felix do xingu,br&units=imperial
    
    Currently calling city number 653:
    xining, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=xining,cn&units=imperial
    
    Currently calling city number 654:
    zyryanka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zyryanka,ru&units=imperial
    
    Currently calling city number 655:
    willmar, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=willmar,us&units=imperial
    
    Currently calling city number 656:
    westport, ie
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=westport,ie&units=imperial
    
    Currently calling city number 657:
    calabozo, ve
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=calabozo,ve&units=imperial
    
    Currently calling city number 658:
    peachland, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=peachland,ca&units=imperial
    
    Currently calling city number 659:
    knin, hr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=knin,hr&units=imperial
    
    Currently calling city number 660:
    charyshskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=charyshskoye,ru&units=imperial
    
    Currently calling city number 661:
    pandan, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pandan,ph&units=imperial
    
    Currently calling city number 662:
    sistranda, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sistranda,no&units=imperial
    
    Currently calling city number 663:
    akdepe, tm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=akdepe,tm&units=imperial
    
    Currently calling city number 664:
    kosh-agach, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kosh-agach,ru&units=imperial
    
    Currently calling city number 665:
    buluang, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=buluang,ph&units=imperial
    
    Currently calling city number 666:
    sur, om
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sur,om&units=imperial
    
    Currently calling city number 667:
    kapuskasing, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kapuskasing,ca&units=imperial
    
    Currently calling city number 668:
    eureka, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=eureka,us&units=imperial
    
    Currently calling city number 669:
    vaitape, pf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vaitape,pf&units=imperial
    
    Currently calling city number 670:
    oranjemund, na
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=oranjemund,na&units=imperial
    
    Currently calling city number 671:
    ust-kuyga, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ust-kuyga,ru&units=imperial
    
    Currently calling city number 672:
    estelle, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=estelle,us&units=imperial
    
    Currently calling city number 673:
    college, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=college,us&units=imperial
    
    Currently calling city number 674:
    usinsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=usinsk,ru&units=imperial
    
    Currently calling city number 675:
    ruteng, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ruteng,id&units=imperial
    
    Currently calling city number 676:
    padang, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=padang,id&units=imperial
    
    Currently calling city number 677:
    jatai, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jatai,br&units=imperial
    
    Currently calling city number 678:
    bayan, kw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bayan,kw&units=imperial
    
    Currently calling city number 679:
    verkhnevilyuysk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=verkhnevilyuysk,ru&units=imperial
    
    Currently calling city number 680:
    labuhan, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=labuhan,id&units=imperial
    
    Currently calling city number 681:
    nizhniy odes, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nizhniy odes,ru&units=imperial
    
    Currently calling city number 682:
    tianpeng, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tianpeng,cn&units=imperial
    
    Currently calling city number 683:
    werneck, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=werneck,de&units=imperial
    
    Currently calling city number 684:
    hanzhong, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hanzhong,cn&units=imperial
    
    Currently calling city number 685:
    nowshera, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nowshera,pk&units=imperial
    
    Currently calling city number 686:
    haapiti, pf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=haapiti,pf&units=imperial
    
    Currently calling city number 687:
    valea nucarilor, ro
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=valea nucarilor,ro&units=imperial
    
    Currently calling city number 688:
    lundazi, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lundazi,zm&units=imperial
    
    Currently calling city number 689:
    vagur, fo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vagur,fo&units=imperial
    
    Currently calling city number 690:
    yook, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yook,ph&units=imperial
    
    Currently calling city number 691:
    mairana, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mairana,bo&units=imperial
    
    Currently calling city number 692:
    hukuntsi, bw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hukuntsi,bw&units=imperial
    
    Currently calling city number 693:
    sundargarh, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sundargarh,in&units=imperial
    
    Currently calling city number 694:
    batagay, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=batagay,ru&units=imperial
    
    Currently calling city number 695:
    walvis bay, na
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=walvis bay,na&units=imperial
    
    Currently calling city number 696:
    rennes, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rennes,fr&units=imperial
    
    Currently calling city number 697:
    licata, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=licata,it&units=imperial
    
    Currently calling city number 698:
    kon tum, vn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kon tum,vn&units=imperial
    
    Currently calling city number 699:
    taicheng, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=taicheng,cn&units=imperial
    
    Currently calling city number 700:
    sao joao da barra, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sao joao da barra,br&units=imperial
    
    Currently calling city number 701:
    lillooet, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lillooet,ca&units=imperial
    
    Currently calling city number 702:
    goias, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=goias,br&units=imperial
    
    Currently calling city number 703:
    bud, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bud,no&units=imperial
    
    Currently calling city number 704:
    dhidhdhoo, mv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dhidhdhoo,mv&units=imperial
    
    Currently calling city number 705:
    tateyama, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tateyama,jp&units=imperial
    
    Currently calling city number 706:
    carnarvon, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=carnarvon,za&units=imperial
    
    Currently calling city number 707:
    ljungby, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ljungby,se&units=imperial
    
    Currently calling city number 708:
    fort nelson, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fort nelson,ca&units=imperial
    
    Currently calling city number 709:
    tsuyama, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tsuyama,jp&units=imperial
    
    Currently calling city number 710:
    richards bay, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=richards bay,za&units=imperial
    
    Currently calling city number 711:
    eydhafushi, mv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=eydhafushi,mv&units=imperial
    
    Currently calling city number 712:
    nakhon thai, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nakhon thai,th&units=imperial
    
    Currently calling city number 713:
    lazaro cardenas, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lazaro cardenas,mx&units=imperial
    
    Currently calling city number 714:
    bell ville, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bell ville,ar&units=imperial
    
    Currently calling city number 715:
    xinqing, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=xinqing,cn&units=imperial
    
    Currently calling city number 716:
    jinchang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jinchang,cn&units=imperial
    
    Currently calling city number 717:
    aksu, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aksu,cn&units=imperial
    
    Currently calling city number 718:
    dromolaxia, cy
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dromolaxia,cy&units=imperial
    
    Currently calling city number 719:
    puri, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=puri,in&units=imperial
    
    Currently calling city number 720:
    marsa matruh, eg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=marsa matruh,eg&units=imperial
    
    Currently calling city number 721:
    canto do buriti, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=canto do buriti,br&units=imperial
    
    Currently calling city number 722:
    bardiyah, ly
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bardiyah,ly&units=imperial
    
    Currently calling city number 723:
    saltpond, gh
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saltpond,gh&units=imperial
    
    Currently calling city number 724:
    maldonado, uy
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=maldonado,uy&units=imperial
    
    Currently calling city number 725:
    townsville, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=townsville,au&units=imperial
    
    Currently calling city number 726:
    nuevo progreso, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nuevo progreso,mx&units=imperial
    
    Currently calling city number 727:
    dana point, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dana point,us&units=imperial
    
    Currently calling city number 728:
    dunda, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dunda,tz&units=imperial
    
    Currently calling city number 729:
    madingou, cg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=madingou,cg&units=imperial
    
    Currently calling city number 730:
    riyadh, sa
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=riyadh,sa&units=imperial
    
    Currently calling city number 731:
    isiro, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=isiro,cd&units=imperial
    
    Currently calling city number 732:
    bayanday, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bayanday,ru&units=imperial
    
    Currently calling city number 733:
    yinchuan, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yinchuan,cn&units=imperial
    
    Currently calling city number 734:
    aleksandrovsk-sakhalinskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aleksandrovsk-sakhalinskiy,ru&units=imperial
    
    Currently calling city number 735:
    coahuayana, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=coahuayana,mx&units=imperial
    
    Currently calling city number 736:
    daru, pg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=daru,pg&units=imperial
    
    Currently calling city number 737:
    voskevan, am
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=voskevan,am&units=imperial
    
    Currently calling city number 738:
    kuruman, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kuruman,za&units=imperial
    
    Currently calling city number 739:
    silver city, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=silver city,us&units=imperial
    
    Currently calling city number 740:
    tura, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tura,ru&units=imperial
    
    Currently calling city number 741:
    kenai, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kenai,us&units=imperial
    
    Currently calling city number 742:
    campbell river, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=campbell river,ca&units=imperial
    
    Currently calling city number 743:
    camabatela, ao
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=camabatela,ao&units=imperial
    
    Currently calling city number 744:
    owensboro, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=owensboro,us&units=imperial
    
    Currently calling city number 745:
    mildura, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mildura,au&units=imperial
    
    Currently calling city number 746:
    araouane, ml
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=araouane,ml&units=imperial
    
    Currently calling city number 747:
    plettenberg bay, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=plettenberg bay,za&units=imperial
    
    Currently calling city number 748:
    bathsheba, bb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bathsheba,bb&units=imperial
    
    Currently calling city number 749:
    mecca, sa
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mecca,sa&units=imperial
    
    Currently calling city number 750:
    malanje, ao
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=malanje,ao&units=imperial
    
    Currently calling city number 751:
    mana, gf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mana,gf&units=imperial
    
    Currently calling city number 752:
    manokwari, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=manokwari,id&units=imperial
    
    Currently calling city number 753:
    nalvo, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nalvo,ph&units=imperial
    
    Currently calling city number 754:
    porto walter, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=porto walter,br&units=imperial
    
    Currently calling city number 755:
    turmalina, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=turmalina,br&units=imperial
    
    Currently calling city number 756:
    puqi, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=puqi,cn&units=imperial
    
    Currently calling city number 757:
    primo tapia, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=primo tapia,mx&units=imperial
    
    Currently calling city number 758:
    san policarpo, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san policarpo,ph&units=imperial
    
    Currently calling city number 759:
    whitehorse, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=whitehorse,ca&units=imperial
    
    Currently calling city number 760:
    ponta delgada, pt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ponta delgada,pt&units=imperial
    
    Currently calling city number 761:
    quthing, ls
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=quthing,ls&units=imperial
    
    Currently calling city number 762:
    hit, iq
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hit,iq&units=imperial
    
    Currently calling city number 763:
    luanda, ao
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=luanda,ao&units=imperial
    
    Currently calling city number 764:
    haines junction, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=haines junction,ca&units=imperial
    
    Currently calling city number 765:
    maltahohe, na
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=maltahohe,na&units=imperial
    
    Currently calling city number 766:
    bejar, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bejar,es&units=imperial
    
    Currently calling city number 767:
    wenzhou, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wenzhou,cn&units=imperial
    
    Currently calling city number 768:
    marondera, zw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=marondera,zw&units=imperial
    
    Currently calling city number 769:
    tabou, ci
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tabou,ci&units=imperial
    
    Currently calling city number 770:
    hualmay, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hualmay,pe&units=imperial
    
    Currently calling city number 771:
    broken hill, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=broken hill,au&units=imperial
    
    Currently calling city number 772:
    baruun-urt, mn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=baruun-urt,mn&units=imperial
    
    Currently calling city number 773:
    hibbing, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hibbing,us&units=imperial
    
    Currently calling city number 774:
    sete, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sete,fr&units=imperial
    
    Currently calling city number 775:
    san rafael del sur, ni
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san rafael del sur,ni&units=imperial
    
    Currently calling city number 776:
    palmas bellas, pa
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=palmas bellas,pa&units=imperial
    
    Currently calling city number 777:
    mahbubabad, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mahbubabad,in&units=imperial
    
    Currently calling city number 778:
    fairbanks, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fairbanks,us&units=imperial
    
    Currently calling city number 779:
    yarmouth, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yarmouth,ca&units=imperial
    
    Currently calling city number 780:
    quesnel, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=quesnel,ca&units=imperial
    
    Currently calling city number 781:
    merritt island, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=merritt island,us&units=imperial
    
    Currently calling city number 782:
    tahoua, ne
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tahoua,ne&units=imperial
    
    Currently calling city number 783:
    bria, cf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bria,cf&units=imperial
    
    Currently calling city number 784:
    quepos, cr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=quepos,cr&units=imperial
    
    Currently calling city number 785:
    arkhipo-osipovka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=arkhipo-osipovka,ru&units=imperial
    
    Currently calling city number 786:
    magnolia, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=magnolia,us&units=imperial
    
    Currently calling city number 787:
    hirara, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hirara,jp&units=imperial
    
    Currently calling city number 788:
    jamame, so
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jamame,so&units=imperial
    
    Currently calling city number 789:
    geraldton, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=geraldton,ca&units=imperial
    
    Currently calling city number 790:
    havoysund, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=havoysund,no&units=imperial
    
    Currently calling city number 791:
    luganville, vu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=luganville,vu&units=imperial
    
    Currently calling city number 792:
    baker city, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=baker city,us&units=imperial
    
    Currently calling city number 793:
    tommot, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tommot,ru&units=imperial
    
    Currently calling city number 794:
    toledo, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=toledo,es&units=imperial
    
    Currently calling city number 795:
    capitao poco, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=capitao poco,br&units=imperial
    
    Currently calling city number 796:
    batalha, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=batalha,br&units=imperial
    
    Currently calling city number 797:
    salinopolis, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=salinopolis,br&units=imperial
    
    Currently calling city number 798:
    sale, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sale,au&units=imperial
    
    Currently calling city number 799:
    monte alegre de minas, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=monte alegre de minas,br&units=imperial
    
    Currently calling city number 800:
    vanavara, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vanavara,ru&units=imperial
    
    Currently calling city number 801:
    mandalgovi, mn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mandalgovi,mn&units=imperial
    
    Currently calling city number 802:
    peleduy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=peleduy,ru&units=imperial
    
    Currently calling city number 803:
    senador jose porfirio, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=senador jose porfirio,br&units=imperial
    
    Currently calling city number 804:
    pulaski, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pulaski,us&units=imperial
    
    Currently calling city number 805:
    mozarlandia, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mozarlandia,br&units=imperial
    
    Currently calling city number 806:
    sinait, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sinait,ph&units=imperial
    
    Currently calling city number 807:
    mirandopolis, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mirandopolis,br&units=imperial
    
    Currently calling city number 808:
    cilibia, ro
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cilibia,ro&units=imperial
    
    Currently calling city number 809:
    bolobo, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bolobo,cd&units=imperial
    
    Currently calling city number 810:
    black river, jm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=black river,jm&units=imperial
    
    Currently calling city number 811:
    rancho palos verdes, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rancho palos verdes,us&units=imperial
    
    Currently calling city number 812:
    puerto narino, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=puerto narino,co&units=imperial
    
    Currently calling city number 813:
    gornyy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gornyy,ru&units=imperial
    
    Currently calling city number 814:
    mindelo, cv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mindelo,cv&units=imperial
    
    Currently calling city number 815:
    buritizeiro, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=buritizeiro,br&units=imperial
    
    Currently calling city number 816:
    kirkwall, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kirkwall,gb&units=imperial
    
    Currently calling city number 817:
    mangrol, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mangrol,in&units=imperial
    
    Currently calling city number 818:
    swan hill, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=swan hill,au&units=imperial
    
    Currently calling city number 819:
    santa cruz, cr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santa cruz,cr&units=imperial
    
    Currently calling city number 820:
    dubbo, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dubbo,au&units=imperial
    
    Currently calling city number 821:
    mosquera, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mosquera,co&units=imperial
    
    Currently calling city number 822:
    poyarkovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=poyarkovo,ru&units=imperial
    
    Currently calling city number 823:
    luangwa, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=luangwa,zm&units=imperial
    
    Currently calling city number 824:
    urozhaynoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=urozhaynoye,ru&units=imperial
    
    Currently calling city number 825:
    ambulu, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ambulu,id&units=imperial
    
    Currently calling city number 826:
    ixtapa, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ixtapa,mx&units=imperial
    
    Currently calling city number 827:
    hostotipaquillo, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hostotipaquillo,mx&units=imperial
    
    Currently calling city number 828:
    kilindoni, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kilindoni,tz&units=imperial
    
    Currently calling city number 829:
    agirish, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=agirish,ru&units=imperial
    
    Currently calling city number 830:
    russell, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=russell,nz&units=imperial
    
    Currently calling city number 831:
    umba, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=umba,ru&units=imperial
    
    Currently calling city number 832:
    velingrad, bg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=velingrad,bg&units=imperial
    
    Currently calling city number 833:
    nadym, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nadym,ru&units=imperial
    
    Currently calling city number 834:
    sombrio, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sombrio,br&units=imperial
    
    Currently calling city number 835:
    brownsville, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=brownsville,us&units=imperial
    
    Currently calling city number 836:
    vila do maio, cv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vila do maio,cv&units=imperial
    
    Currently calling city number 837:
    miraflores, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=miraflores,co&units=imperial
    
    Currently calling city number 838:
    farrukhnagar, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=farrukhnagar,in&units=imperial
    
    Currently calling city number 839:
    atasu, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=atasu,kz&units=imperial
    
    Currently calling city number 840:
    pauini, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pauini,br&units=imperial
    
    Currently calling city number 841:
    bosaso, so
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bosaso,so&units=imperial
    
    Currently calling city number 842:
    matara, lk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=matara,lk&units=imperial
    
    Currently calling city number 843:
    lingyuan, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lingyuan,cn&units=imperial
    
    Currently calling city number 844:
    lastoursville, ga
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lastoursville,ga&units=imperial
    
    Currently calling city number 845:
    ishigaki, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ishigaki,jp&units=imperial
    
    Currently calling city number 846:
    bay roberts, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bay roberts,ca&units=imperial
    
    Currently calling city number 847:
    rolla, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rolla,us&units=imperial
    
    Currently calling city number 848:
    tual, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tual,id&units=imperial
    
    Currently calling city number 849:
    pacific grove, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pacific grove,us&units=imperial
    
    Currently calling city number 850:
    komsomolskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=komsomolskiy,ru&units=imperial
    
    Currently calling city number 851:
    zhaoyang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zhaoyang,cn&units=imperial
    
    Currently calling city number 852:
    ampanihy, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ampanihy,mg&units=imperial
    
    Currently calling city number 853:
    naranjal, py
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=naranjal,py&units=imperial
    
    Currently calling city number 854:
    pimenta bueno, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pimenta bueno,br&units=imperial
    
    Currently calling city number 855:
    mendi, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mendi,et&units=imperial
    
    Currently calling city number 856:
    tyubuk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tyubuk,ru&units=imperial
    
    Currently calling city number 857:
    tocopilla, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tocopilla,cl&units=imperial
    
    Currently calling city number 858:
    ito, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ito,jp&units=imperial
    
    Currently calling city number 859:
    gorele, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gorele,tr&units=imperial
    
    Currently calling city number 860:
    lambarene, ga
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lambarene,ga&units=imperial
    
    Currently calling city number 861:
    atbasar, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=atbasar,kz&units=imperial
    
    Currently calling city number 862:
    isaka, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=isaka,tz&units=imperial
    
    Currently calling city number 863:
    suamico, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=suamico,us&units=imperial
    
    Currently calling city number 864:
    san juan, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san juan,ar&units=imperial
    
    Currently calling city number 865:
    novikovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=novikovo,ru&units=imperial
    
    Currently calling city number 866:
    tooele, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tooele,us&units=imperial
    
    Currently calling city number 867:
    kisangani, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kisangani,cd&units=imperial
    
    Currently calling city number 868:
    chara, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chara,ru&units=imperial
    
    Currently calling city number 869:
    severodvinsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=severodvinsk,ru&units=imperial
    
    Currently calling city number 870:
    lexington, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lexington,us&units=imperial
    
    Currently calling city number 871:
    araguaina, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=araguaina,br&units=imperial
    
    Currently calling city number 872:
    masingbi, sl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=masingbi,sl&units=imperial
    
    Currently calling city number 873:
    palasa, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=palasa,in&units=imperial
    
    Currently calling city number 874:
    channagiri, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=channagiri,in&units=imperial
    
    Currently calling city number 875:
    casa grande, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=casa grande,us&units=imperial
    
    Currently calling city number 876:
    santa teresa, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santa teresa,ph&units=imperial
    
    Currently calling city number 877:
    lieksa, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lieksa,fi&units=imperial
    
    Currently calling city number 878:
    kardla, ee
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kardla,ee&units=imperial
    
    Currently calling city number 879:
    stykkisholmur, is
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=stykkisholmur,is&units=imperial
    
    Currently calling city number 880:
    muborak, uz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=muborak,uz&units=imperial
    
    Currently calling city number 881:
    manbij, sy
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=manbij,sy&units=imperial
    
    Currently calling city number 882:
    imeni poliny osipenko, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=imeni poliny osipenko,ru&units=imperial
    
    Currently calling city number 883:
    ohara, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ohara,jp&units=imperial
    
    Currently calling city number 884:
    hamilton, bm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hamilton,bm&units=imperial
    
    Currently calling city number 885:
    nagapattinam, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nagapattinam,in&units=imperial
    
    Currently calling city number 886:
    kattivakkam, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kattivakkam,in&units=imperial
    
    Currently calling city number 887:
    lekoni, ga
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lekoni,ga&units=imperial
    
    Currently calling city number 888:
    pachino, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pachino,it&units=imperial
    
    Currently calling city number 889:
    talas, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=talas,tr&units=imperial
    
    Currently calling city number 890:
    saint-leu, re
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saint-leu,re&units=imperial
    
    Currently calling city number 891:
    san luis, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san luis,pe&units=imperial
    
    Currently calling city number 892:
    viedma, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=viedma,ar&units=imperial
    
    Currently calling city number 893:
    girona, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=girona,es&units=imperial
    
    Currently calling city number 894:
    port hawkesbury, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=port hawkesbury,ca&units=imperial
    
    Currently calling city number 895:
    washim, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=washim,in&units=imperial
    
    Currently calling city number 896:
    umm kaddadah, sd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=umm kaddadah,sd&units=imperial
    
    Currently calling city number 897:
    mananjary, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mananjary,mg&units=imperial
    
    Currently calling city number 898:
    vanimo, pg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vanimo,pg&units=imperial
    
    Currently calling city number 899:
    ola, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ola,ru&units=imperial
    
    Currently calling city number 900:
    kaka, tm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kaka,tm&units=imperial
    
    Currently calling city number 901:
    tynda, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tynda,ru&units=imperial
    
    Currently calling city number 902:
    matamoros, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=matamoros,mx&units=imperial
    
    Currently calling city number 903:
    kendari, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kendari,id&units=imperial
    
    Currently calling city number 904:
    banyo, cm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=banyo,cm&units=imperial
    
    Currently calling city number 905:
    sines, pt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sines,pt&units=imperial
    
    Currently calling city number 906:
    kizhinga, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kizhinga,ru&units=imperial
    
    Currently calling city number 907:
    kiunga, pg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kiunga,pg&units=imperial
    
    Currently calling city number 908:
    lodja, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lodja,cd&units=imperial
    
    Currently calling city number 909:
    eyl, so
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=eyl,so&units=imperial
    
    Currently calling city number 910:
    orchard homes, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=orchard homes,us&units=imperial
    
    Currently calling city number 911:
    mnogovershinnyy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mnogovershinnyy,ru&units=imperial
    
    Currently calling city number 912:
    darnah, ly
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=darnah,ly&units=imperial
    
    Currently calling city number 913:
    kontagora, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kontagora,ng&units=imperial
    
    Currently calling city number 914:
    la asuncion, ve
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=la asuncion,ve&units=imperial
    
    Currently calling city number 915:
    pidhorodna, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pidhorodna,ua&units=imperial
    
    Currently calling city number 916:
    remanso, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=remanso,br&units=imperial
    
    Currently calling city number 917:
    starosubkhangulovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=starosubkhangulovo,ru&units=imperial
    
    Currently calling city number 918:
    nara, ml
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nara,ml&units=imperial
    
    Currently calling city number 919:
    buraydah, sa
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=buraydah,sa&units=imperial
    
    Currently calling city number 920:
    cascais, pt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cascais,pt&units=imperial
    
    Currently calling city number 921:
    monrovia, lr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=monrovia,lr&units=imperial
    
    Currently calling city number 922:
    seymchan, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=seymchan,ru&units=imperial
    
    Currently calling city number 923:
    valkeakoski, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=valkeakoski,fi&units=imperial
    
    Currently calling city number 924:
    abonnema, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=abonnema,ng&units=imperial
    
    Currently calling city number 925:
    mabaruma, gy
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mabaruma,gy&units=imperial
    
    Currently calling city number 926:
    werda, bw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=werda,bw&units=imperial
    
    Currently calling city number 927:
    enkoping, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=enkoping,se&units=imperial
    
    Currently calling city number 928:
    koumac, nc
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=koumac,nc&units=imperial
    
    Currently calling city number 929:
    liverpool, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=liverpool,ca&units=imperial
    
    Currently calling city number 930:
    kimbe, pg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kimbe,pg&units=imperial
    
    Currently calling city number 931:
    san joaquin, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san joaquin,bo&units=imperial
    
    Currently calling city number 932:
    nakusp, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nakusp,ca&units=imperial
    
    Currently calling city number 933:
    kalianget, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kalianget,id&units=imperial
    
    Currently calling city number 934:
    zaozerne, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zaozerne,ua&units=imperial
    
    Currently calling city number 935:
    alamogordo, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alamogordo,us&units=imperial
    
    Currently calling city number 936:
    gotsu, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gotsu,jp&units=imperial
    
    Currently calling city number 937:
    pinar del rio, cu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pinar del rio,cu&units=imperial
    
    Currently calling city number 938:
    san carlos de bariloche, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san carlos de bariloche,ar&units=imperial
    
    Currently calling city number 939:
    nyakahanga, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nyakahanga,tz&units=imperial
    
    Currently calling city number 940:
    alyangula, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alyangula,au&units=imperial
    
    Currently calling city number 941:
    katobu, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=katobu,id&units=imperial
    
    Currently calling city number 942:
    south lake tahoe, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=south lake tahoe,us&units=imperial
    
    Currently calling city number 943:
    champasak, la
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=champasak,la&units=imperial
    
    Currently calling city number 944:
    hovd, mn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hovd,mn&units=imperial
    
    Currently calling city number 945:
    olyka, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=olyka,ua&units=imperial
    
    Currently calling city number 946:
    evanston, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=evanston,us&units=imperial
    
    Currently calling city number 947:
    amilly, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=amilly,fr&units=imperial
    
    Currently calling city number 948:
    la romana, do
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=la romana,do&units=imperial
    
    Currently calling city number 949:
    orangeburg, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=orangeburg,us&units=imperial
    
    Currently calling city number 950:
    gimbi, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gimbi,et&units=imperial
    
    Currently calling city number 951:
    isla vista, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=isla vista,us&units=imperial
    
    Currently calling city number 952:
    charters towers, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=charters towers,au&units=imperial
    
    Currently calling city number 953:
    pervomayskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pervomayskoye,ru&units=imperial
    
    Currently calling city number 954:
    kajaani, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kajaani,fi&units=imperial
    
    Currently calling city number 955:
    pitiquito, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pitiquito,mx&units=imperial
    
    Currently calling city number 956:
    amambai, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=amambai,br&units=imperial
    
    Currently calling city number 957:
    boguchany, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=boguchany,ru&units=imperial
    
    Currently calling city number 958:
    bullhead city, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bullhead city,us&units=imperial
    
    Currently calling city number 959:
    kota kinabalu, my
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kota kinabalu,my&units=imperial
    
    Currently calling city number 960:
    halmstad, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=halmstad,se&units=imperial
    
    Currently calling city number 961:
    kirovskaya, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kirovskaya,ru&units=imperial
    
    Currently calling city number 962:
    miandoab, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=miandoab,ir&units=imperial
    
    Currently calling city number 963:
    melita, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=melita,ca&units=imperial
    
    Currently calling city number 964:
    leshan, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=leshan,cn&units=imperial
    
    Currently calling city number 965:
    serik, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=serik,tr&units=imperial
    
    Currently calling city number 966:
    haflong, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=haflong,in&units=imperial
    
    Currently calling city number 967:
    arroio grande, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=arroio grande,br&units=imperial
    
    Currently calling city number 968:
    abomey, bj
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=abomey,bj&units=imperial
    
    Currently calling city number 969:
    kerema, pg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kerema,pg&units=imperial
    
    Currently calling city number 970:
    la paz, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=la paz,mx&units=imperial
    
    Currently calling city number 971:
    pastavy, by
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pastavy,by&units=imperial
    
    Currently calling city number 972:
    sampit, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sampit,id&units=imperial
    
    Currently calling city number 973:
    ludvika, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ludvika,se&units=imperial
    
    Currently calling city number 974:
    wyndham, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wyndham,nz&units=imperial
    
    Currently calling city number 975:
    merauke, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=merauke,id&units=imperial
    
    Currently calling city number 976:
    mujiayingzi, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mujiayingzi,cn&units=imperial
    
    Currently calling city number 977:
    sertanopolis, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sertanopolis,br&units=imperial
    
    Currently calling city number 978:
    keetmanshoop, na
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=keetmanshoop,na&units=imperial
    
    Currently calling city number 979:
    new haven, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=new haven,us&units=imperial
    
    Currently calling city number 980:
    kindu, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kindu,cd&units=imperial
    
    Currently calling city number 981:
    lucea, jm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lucea,jm&units=imperial
    
    Currently calling city number 982:
    sontra, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sontra,de&units=imperial
    
    Currently calling city number 983:
    buala, sb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=buala,sb&units=imperial
    
    Currently calling city number 984:
    dire dawa, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dire dawa,et&units=imperial
    
    Currently calling city number 985:
    nouakchott, mr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nouakchott,mr&units=imperial
    
    Currently calling city number 986:
    mouila, ga
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mouila,ga&units=imperial
    
    Currently calling city number 987:
    harrison, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=harrison,us&units=imperial
    
    Currently calling city number 988:
    argentan, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=argentan,fr&units=imperial
    
    Currently calling city number 989:
    port townsend, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=port townsend,us&units=imperial
    
    Currently calling city number 990:
    trincomalee, lk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=trincomalee,lk&units=imperial
    
    Currently calling city number 991:
    parintins, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=parintins,br&units=imperial
    
    Currently calling city number 992:
    cunha, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cunha,br&units=imperial
    
    Currently calling city number 993:
    abdanan, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=abdanan,ir&units=imperial
    
    Currently calling city number 994:
    sioux lookout, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sioux lookout,ca&units=imperial
    
    Currently calling city number 995:
    andenes, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=andenes,no&units=imperial
    
    Currently calling city number 996:
    bicester, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bicester,gb&units=imperial
    
    Currently calling city number 997:
    preobrazheniye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=preobrazheniye,ru&units=imperial
    
    Currently calling city number 998:
    fuerte olimpo, py
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fuerte olimpo,py&units=imperial
    
    Currently calling city number 999:
    astoria, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=astoria,us&units=imperial
    
    Currently calling city number 1000:
    danilov, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=danilov,ru&units=imperial
    
    Currently calling city number 1001:
    boyolangu, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=boyolangu,id&units=imperial
    
    Currently calling city number 1002:
    penzance, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=penzance,gb&units=imperial
    
    Currently calling city number 1003:
    ha tinh, vn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ha tinh,vn&units=imperial
    
    Currently calling city number 1004:
    biskupiec, pl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=biskupiec,pl&units=imperial
    
    Currently calling city number 1005:
    segou, ml
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=segou,ml&units=imperial
    
    Currently calling city number 1006:
    scarborough, tt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=scarborough,tt&units=imperial
    
    Currently calling city number 1007:
    ilula, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ilula,tz&units=imperial
    
    Currently calling city number 1008:
    colares, pt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=colares,pt&units=imperial
    
    Currently calling city number 1009:
    ferrol, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ferrol,es&units=imperial
    
    Currently calling city number 1010:
    camocim, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=camocim,br&units=imperial
    
    Currently calling city number 1011:
    vikindu, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vikindu,tz&units=imperial
    
    Currently calling city number 1012:
    sosnovo-ozerskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sosnovo-ozerskoye,ru&units=imperial
    
    Currently calling city number 1013:
    dalby, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dalby,au&units=imperial
    
    Currently calling city number 1014:
    novobiryusinskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=novobiryusinskiy,ru&units=imperial
    
    Currently calling city number 1015:
    vicuna, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vicuna,cl&units=imperial
    
    Currently calling city number 1016:
    wukari, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wukari,ng&units=imperial
    
    Currently calling city number 1017:
    salto, uy
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=salto,uy&units=imperial
    
    Currently calling city number 1018:
    benguela, ao
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=benguela,ao&units=imperial
    
    Currently calling city number 1019:
    rocky mount, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rocky mount,us&units=imperial
    
    Currently calling city number 1020:
    ongandjera, na
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ongandjera,na&units=imperial
    
    Currently calling city number 1021:
    porto novo, cv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=porto novo,cv&units=imperial
    
    Currently calling city number 1022:
    mazara del vallo, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mazara del vallo,it&units=imperial
    
    Currently calling city number 1023:
    gari, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gari,ru&units=imperial
    
    Currently calling city number 1024:
    mandan, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mandan,us&units=imperial
    
    Currently calling city number 1025:
    suntar, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=suntar,ru&units=imperial
    
    Currently calling city number 1026:
    madarounfa, ne
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=madarounfa,ne&units=imperial
    
    Currently calling city number 1027:
    loveland, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=loveland,us&units=imperial
    
    Currently calling city number 1028:
    bloomfield, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bloomfield,us&units=imperial
    
    Currently calling city number 1029:
    kazanka, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kazanka,ua&units=imperial
    
    Currently calling city number 1030:
    talaya, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=talaya,ru&units=imperial
    
    Currently calling city number 1031:
    beloha, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=beloha,mg&units=imperial
    
    Currently calling city number 1032:
    sangar, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sangar,ru&units=imperial
    
    Currently calling city number 1033:
    yangjiang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yangjiang,cn&units=imperial
    
    Currently calling city number 1034:
    creel, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=creel,mx&units=imperial
    
    Currently calling city number 1035:
    sol-iletsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sol-iletsk,ru&units=imperial
    
    Currently calling city number 1036:
    salinas, ec
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=salinas,ec&units=imperial
    
    Currently calling city number 1037:
    wadi musa, jo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wadi musa,jo&units=imperial
    
    Currently calling city number 1038:
    muriti, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=muriti,tz&units=imperial
    
    Currently calling city number 1039:
    zaysan, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zaysan,kz&units=imperial
    
    Currently calling city number 1040:
    ales, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ales,fr&units=imperial
    
    Currently calling city number 1041:
    orlik, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=orlik,ru&units=imperial
    
    Currently calling city number 1042:
    annau, tm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=annau,tm&units=imperial
    
    Currently calling city number 1043:
    iquique, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=iquique,cl&units=imperial
    
    Currently calling city number 1044:
    ordu, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ordu,tr&units=imperial
    
    Currently calling city number 1045:
    craig, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=craig,us&units=imperial
    
    Currently calling city number 1046:
    rovenki, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rovenki,ru&units=imperial
    
    Currently calling city number 1047:
    rockland, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rockland,us&units=imperial
    
    Currently calling city number 1048:
    korla, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=korla,cn&units=imperial
    
    Currently calling city number 1049:
    ukiah, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ukiah,us&units=imperial
    
    Currently calling city number 1050:
    necochea, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=necochea,ar&units=imperial
    
    Currently calling city number 1051:
    sovetskaya gavan, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sovetskaya gavan,ru&units=imperial
    
    Currently calling city number 1052:
    mayumba, ga
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mayumba,ga&units=imperial
    
    Currently calling city number 1053:
    basco, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=basco,ph&units=imperial
    
    Currently calling city number 1054:
    keti bandar, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=keti bandar,pk&units=imperial
    
    Currently calling city number 1055:
    santa marta, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santa marta,co&units=imperial
    
    Currently calling city number 1056:
    guiratinga, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=guiratinga,br&units=imperial
    
    Currently calling city number 1057:
    ureki, ge
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ureki,ge&units=imperial
    
    Currently calling city number 1058:
    kinablangan, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kinablangan,ph&units=imperial
    
    Currently calling city number 1059:
    salina cruz, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=salina cruz,mx&units=imperial
    
    Currently calling city number 1060:
    oranjestad, aw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=oranjestad,aw&units=imperial
    
    Currently calling city number 1061:
    nevel, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nevel,ru&units=imperial
    
    Currently calling city number 1062:
    fagernes, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fagernes,no&units=imperial
    
    Currently calling city number 1063:
    baturaja, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=baturaja,id&units=imperial
    
    Currently calling city number 1064:
    urambo, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=urambo,tz&units=imperial
    
    Currently calling city number 1065:
    pyshma, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pyshma,ru&units=imperial
    
    Currently calling city number 1066:
    kotma, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kotma,in&units=imperial
    
    Currently calling city number 1067:
    hun, ly
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hun,ly&units=imperial
    
    Currently calling city number 1068:
    madang, pg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=madang,pg&units=imperial
    
    Currently calling city number 1069:
    karpathos, gr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=karpathos,gr&units=imperial
    
    Currently calling city number 1070:
    erenhot, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=erenhot,cn&units=imperial
    
    Currently calling city number 1071:
    miandrivazo, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=miandrivazo,mg&units=imperial
    
    Currently calling city number 1072:
    santiago, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santiago,pe&units=imperial
    
    Currently calling city number 1073:
    uryupinsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=uryupinsk,ru&units=imperial
    
    Currently calling city number 1074:
    tra vinh, vn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tra vinh,vn&units=imperial
    
    Currently calling city number 1075:
    pangody, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pangody,ru&units=imperial
    
    Currently calling city number 1076:
    almaznyy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=almaznyy,ru&units=imperial
    
    Currently calling city number 1077:
    yangliuqing, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yangliuqing,cn&units=imperial
    
    Currently calling city number 1078:
    slave lake, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=slave lake,ca&units=imperial
    
    Currently calling city number 1079:
    llangefni, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=llangefni,gb&units=imperial
    
    Currently calling city number 1080:
    cortez, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cortez,us&units=imperial
    
    Currently calling city number 1081:
    sattahip, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sattahip,th&units=imperial
    
    Currently calling city number 1082:
    kasongo-lunda, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kasongo-lunda,cd&units=imperial
    
    Currently calling city number 1083:
    putina, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=putina,pe&units=imperial
    
    Currently calling city number 1084:
    gimli, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gimli,ca&units=imperial
    
    Currently calling city number 1085:
    ndele, cf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ndele,cf&units=imperial
    
    Currently calling city number 1086:
    ust-nera, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ust-nera,ru&units=imperial
    
    Currently calling city number 1087:
    selma, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=selma,us&units=imperial
    
    Currently calling city number 1088:
    jati, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jati,id&units=imperial
    
    Currently calling city number 1089:
    moron, mn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=moron,mn&units=imperial
    
    Currently calling city number 1090:
    kareli, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kareli,in&units=imperial
    
    Currently calling city number 1091:
    lebedyan, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lebedyan,ru&units=imperial
    
    Currently calling city number 1092:
    polessk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=polessk,ru&units=imperial
    
    Currently calling city number 1093:
    trairi, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=trairi,br&units=imperial
    
    Currently calling city number 1094:
    namatanai, pg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=namatanai,pg&units=imperial
    
    Currently calling city number 1095:
    nivala, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nivala,fi&units=imperial
    
    Currently calling city number 1096:
    great yarmouth, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=great yarmouth,gb&units=imperial
    
    Currently calling city number 1097:
    quelimane, mz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=quelimane,mz&units=imperial
    
    Currently calling city number 1098:
    durant, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=durant,us&units=imperial
    
    Currently calling city number 1099:
    marsh harbour, bs
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=marsh harbour,bs&units=imperial
    
    Currently calling city number 1100:
    esmeraldas, ec
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=esmeraldas,ec&units=imperial
    
    Currently calling city number 1101:
    areia branca, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=areia branca,br&units=imperial
    
    Currently calling city number 1102:
    derbent, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=derbent,ru&units=imperial
    
    Currently calling city number 1103:
    gulu, ug
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gulu,ug&units=imperial
    
    Currently calling city number 1104:
    itoman, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=itoman,jp&units=imperial
    
    Currently calling city number 1105:
    yenagoa, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yenagoa,ng&units=imperial
    
    Currently calling city number 1106:
    weinan, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=weinan,cn&units=imperial
    
    Currently calling city number 1107:
    rumonge, bi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rumonge,bi&units=imperial
    
    Currently calling city number 1108:
    hami, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hami,cn&units=imperial
    
    Currently calling city number 1109:
    kargopol, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kargopol,ru&units=imperial
    
    Currently calling city number 1110:
    port-cartier, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=port-cartier,ca&units=imperial
    
    Currently calling city number 1111:
    ayagoz, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ayagoz,kz&units=imperial
    
    Currently calling city number 1112:
    rongai, ke
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rongai,ke&units=imperial
    
    Currently calling city number 1113:
    diapaga, bf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=diapaga,bf&units=imperial
    
    Currently calling city number 1114:
    pochutla, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pochutla,mx&units=imperial
    
    Currently calling city number 1115:
    azangaro, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=azangaro,pe&units=imperial
    
    Currently calling city number 1116:
    caramoran, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=caramoran,ph&units=imperial
    
    Currently calling city number 1117:
    atherton, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=atherton,au&units=imperial
    
    Currently calling city number 1118:
    mayor pablo lagerenza, py
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mayor pablo lagerenza,py&units=imperial
    
    Currently calling city number 1119:
    juneau, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=juneau,us&units=imperial
    
    Currently calling city number 1120:
    koscierzyna, pl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=koscierzyna,pl&units=imperial
    
    Currently calling city number 1121:
    maykor, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=maykor,ru&units=imperial
    
    Currently calling city number 1122:
    lipari, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lipari,it&units=imperial
    
    Currently calling city number 1123:
    cacequi, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cacequi,br&units=imperial
    
    Currently calling city number 1124:
    winnemucca, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=winnemucca,us&units=imperial
    
    Currently calling city number 1125:
    staryy nadym, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=staryy nadym,ru&units=imperial
    
    Currently calling city number 1126:
    vidim, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vidim,ru&units=imperial
    
    Currently calling city number 1127:
    soe, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=soe,id&units=imperial
    
    Currently calling city number 1128:
    gallup, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gallup,us&units=imperial
    
    Currently calling city number 1129:
    ostersund, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ostersund,se&units=imperial
    
    Currently calling city number 1130:
    mareeba, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mareeba,au&units=imperial
    
    Currently calling city number 1131:
    ninh binh, vn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ninh binh,vn&units=imperial
    
    Currently calling city number 1132:
    vedeno, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vedeno,ru&units=imperial
    
    Currently calling city number 1133:
    nadadores, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nadadores,mx&units=imperial
    
    Currently calling city number 1134:
    ulaanbaatar, mn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ulaanbaatar,mn&units=imperial
    
    Currently calling city number 1135:
    magistralnyy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=magistralnyy,ru&units=imperial
    
    Currently calling city number 1136:
    kembe, cf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kembe,cf&units=imperial
    
    Currently calling city number 1137:
    fushe-arrez, al
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fushe-arrez,al&units=imperial
    
    Currently calling city number 1138:
    itaituba, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=itaituba,br&units=imperial
    
    Currently calling city number 1139:
    toora-khem, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=toora-khem,ru&units=imperial
    
    Currently calling city number 1140:
    ponta do sol, pt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ponta do sol,pt&units=imperial
    
    Currently calling city number 1141:
    melfi, td
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=melfi,td&units=imperial
    
    Currently calling city number 1142:
    oktyabrskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=oktyabrskiy,ru&units=imperial
    
    Currently calling city number 1143:
    zdvinsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zdvinsk,ru&units=imperial
    
    Currently calling city number 1144:
    san jose, gt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san jose,gt&units=imperial
    
    Currently calling city number 1145:
    kaoma, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kaoma,zm&units=imperial
    
    Currently calling city number 1146:
    sumbe, ao
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sumbe,ao&units=imperial
    
    Currently calling city number 1147:
    methoni, gr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=methoni,gr&units=imperial
    
    Currently calling city number 1148:
    wanning, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wanning,cn&units=imperial
    
    Currently calling city number 1149:
    moa, cu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=moa,cu&units=imperial
    
    Currently calling city number 1150:
    isla mujeres, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=isla mujeres,mx&units=imperial
    
    Currently calling city number 1151:
    norsup, vu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=norsup,vu&units=imperial
    
    Currently calling city number 1152:
    tsaratanana, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tsaratanana,mg&units=imperial
    
    Currently calling city number 1153:
    sibu, my
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sibu,my&units=imperial
    
    Currently calling city number 1154:
    fethiye, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fethiye,tr&units=imperial
    
    Currently calling city number 1155:
    bunbury, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bunbury,au&units=imperial
    
    Currently calling city number 1156:
    aripuana, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aripuana,br&units=imperial
    
    Currently calling city number 1157:
    hammerfest, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hammerfest,no&units=imperial
    
    Currently calling city number 1158:
    vasilyevskiy mokh, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vasilyevskiy mokh,ru&units=imperial
    
    Currently calling city number 1159:
    carauari, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=carauari,br&units=imperial
    
    Currently calling city number 1160:
    curaca, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=curaca,br&units=imperial
    
    Currently calling city number 1161:
    zhicheng, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zhicheng,cn&units=imperial
    
    Currently calling city number 1162:
    palauig, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=palauig,ph&units=imperial
    
    Currently calling city number 1163:
    kidal, ml
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kidal,ml&units=imperial
    
    Currently calling city number 1164:
    boundiali, ci
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=boundiali,ci&units=imperial
    
    Currently calling city number 1165:
    nador, ma
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nador,ma&units=imperial
    
    Currently calling city number 1166:
    ozark, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ozark,us&units=imperial
    
    Currently calling city number 1167:
    balkanabat, tm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=balkanabat,tm&units=imperial
    
    Currently calling city number 1168:
    lovozero, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lovozero,ru&units=imperial
    
    Currently calling city number 1169:
    lafia, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lafia,ng&units=imperial
    
    Currently calling city number 1170:
    lapua, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lapua,fi&units=imperial
    
    Currently calling city number 1171:
    sola, vu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sola,vu&units=imperial
    
    Currently calling city number 1172:
    taguatinga, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=taguatinga,br&units=imperial
    
    Currently calling city number 1173:
    gobo, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gobo,jp&units=imperial
    
    Currently calling city number 1174:
    champerico, gt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=champerico,gt&units=imperial
    
    Currently calling city number 1175:
    nicolas bravo, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nicolas bravo,mx&units=imperial
    
    Currently calling city number 1176:
    qeshm, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=qeshm,ir&units=imperial
    
    Currently calling city number 1177:
    ca mau, vn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ca mau,vn&units=imperial
    
    Currently calling city number 1178:
    ragama, lk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ragama,lk&units=imperial
    
    Currently calling city number 1179:
    don benito, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=don benito,es&units=imperial
    
    Currently calling city number 1180:
    corowa, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=corowa,au&units=imperial
    
    Currently calling city number 1181:
    grand gaube, mu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=grand gaube,mu&units=imperial
    
    Currently calling city number 1182:
    orotukan, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=orotukan,ru&units=imperial
    
    Currently calling city number 1183:
    shancheng, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shancheng,cn&units=imperial
    
    Currently calling city number 1184:
    celestun, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=celestun,mx&units=imperial
    
    Currently calling city number 1185:
    keita, ne
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=keita,ne&units=imperial
    
    Currently calling city number 1186:
    jiangyou, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jiangyou,cn&units=imperial
    
    Currently calling city number 1187:
    cilegon, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cilegon,id&units=imperial
    
    Currently calling city number 1188:
    saint-pierre, re
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saint-pierre,re&units=imperial
    
    Currently calling city number 1189:
    willowmore, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=willowmore,za&units=imperial
    
    Currently calling city number 1190:
    kortkeros, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kortkeros,ru&units=imperial
    
    Currently calling city number 1191:
    barranca, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=barranca,pe&units=imperial
    
    Currently calling city number 1192:
    gorin, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gorin,ru&units=imperial
    
    Currently calling city number 1193:
    dekar, bw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dekar,bw&units=imperial
    
    Currently calling city number 1194:
    luwuk, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=luwuk,id&units=imperial
    
    Currently calling city number 1195:
    melo, uy
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=melo,uy&units=imperial
    
    Currently calling city number 1196:
    kharp, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kharp,ru&units=imperial
    
    Currently calling city number 1197:
    litovko, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=litovko,ru&units=imperial
    
    Currently calling city number 1198:
    benidorm, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=benidorm,es&units=imperial
    
    Currently calling city number 1199:
    yeppoon, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yeppoon,au&units=imperial
    
    Currently calling city number 1200:
    constitucion, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=constitucion,cl&units=imperial
    
    Currently calling city number 1201:
    tomatlan, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tomatlan,mx&units=imperial
    
    Currently calling city number 1202:
    berdigestyakh, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=berdigestyakh,ru&units=imperial
    
    Currently calling city number 1203:
    tomelloso, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tomelloso,es&units=imperial
    
    Currently calling city number 1204:
    palmer, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=palmer,us&units=imperial
    
    Currently calling city number 1205:
    skelleftea, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=skelleftea,se&units=imperial
    
    Currently calling city number 1206:
    dondo, mz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dondo,mz&units=imperial
    
    Currently calling city number 1207:
    sinop, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sinop,tr&units=imperial
    
    Currently calling city number 1208:
    inongo, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=inongo,cd&units=imperial
    
    Currently calling city number 1209:
    abadan, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=abadan,ir&units=imperial
    
    Currently calling city number 1210:
    marrakesh, ma
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=marrakesh,ma&units=imperial
    
    Currently calling city number 1211:
    olean, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=olean,us&units=imperial
    
    Currently calling city number 1212:
    foumban, cm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=foumban,cm&units=imperial
    
    Currently calling city number 1213:
    goundi, td
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=goundi,td&units=imperial
    
    Currently calling city number 1214:
    luang prabang, la
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=luang prabang,la&units=imperial
    
    Currently calling city number 1215:
    cran-gevrier, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cran-gevrier,fr&units=imperial
    
    Currently calling city number 1216:
    petatlan, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=petatlan,mx&units=imperial
    
    Currently calling city number 1217:
    ucluelet, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ucluelet,ca&units=imperial
    
    Currently calling city number 1218:
    impfondo, cg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=impfondo,cg&units=imperial
    
    Currently calling city number 1219:
    morgan city, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=morgan city,us&units=imperial
    
    Currently calling city number 1220:
    sarkand, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sarkand,kz&units=imperial
    
    Currently calling city number 1221:
    novopokrovka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=novopokrovka,ru&units=imperial
    
    Currently calling city number 1222:
    north bend, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=north bend,us&units=imperial
    
    Currently calling city number 1223:
    udachnyy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=udachnyy,ru&units=imperial
    
    Currently calling city number 1224:
    safranbolu, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=safranbolu,tr&units=imperial
    
    Currently calling city number 1225:
    shima, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shima,cn&units=imperial
    
    Currently calling city number 1226:
    ballina, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ballina,au&units=imperial
    
    Currently calling city number 1227:
    aleksandrovka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aleksandrovka,ru&units=imperial
    
    Currently calling city number 1228:
    senj, hr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=senj,hr&units=imperial
    
    Currently calling city number 1229:
    aquiraz, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aquiraz,br&units=imperial
    
    Currently calling city number 1230:
    diplo, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=diplo,pk&units=imperial
    
    Currently calling city number 1231:
    south venice, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=south venice,us&units=imperial
    
    Currently calling city number 1232:
    garowe, so
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=garowe,so&units=imperial
    
    Currently calling city number 1233:
    khandyga, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=khandyga,ru&units=imperial
    
    Currently calling city number 1234:
    mansa, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mansa,zm&units=imperial
    
    Currently calling city number 1235:
    tandalti, sd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tandalti,sd&units=imperial
    
    Currently calling city number 1236:
    kabugao, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kabugao,ph&units=imperial
    
    Currently calling city number 1237:
    leh, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=leh,in&units=imperial
    
    Currently calling city number 1238:
    dumbraveni, ro
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dumbraveni,ro&units=imperial
    
    Currently calling city number 1239:
    la tuque, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=la tuque,ca&units=imperial
    
    Currently calling city number 1240:
    camapua, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=camapua,br&units=imperial
    
    Currently calling city number 1241:
    jurm, af
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jurm,af&units=imperial
    
    Currently calling city number 1242:
    deer lake, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=deer lake,ca&units=imperial
    
    Currently calling city number 1243:
    buta, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=buta,cd&units=imperial
    
    Currently calling city number 1244:
    shenjiamen, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shenjiamen,cn&units=imperial
    
    Currently calling city number 1245:
    ust-maya, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ust-maya,ru&units=imperial
    
    Currently calling city number 1246:
    elesbao veloso, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=elesbao veloso,br&units=imperial
    
    Currently calling city number 1247:
    shingu, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shingu,jp&units=imperial
    
    Currently calling city number 1248:
    mbekenyera, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mbekenyera,tz&units=imperial
    
    Currently calling city number 1249:
    dawlatabad, af
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dawlatabad,af&units=imperial
    
    Currently calling city number 1250:
    westport, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=westport,nz&units=imperial
    
    Currently calling city number 1251:
    zhanaozen, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zhanaozen,kz&units=imperial
    
    Currently calling city number 1252:
    puerto escondido, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=puerto escondido,co&units=imperial
    
    Currently calling city number 1253:
    teahupoo, pf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=teahupoo,pf&units=imperial
    
    Currently calling city number 1254:
    una, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=una,in&units=imperial
    
    Currently calling city number 1255:
    udimskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=udimskiy,ru&units=imperial
    
    Currently calling city number 1256:
    karasjok, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=karasjok,no&units=imperial
    
    Currently calling city number 1257:
    bubaque, gw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bubaque,gw&units=imperial
    
    Currently calling city number 1258:
    niigata, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=niigata,jp&units=imperial
    
    Currently calling city number 1259:
    bitung, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bitung,id&units=imperial
    
    Currently calling city number 1260:
    tadine, nc
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tadine,nc&units=imperial
    
    Currently calling city number 1261:
    tezu, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tezu,in&units=imperial
    
    Currently calling city number 1262:
    umm lajj, sa
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=umm lajj,sa&units=imperial
    
    Currently calling city number 1263:
    paciran, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=paciran,id&units=imperial
    
    Currently calling city number 1264:
    jaciara, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jaciara,br&units=imperial
    
    Currently calling city number 1265:
    teknaf, bd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=teknaf,bd&units=imperial
    
    Currently calling city number 1266:
    nueva gerona, cu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nueva gerona,cu&units=imperial
    
    Currently calling city number 1267:
    copiapo, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=copiapo,cl&units=imperial
    
    Currently calling city number 1268:
    innisfail, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=innisfail,au&units=imperial
    
    Currently calling city number 1269:
    garissa, ke
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=garissa,ke&units=imperial
    
    Currently calling city number 1270:
    fort morgan, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fort morgan,us&units=imperial
    
    Currently calling city number 1271:
    mega, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mega,et&units=imperial
    
    Currently calling city number 1272:
    luau, ao
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=luau,ao&units=imperial
    
    Currently calling city number 1273:
    greeneville, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=greeneville,us&units=imperial
    
    Currently calling city number 1274:
    yerofey pavlovich, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yerofey pavlovich,ru&units=imperial
    
    Currently calling city number 1275:
    la rioja, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=la rioja,ar&units=imperial
    
    Currently calling city number 1276:
    gornja koprivna, ba
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gornja koprivna,ba&units=imperial
    
    Currently calling city number 1277:
    mocuba, mz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mocuba,mz&units=imperial
    
    Currently calling city number 1278:
    baie-comeau, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=baie-comeau,ca&units=imperial
    
    Currently calling city number 1279:
    verkhoyansk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=verkhoyansk,ru&units=imperial
    
    Currently calling city number 1280:
    istisu, az
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=istisu,az&units=imperial
    
    Currently calling city number 1281:
    wenling, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wenling,cn&units=imperial
    
    Currently calling city number 1282:
    mao, td
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mao,td&units=imperial
    
    Currently calling city number 1283:
    zanzibar, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zanzibar,tz&units=imperial
    
    Currently calling city number 1284:
    kalabo, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kalabo,zm&units=imperial
    
    Currently calling city number 1285:
    bochil, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bochil,mx&units=imperial
    
    Currently calling city number 1286:
    najran, sa
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=najran,sa&units=imperial
    
    Currently calling city number 1287:
    porto santo, pt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=porto santo,pt&units=imperial
    
    Currently calling city number 1288:
    cerro cama, pa
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cerro cama,pa&units=imperial
    
    Currently calling city number 1289:
    inta, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=inta,ru&units=imperial
    
    Currently calling city number 1290:
    le port, re
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=le port,re&units=imperial
    
    Currently calling city number 1291:
    muroto, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=muroto,jp&units=imperial
    
    Currently calling city number 1292:
    cooma, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cooma,au&units=imperial
    
    Currently calling city number 1293:
    mattru, sl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mattru,sl&units=imperial
    
    Currently calling city number 1294:
    sun valley, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sun valley,us&units=imperial
    
    Currently calling city number 1295:
    pishin, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pishin,pk&units=imperial
    
    Currently calling city number 1296:
    sandwick, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sandwick,gb&units=imperial
    
    Currently calling city number 1297:
    weligama, lk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=weligama,lk&units=imperial
    
    Currently calling city number 1298:
    miram shah, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=miram shah,pk&units=imperial
    
    Currently calling city number 1299:
    houma, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=houma,us&units=imperial
    
    Currently calling city number 1300:
    half moon bay, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=half moon bay,us&units=imperial
    
    Currently calling city number 1301:
    chaoyang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chaoyang,cn&units=imperial
    
    Currently calling city number 1302:
    cockburn town, tc
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cockburn town,tc&units=imperial
    
    Currently calling city number 1303:
    bogatynia, pl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bogatynia,pl&units=imperial
    
    Currently calling city number 1304:
    beberibe, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=beberibe,br&units=imperial
    
    Currently calling city number 1305:
    awjilah, ly
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=awjilah,ly&units=imperial
    
    Currently calling city number 1306:
    smithers, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=smithers,ca&units=imperial
    
    Currently calling city number 1307:
    kabompo, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kabompo,zm&units=imperial
    
    Currently calling city number 1308:
    nangomba, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nangomba,tz&units=imperial
    
    Currently calling city number 1309:
    north battleford, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=north battleford,ca&units=imperial
    
    Currently calling city number 1310:
    atambua, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=atambua,id&units=imperial
    
    Currently calling city number 1311:
    veraval, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=veraval,in&units=imperial
    
    Currently calling city number 1312:
    limoux, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=limoux,fr&units=imperial
    
    Currently calling city number 1313:
    jorpeland, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jorpeland,no&units=imperial
    
    Currently calling city number 1314:
    ust-koksa, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ust-koksa,ru&units=imperial
    
    Currently calling city number 1315:
    kuryk, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kuryk,kz&units=imperial
    
    Currently calling city number 1316:
    rajo khanani, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rajo khanani,pk&units=imperial
    
    Currently calling city number 1317:
    katangli, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=katangli,ru&units=imperial
    
    Currently calling city number 1318:
    inhambane, mz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=inhambane,mz&units=imperial
    
    Currently calling city number 1319:
    badvel, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=badvel,in&units=imperial
    
    Currently calling city number 1320:
    bull savanna, jm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bull savanna,jm&units=imperial
    
    Currently calling city number 1321:
    yatou, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yatou,cn&units=imperial
    
    Currently calling city number 1322:
    kampene, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kampene,cd&units=imperial
    
    Currently calling city number 1323:
    kalaleh, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kalaleh,ir&units=imperial
    
    Currently calling city number 1324:
    santa rita, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santa rita,br&units=imperial
    
    Currently calling city number 1325:
    aljezur, pt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aljezur,pt&units=imperial
    
    Currently calling city number 1326:
    mizdah, ly
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mizdah,ly&units=imperial
    
    Currently calling city number 1327:
    mehran, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mehran,ir&units=imperial
    
    Currently calling city number 1328:
    nizhniy baskunchak, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nizhniy baskunchak,ru&units=imperial
    
    Currently calling city number 1329:
    blagoyevo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=blagoyevo,ru&units=imperial
    
    Currently calling city number 1330:
    alamos, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alamos,mx&units=imperial
    
    Currently calling city number 1331:
    great falls, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=great falls,us&units=imperial
    
    Currently calling city number 1332:
    bouafle, ci
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bouafle,ci&units=imperial
    
    Currently calling city number 1333:
    pontianak, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pontianak,id&units=imperial
    
    Currently calling city number 1334:
    burgersdorp, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=burgersdorp,za&units=imperial
    
    Currently calling city number 1335:
    goryachegorsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=goryachegorsk,ru&units=imperial
    
    Currently calling city number 1336:
    manhuacu, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=manhuacu,br&units=imperial
    
    Currently calling city number 1337:
    upata, ve
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=upata,ve&units=imperial
    
    Currently calling city number 1338:
    banjar, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=banjar,id&units=imperial
    
    Currently calling city number 1339:
    antalaha, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=antalaha,mg&units=imperial
    
    Currently calling city number 1340:
    sungairaya, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sungairaya,id&units=imperial
    
    Currently calling city number 1341:
    boffa, gn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=boffa,gn&units=imperial
    
    Currently calling city number 1342:
    dakar, sn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dakar,sn&units=imperial
    
    Currently calling city number 1343:
    dvinskoy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dvinskoy,ru&units=imperial
    
    Currently calling city number 1344:
    corinto, ni
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=corinto,ni&units=imperial
    
    Currently calling city number 1345:
    ivnya, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ivnya,ru&units=imperial
    
    Currently calling city number 1346:
    rochefort, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rochefort,fr&units=imperial
    
    Currently calling city number 1347:
    cardston, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cardston,ca&units=imperial
    
    Currently calling city number 1348:
    upington, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=upington,za&units=imperial
    
    Currently calling city number 1349:
    saint-ambroise, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saint-ambroise,ca&units=imperial
    
    Currently calling city number 1350:
    baiao, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=baiao,br&units=imperial
    
    Currently calling city number 1351:
    sumbawanga, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sumbawanga,tz&units=imperial
    
    Currently calling city number 1352:
    kitimat, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kitimat,ca&units=imperial
    
    Currently calling city number 1353:
    gewane, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gewane,et&units=imperial
    
    Currently calling city number 1354:
    port pirie, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=port pirie,au&units=imperial
    
    Currently calling city number 1355:
    redlands, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=redlands,us&units=imperial
    
    Currently calling city number 1356:
    kombissiri, bf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kombissiri,bf&units=imperial
    
    Currently calling city number 1357:
    voznesenye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=voznesenye,ru&units=imperial
    
    Currently calling city number 1358:
    plast, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=plast,ru&units=imperial
    
    Currently calling city number 1359:
    bakhmach, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bakhmach,ua&units=imperial
    
    Currently calling city number 1360:
    rio bravo, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rio bravo,mx&units=imperial
    
    Currently calling city number 1361:
    pemba, mz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pemba,mz&units=imperial
    
    Currently calling city number 1362:
    goundam, ml
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=goundam,ml&units=imperial
    
    Currently calling city number 1363:
    teguise, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=teguise,es&units=imperial
    
    Currently calling city number 1364:
    kuusamo, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kuusamo,fi&units=imperial
    
    Currently calling city number 1365:
    hay river, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hay river,ca&units=imperial
    
    Currently calling city number 1366:
    birao, cf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=birao,cf&units=imperial
    
    Currently calling city number 1367:
    dodge city, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dodge city,us&units=imperial
    
    Currently calling city number 1368:
    tipp city, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tipp city,us&units=imperial
    
    Currently calling city number 1369:
    neumunster, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=neumunster,de&units=imperial
    
    Currently calling city number 1370:
    zhuhai, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zhuhai,cn&units=imperial
    
    Currently calling city number 1371:
    menongue, ao
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=menongue,ao&units=imperial
    
    Currently calling city number 1372:
    arlit, ne
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=arlit,ne&units=imperial
    
    Currently calling city number 1373:
    terra roxa, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=terra roxa,br&units=imperial
    
    Currently calling city number 1374:
    dicabisagan, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dicabisagan,ph&units=imperial
    
    Currently calling city number 1375:
    neyshabur, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=neyshabur,ir&units=imperial
    
    Currently calling city number 1376:
    kosa, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kosa,ru&units=imperial
    
    Currently calling city number 1377:
    tahlequah, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tahlequah,us&units=imperial
    
    Currently calling city number 1378:
    aguas belas, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aguas belas,br&units=imperial
    
    Currently calling city number 1379:
    kedougou, sn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kedougou,sn&units=imperial
    
    Currently calling city number 1380:
    ancona, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ancona,it&units=imperial
    
    Currently calling city number 1381:
    obo, cf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=obo,cf&units=imperial
    
    Currently calling city number 1382:
    malkara, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=malkara,tr&units=imperial
    
    Currently calling city number 1383:
    ionia, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ionia,us&units=imperial
    
    Currently calling city number 1384:
    tiarei, pf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tiarei,pf&units=imperial
    
    Currently calling city number 1385:
    mandera, ke
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mandera,ke&units=imperial
    
    Currently calling city number 1386:
    quimper, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=quimper,fr&units=imperial
    
    Currently calling city number 1387:
    kangasala, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kangasala,fi&units=imperial
    
    Currently calling city number 1388:
    paramonga, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=paramonga,pe&units=imperial
    
    Currently calling city number 1389:
    zhigalovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zhigalovo,ru&units=imperial
    
    Currently calling city number 1390:
    talakan, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=talakan,ru&units=imperial
    
    Currently calling city number 1391:
    ntungamo, ug
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ntungamo,ug&units=imperial
    
    Currently calling city number 1392:
    shieli, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shieli,kz&units=imperial
    
    Currently calling city number 1393:
    jiuquan, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jiuquan,cn&units=imperial
    
    Currently calling city number 1394:
    treinta y tres, uy
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=treinta y tres,uy&units=imperial
    
    Currently calling city number 1395:
    crotone, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=crotone,it&units=imperial
    
    Currently calling city number 1396:
    shetpe, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shetpe,kz&units=imperial
    
    Currently calling city number 1397:
    urusha, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=urusha,ru&units=imperial
    
    Currently calling city number 1398:
    porbandar, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=porbandar,in&units=imperial
    
    Currently calling city number 1399:
    tamiahua, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tamiahua,mx&units=imperial
    
    Currently calling city number 1400:
    sydney, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sydney,au&units=imperial
    
    Currently calling city number 1401:
    polunochnoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=polunochnoye,ru&units=imperial
    
    Currently calling city number 1402:
    sorong, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sorong,id&units=imperial
    
    Currently calling city number 1403:
    miri, my
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=miri,my&units=imperial
    
    Currently calling city number 1404:
    pontivy, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pontivy,fr&units=imperial
    
    Currently calling city number 1405:
    dutse, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dutse,ng&units=imperial
    
    Currently calling city number 1406:
    plouzane, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=plouzane,fr&units=imperial
    
    Currently calling city number 1407:
    lhokseumawe, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lhokseumawe,id&units=imperial
    
    Currently calling city number 1408:
    russellville, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=russellville,us&units=imperial
    
    Currently calling city number 1409:
    gravdal, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gravdal,no&units=imperial
    
    Currently calling city number 1410:
    aguai, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aguai,br&units=imperial
    
    Currently calling city number 1411:
    itapirapua, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=itapirapua,br&units=imperial
    
    Currently calling city number 1412:
    muravlenko, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=muravlenko,ru&units=imperial
    
    Currently calling city number 1413:
    victor harbor, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=victor harbor,au&units=imperial
    
    Currently calling city number 1414:
    svetlaya, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=svetlaya,ru&units=imperial
    
    Currently calling city number 1415:
    bougouni, ml
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bougouni,ml&units=imperial
    
    Currently calling city number 1416:
    toyooka, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=toyooka,jp&units=imperial
    
    Currently calling city number 1417:
    mhlume, sz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mhlume,sz&units=imperial
    
    Currently calling city number 1418:
    andujar, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=andujar,es&units=imperial
    
    Currently calling city number 1419:
    hudiksvall, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hudiksvall,se&units=imperial
    
    Currently calling city number 1420:
    kasane, bw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kasane,bw&units=imperial
    
    Currently calling city number 1421:
    concordia, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=concordia,ar&units=imperial
    
    Currently calling city number 1422:
    harindanga, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=harindanga,in&units=imperial
    
    Currently calling city number 1423:
    mihijam, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mihijam,in&units=imperial
    
    Currently calling city number 1424:
    rio do sul, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rio do sul,br&units=imperial
    
    Currently calling city number 1425:
    general pico, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=general pico,ar&units=imperial
    
    Currently calling city number 1426:
    zarubino, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zarubino,ru&units=imperial
    
    Currently calling city number 1427:
    tuapse, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tuapse,ru&units=imperial
    
    Currently calling city number 1428:
    xunchang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=xunchang,cn&units=imperial
    
    Currently calling city number 1429:
    porterville, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=porterville,us&units=imperial
    
    Currently calling city number 1430:
    bail hongal, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bail hongal,in&units=imperial
    
    Currently calling city number 1431:
    singarayakonda, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=singarayakonda,in&units=imperial
    
    Currently calling city number 1432:
    igrim, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=igrim,ru&units=imperial
    
    Currently calling city number 1433:
    jinan, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jinan,cn&units=imperial
    
    Currently calling city number 1434:
    rabo de peixe, pt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rabo de peixe,pt&units=imperial
    
    Currently calling city number 1435:
    kulhudhuffushi, mv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kulhudhuffushi,mv&units=imperial
    
    Currently calling city number 1436:
    zipaquira, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zipaquira,co&units=imperial
    
    Currently calling city number 1437:
    ilo, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ilo,pe&units=imperial
    
    Currently calling city number 1438:
    beyneu, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=beyneu,kz&units=imperial
    
    Currently calling city number 1439:
    golden, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=golden,ca&units=imperial
    
    Currently calling city number 1440:
    praya, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=praya,id&units=imperial
    
    Currently calling city number 1441:
    grand island, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=grand island,us&units=imperial
    
    Currently calling city number 1442:
    san jeronimo, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san jeronimo,mx&units=imperial
    
    Currently calling city number 1443:
    mingshui, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mingshui,cn&units=imperial
    
    Currently calling city number 1444:
    winslow, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=winslow,us&units=imperial
    
    Currently calling city number 1445:
    prieska, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=prieska,za&units=imperial
    
    Currently calling city number 1446:
    mori, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mori,jp&units=imperial
    
    Currently calling city number 1447:
    borogontsy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=borogontsy,ru&units=imperial
    
    Currently calling city number 1448:
    tairua, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tairua,nz&units=imperial
    
    Currently calling city number 1449:
    sterling, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sterling,us&units=imperial
    
    Currently calling city number 1450:
    inyonga, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=inyonga,tz&units=imperial
    
    Currently calling city number 1451:
    lamu, ke
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lamu,ke&units=imperial
    
    Currently calling city number 1452:
    saint simons, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saint simons,us&units=imperial
    
    Currently calling city number 1453:
    kamaishi, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kamaishi,jp&units=imperial
    
    Currently calling city number 1454:
    maniitsoq, gl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=maniitsoq,gl&units=imperial
    
    Currently calling city number 1455:
    posadas, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=posadas,ar&units=imperial
    
    Currently calling city number 1456:
    oriximina, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=oriximina,br&units=imperial
    
    Currently calling city number 1457:
    sabathu, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sabathu,in&units=imperial
    
    Currently calling city number 1458:
    banda aceh, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=banda aceh,id&units=imperial
    
    Currently calling city number 1459:
    brigantine, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=brigantine,us&units=imperial
    
    Currently calling city number 1460:
    richmond, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=richmond,us&units=imperial
    
    Currently calling city number 1461:
    cumbernauld, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cumbernauld,gb&units=imperial
    
    Currently calling city number 1462:
    lae, pg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lae,pg&units=imperial
    
    Currently calling city number 1463:
    la baneza, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=la baneza,es&units=imperial
    
    Currently calling city number 1464:
    san ramon de la nueva oran, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san ramon de la nueva oran,ar&units=imperial
    
    Currently calling city number 1465:
    ndioum, sn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ndioum,sn&units=imperial
    
    Currently calling city number 1466:
    yaring, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yaring,th&units=imperial
    
    Currently calling city number 1467:
    peterborough, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=peterborough,us&units=imperial
    
    Currently calling city number 1468:
    thaba-tseka, ls
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=thaba-tseka,ls&units=imperial
    
    Currently calling city number 1469:
    mahajanga, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mahajanga,mg&units=imperial
    
    Currently calling city number 1470:
    lahaina, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lahaina,us&units=imperial
    
    Currently calling city number 1471:
    yangambi, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yangambi,cd&units=imperial
    
    Currently calling city number 1472:
    sassandra, ci
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sassandra,ci&units=imperial
    
    Currently calling city number 1473:
    leona vicario, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=leona vicario,mx&units=imperial
    
    Currently calling city number 1474:
    lai, td
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lai,td&units=imperial
    
    Currently calling city number 1475:
    dir, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dir,pk&units=imperial
    
    Currently calling city number 1476:
    hailar, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hailar,cn&units=imperial
    
    Currently calling city number 1477:
    hearst, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hearst,ca&units=imperial
    
    Currently calling city number 1478:
    pemberton, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pemberton,ca&units=imperial
    
    Currently calling city number 1479:
    ayame, ci
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ayame,ci&units=imperial
    
    Currently calling city number 1480:
    novoagansk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=novoagansk,ru&units=imperial
    
    Currently calling city number 1481:
    mirzapur, bd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mirzapur,bd&units=imperial
    
    Currently calling city number 1482:
    chisinau, md
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chisinau,md&units=imperial
    
    Currently calling city number 1483:
    williamsport, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=williamsport,us&units=imperial
    
    Currently calling city number 1484:
    havre, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=havre,us&units=imperial
    
    Currently calling city number 1485:
    anzhero-sudzhensk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=anzhero-sudzhensk,ru&units=imperial
    
    Currently calling city number 1486:
    tomohon, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tomohon,id&units=imperial
    
    Currently calling city number 1487:
    navahrudak, by
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=navahrudak,by&units=imperial
    
    Currently calling city number 1488:
    kalanwali, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kalanwali,in&units=imperial
    
    Currently calling city number 1489:
    cangucu, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cangucu,br&units=imperial
    
    Currently calling city number 1490:
    kurilsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kurilsk,ru&units=imperial
    
    Currently calling city number 1491:
    ust-omchug, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ust-omchug,ru&units=imperial
    
    Currently calling city number 1492:
    san-pedro, ci
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san-pedro,ci&units=imperial
    
    Currently calling city number 1493:
    peniche, pt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=peniche,pt&units=imperial
    
    Currently calling city number 1494:
    larvik, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=larvik,no&units=imperial
    
    Currently calling city number 1495:
    sept-iles, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sept-iles,ca&units=imperial
    
    Currently calling city number 1496:
    dalaba, gn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dalaba,gn&units=imperial
    
    Currently calling city number 1497:
    bani walid, ly
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bani walid,ly&units=imperial
    
    Currently calling city number 1498:
    mundo nuevo, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mundo nuevo,mx&units=imperial
    
    Currently calling city number 1499:
    sakaiminato, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sakaiminato,jp&units=imperial
    
    Currently calling city number 1500:
    qidong, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=qidong,cn&units=imperial
    
    Currently calling city number 1501:
    maine-soroa, ne
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=maine-soroa,ne&units=imperial
    
    Currently calling city number 1502:
    timizart, dz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=timizart,dz&units=imperial
    
    Currently calling city number 1503:
    wawa, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wawa,ca&units=imperial
    
    Currently calling city number 1504:
    taseyevo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=taseyevo,ru&units=imperial
    
    Currently calling city number 1505:
    senneterre, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=senneterre,ca&units=imperial
    
    Currently calling city number 1506:
    alihe, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alihe,cn&units=imperial
    
    Currently calling city number 1507:
    havelock, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=havelock,ca&units=imperial
    
    Currently calling city number 1508:
    brody, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=brody,ua&units=imperial
    
    Currently calling city number 1509:
    seoul, kr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=seoul,kr&units=imperial
    
    Currently calling city number 1510:
    san carlos, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san carlos,bo&units=imperial
    
    Currently calling city number 1511:
    tapejara, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tapejara,br&units=imperial
    
    Currently calling city number 1512:
    hudson bay, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hudson bay,ca&units=imperial
    
    Currently calling city number 1513:
    shakawe, bw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shakawe,bw&units=imperial
    
    Currently calling city number 1514:
    kenora, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kenora,ca&units=imperial
    
    Currently calling city number 1515:
    fastiv, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fastiv,ua&units=imperial
    
    Currently calling city number 1516:
    kuantan, my
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kuantan,my&units=imperial
    
    Currently calling city number 1517:
    gara, hu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gara,hu&units=imperial
    
    Currently calling city number 1518:
    cocorit, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cocorit,mx&units=imperial
    
    Currently calling city number 1519:
    ahraura, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ahraura,in&units=imperial
    
    Currently calling city number 1520:
    chornukhy, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chornukhy,ua&units=imperial
    
    Currently calling city number 1521:
    la orilla, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=la orilla,mx&units=imperial
    
    Currently calling city number 1522:
    jardim, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jardim,br&units=imperial
    
    Currently calling city number 1523:
    storforshei, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=storforshei,no&units=imperial
    
    Currently calling city number 1524:
    sinaloa, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sinaloa,mx&units=imperial
    
    Currently calling city number 1525:
    omsukchan, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=omsukchan,ru&units=imperial
    
    Currently calling city number 1526:
    baculin, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=baculin,ph&units=imperial
    
    Currently calling city number 1527:
    san juan de colon, ve
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san juan de colon,ve&units=imperial
    
    Currently calling city number 1528:
    tera, ne
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tera,ne&units=imperial
    
    Currently calling city number 1529:
    porto seguro, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=porto seguro,br&units=imperial
    
    Currently calling city number 1530:
    nizhnyaya tavda, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nizhnyaya tavda,ru&units=imperial
    
    Currently calling city number 1531:
    colonial heights, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=colonial heights,us&units=imperial
    
    Currently calling city number 1532:
    ajdabiya, ly
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ajdabiya,ly&units=imperial
    
    Currently calling city number 1533:
    samarai, pg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=samarai,pg&units=imperial
    
    Currently calling city number 1534:
    horta, pt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=horta,pt&units=imperial
    
    Currently calling city number 1535:
    allonnes, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=allonnes,fr&units=imperial
    
    Currently calling city number 1536:
    edd, er
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=edd,er&units=imperial
    
    Currently calling city number 1537:
    yacuiba, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yacuiba,bo&units=imperial
    
    Currently calling city number 1538:
    osypenko, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=osypenko,ua&units=imperial
    
    Currently calling city number 1539:
    layton, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=layton,us&units=imperial
    
    Currently calling city number 1540:
    boali, cf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=boali,cf&units=imperial
    
    Currently calling city number 1541:
    katrineholm, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=katrineholm,se&units=imperial
    
    Currently calling city number 1542:
    raton, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=raton,us&units=imperial
    
    Currently calling city number 1543:
    venice, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=venice,us&units=imperial
    
    Currently calling city number 1544:
    xuddur, so
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=xuddur,so&units=imperial
    
    Currently calling city number 1545:
    pyapon, mm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pyapon,mm&units=imperial
    
    Currently calling city number 1546:
    limbang, my
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=limbang,my&units=imperial
    
    Currently calling city number 1547:
    waterloo, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=waterloo,us&units=imperial
    
    Currently calling city number 1548:
    formosa do rio preto, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=formosa do rio preto,br&units=imperial
    
    Currently calling city number 1549:
    pahrump, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pahrump,us&units=imperial
    
    Currently calling city number 1550:
    abha, sa
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=abha,sa&units=imperial
    
    Currently calling city number 1551:
    gigmoto, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gigmoto,ph&units=imperial
    
    Currently calling city number 1552:
    simpang, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=simpang,id&units=imperial
    
    Currently calling city number 1553:
    goure, ne
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=goure,ne&units=imperial
    
    Currently calling city number 1554:
    gisborne, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gisborne,nz&units=imperial
    
    Currently calling city number 1555:
    artyom, az
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=artyom,az&units=imperial
    
    Currently calling city number 1556:
    coruripe, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=coruripe,br&units=imperial
    
    Currently calling city number 1557:
    moron, cu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=moron,cu&units=imperial
    
    Currently calling city number 1558:
    namtsy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=namtsy,ru&units=imperial
    
    Currently calling city number 1559:
    zhirnovsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zhirnovsk,ru&units=imperial
    
    Currently calling city number 1560:
    tongzi, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tongzi,cn&units=imperial
    
    Currently calling city number 1561:
    kariba, zw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kariba,zw&units=imperial
    
    Currently calling city number 1562:
    cremona, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cremona,it&units=imperial
    
    Currently calling city number 1563:
    belle fourche, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=belle fourche,us&units=imperial
    
    Currently calling city number 1564:
    wladyslawowo, pl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wladyslawowo,pl&units=imperial
    
    Currently calling city number 1565:
    kikwit, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kikwit,cd&units=imperial
    
    Currently calling city number 1566:
    godinesti, ro
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=godinesti,ro&units=imperial
    
    Currently calling city number 1567:
    karasburg, na
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=karasburg,na&units=imperial
    
    Currently calling city number 1568:
    roebourne, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=roebourne,au&units=imperial
    
    Currently calling city number 1569:
    fukue, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fukue,jp&units=imperial
    
    Currently calling city number 1570:
    temascalcingo, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=temascalcingo,mx&units=imperial
    
    Currently calling city number 1571:
    akot, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=akot,in&units=imperial
    
    Currently calling city number 1572:
    punta alta, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=punta alta,ar&units=imperial
    
    Currently calling city number 1573:
    auki, sb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=auki,sb&units=imperial
    
    Currently calling city number 1574:
    akhmim, eg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=akhmim,eg&units=imperial
    
    Currently calling city number 1575:
    tabas, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tabas,ir&units=imperial
    
    Currently calling city number 1576:
    rjukan, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rjukan,no&units=imperial
    
    Currently calling city number 1577:
    pasighat, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pasighat,in&units=imperial
    
    Currently calling city number 1578:
    maple creek, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=maple creek,ca&units=imperial
    
    Currently calling city number 1579:
    ulaangom, mn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ulaangom,mn&units=imperial
    
    Currently calling city number 1580:
    crawfordsville, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=crawfordsville,us&units=imperial
    
    Currently calling city number 1581:
    gornopravdinsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gornopravdinsk,ru&units=imperial
    
    Currently calling city number 1582:
    dali, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dali,cn&units=imperial
    
    Currently calling city number 1583:
    verkhoshizhemye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=verkhoshizhemye,ru&units=imperial
    
    Currently calling city number 1584:
    nurota, uz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nurota,uz&units=imperial
    
    Currently calling city number 1585:
    matagami, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=matagami,ca&units=imperial
    
    Currently calling city number 1586:
    portobelo, pa
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=portobelo,pa&units=imperial
    
    Currently calling city number 1587:
    kjopsvik, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kjopsvik,no&units=imperial
    
    Currently calling city number 1588:
    cortes, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cortes,ph&units=imperial
    
    Currently calling city number 1589:
    grenfell, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=grenfell,ca&units=imperial
    
    Currently calling city number 1590:
    ennis, ie
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ennis,ie&units=imperial
    
    Currently calling city number 1591:
    turukhansk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=turukhansk,ru&units=imperial
    
    Currently calling city number 1592:
    gryfice, pl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gryfice,pl&units=imperial
    
    Currently calling city number 1593:
    lamar, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lamar,us&units=imperial
    
    Currently calling city number 1594:
    wagar, sd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wagar,sd&units=imperial
    
    Currently calling city number 1595:
    pedernales, do
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pedernales,do&units=imperial
    
    Currently calling city number 1596:
    algodones, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=algodones,mx&units=imperial
    
    Currently calling city number 1597:
    azul, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=azul,ar&units=imperial
    
    Currently calling city number 1598:
    guderup, dk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=guderup,dk&units=imperial
    
    Currently calling city number 1599:
    nikolayevsk-na-amure, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nikolayevsk-na-amure,ru&units=imperial
    
    Currently calling city number 1600:
    mitzic, ga
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mitzic,ga&units=imperial
    
    Currently calling city number 1601:
    warrnambool, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=warrnambool,au&units=imperial
    
    Currently calling city number 1602:
    puerto madero, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=puerto madero,mx&units=imperial
    
    Currently calling city number 1603:
    novo aripuana, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=novo aripuana,br&units=imperial
    
    Currently calling city number 1604:
    damghan, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=damghan,ir&units=imperial
    
    Currently calling city number 1605:
    watsa, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=watsa,cd&units=imperial
    
    Currently calling city number 1606:
    antsohihy, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=antsohihy,mg&units=imperial
    
    Currently calling city number 1607:
    novolabinskaya, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=novolabinskaya,ru&units=imperial
    
    Currently calling city number 1608:
    elizabeth city, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=elizabeth city,us&units=imperial
    
    Currently calling city number 1609:
    abu dhabi, ae
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=abu dhabi,ae&units=imperial
    
    Currently calling city number 1610:
    astana, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=astana,kz&units=imperial
    
    Currently calling city number 1611:
    trapani, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=trapani,it&units=imperial
    
    Currently calling city number 1612:
    boone, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=boone,us&units=imperial
    
    Currently calling city number 1613:
    hagersville, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hagersville,ca&units=imperial
    
    Currently calling city number 1614:
    melivoia, gr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=melivoia,gr&units=imperial
    
    Currently calling city number 1615:
    waitati, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=waitati,nz&units=imperial
    
    Currently calling city number 1616:
    hennenman, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hennenman,za&units=imperial
    
    Currently calling city number 1617:
    kannauj, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kannauj,in&units=imperial
    
    Currently calling city number 1618:
    caconda, ao
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=caconda,ao&units=imperial
    
    Currently calling city number 1619:
    pipri, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pipri,in&units=imperial
    
    Currently calling city number 1620:
    panshi, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=panshi,cn&units=imperial
    
    Currently calling city number 1621:
    polson, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=polson,us&units=imperial
    
    Currently calling city number 1622:
    cuiluan, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cuiluan,cn&units=imperial
    
    Currently calling city number 1623:
    newcastle, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=newcastle,au&units=imperial
    
    Currently calling city number 1624:
    santa rosa, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santa rosa,ar&units=imperial
    
    Currently calling city number 1625:
    bar harbor, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bar harbor,us&units=imperial
    
    Currently calling city number 1626:
    cap-aux-meules, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cap-aux-meules,ca&units=imperial
    
    Currently calling city number 1627:
    paka, my
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=paka,my&units=imperial
    
    Currently calling city number 1628:
    channel-port aux basques, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=channel-port aux basques,ca&units=imperial
    
    Currently calling city number 1629:
    saint-francois, gp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saint-francois,gp&units=imperial
    
    Currently calling city number 1630:
    khash, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=khash,ir&units=imperial
    
    Currently calling city number 1631:
    terrace bay, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=terrace bay,ca&units=imperial
    
    Currently calling city number 1632:
    linxia, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=linxia,cn&units=imperial
    
    Currently calling city number 1633:
    galeana, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=galeana,mx&units=imperial
    
    Currently calling city number 1634:
    hare bay, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hare bay,ca&units=imperial
    
    Currently calling city number 1635:
    mangaratiba, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mangaratiba,br&units=imperial
    
    Currently calling city number 1636:
    lemgo, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lemgo,de&units=imperial
    
    Currently calling city number 1637:
    moses lake, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=moses lake,us&units=imperial
    
    Currently calling city number 1638:
    suchitoto, sv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=suchitoto,sv&units=imperial
    
    Currently calling city number 1639:
    xichang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=xichang,cn&units=imperial
    
    Currently calling city number 1640:
    novokuznetsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=novokuznetsk,ru&units=imperial
    
    Currently calling city number 1641:
    taksimo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=taksimo,ru&units=imperial
    
    Currently calling city number 1642:
    panuco, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=panuco,mx&units=imperial
    
    Currently calling city number 1643:
    delta del tigre, uy
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=delta del tigre,uy&units=imperial
    
    Currently calling city number 1644:
    kuytun, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kuytun,ru&units=imperial
    
    Currently calling city number 1645:
    shihezi, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shihezi,cn&units=imperial
    
    Currently calling city number 1646:
    dmytrivka, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dmytrivka,ua&units=imperial
    
    Currently calling city number 1647:
    mzimba, mw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mzimba,mw&units=imperial
    
    Currently calling city number 1648:
    brewster, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=brewster,us&units=imperial
    
    Currently calling city number 1649:
    duldurga, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=duldurga,ru&units=imperial
    
    Currently calling city number 1650:
    athabasca, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=athabasca,ca&units=imperial
    
    Currently calling city number 1651:
    hamilton, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hamilton,us&units=imperial
    
    Currently calling city number 1652:
    nelson, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nelson,ca&units=imperial
    
    Currently calling city number 1653:
    carmelo, uy
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=carmelo,uy&units=imperial
    
    Currently calling city number 1654:
    fuling, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fuling,cn&units=imperial
    
    Currently calling city number 1655:
    karpogory, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=karpogory,ru&units=imperial
    
    Currently calling city number 1656:
    libreville, ga
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=libreville,ga&units=imperial
    
    Currently calling city number 1657:
    cessnock, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cessnock,au&units=imperial
    
    Currently calling city number 1658:
    bani, do
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bani,do&units=imperial
    
    Currently calling city number 1659:
    amantea, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=amantea,it&units=imperial
    
    Currently calling city number 1660:
    turan, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=turan,ru&units=imperial
    
    Currently calling city number 1661:
    charagua, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=charagua,bo&units=imperial
    
    Currently calling city number 1662:
    millinocket, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=millinocket,us&units=imperial
    
    Currently calling city number 1663:
    varkkallai, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=varkkallai,in&units=imperial
    
    Currently calling city number 1664:
    san jose, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san jose,ph&units=imperial
    
    Currently calling city number 1665:
    barinas, ve
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=barinas,ve&units=imperial
    
    Currently calling city number 1666:
    netrakona, bd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=netrakona,bd&units=imperial
    
    Currently calling city number 1667:
    general roca, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=general roca,ar&units=imperial
    
    Currently calling city number 1668:
    scarborough, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=scarborough,gb&units=imperial
    
    Currently calling city number 1669:
    torrington, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=torrington,us&units=imperial
    
    Currently calling city number 1670:
    soderhamn, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=soderhamn,se&units=imperial
    
    Currently calling city number 1671:
    lady lake, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lady lake,us&units=imperial
    
    Currently calling city number 1672:
    pteleos, gr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pteleos,gr&units=imperial
    
    Currently calling city number 1673:
    beisfjord, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=beisfjord,no&units=imperial
    
    Currently calling city number 1674:
    mombasa, ke
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mombasa,ke&units=imperial
    
    Currently calling city number 1675:
    thurso, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=thurso,gb&units=imperial
    
    Currently calling city number 1676:
    kloulklubed, pw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kloulklubed,pw&units=imperial
    
    Currently calling city number 1677:
    manado, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=manado,id&units=imperial
    
    Currently calling city number 1678:
    las tunas, cu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=las tunas,cu&units=imperial
    
    Currently calling city number 1679:
    sar-e pul, af
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sar-e pul,af&units=imperial
    
    Currently calling city number 1680:
    kropotkin, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kropotkin,ru&units=imperial
    
    Currently calling city number 1681:
    morros, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=morros,br&units=imperial
    
    Currently calling city number 1682:
    ust-ilimsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ust-ilimsk,ru&units=imperial
    
    Currently calling city number 1683:
    rio grande, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rio grande,br&units=imperial
    
    Currently calling city number 1684:
    peruibe, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=peruibe,br&units=imperial
    
    Currently calling city number 1685:
    praia, cv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=praia,cv&units=imperial
    
    Currently calling city number 1686:
    micheweni, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=micheweni,tz&units=imperial
    
    Currently calling city number 1687:
    nizhniy kuranakh, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nizhniy kuranakh,ru&units=imperial
    
    Currently calling city number 1688:
    voyinka, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=voyinka,ua&units=imperial
    
    Currently calling city number 1689:
    grants pass, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=grants pass,us&units=imperial
    
    Currently calling city number 1690:
    kemi, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kemi,fi&units=imperial
    
    Currently calling city number 1691:
    manuk mangkaw, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=manuk mangkaw,ph&units=imperial
    
    Currently calling city number 1692:
    anjar, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=anjar,in&units=imperial
    
    Currently calling city number 1693:
    kanker, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kanker,in&units=imperial
    
    Currently calling city number 1694:
    tanout, ne
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tanout,ne&units=imperial
    
    Currently calling city number 1695:
    bhandara, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bhandara,in&units=imperial
    
    Currently calling city number 1696:
    yuxia, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yuxia,cn&units=imperial
    
    Currently calling city number 1697:
    yara, cu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yara,cu&units=imperial
    
    Currently calling city number 1698:
    fallon, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fallon,us&units=imperial
    
    Currently calling city number 1699:
    ardahan, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ardahan,tr&units=imperial
    
    Currently calling city number 1700:
    timberlake, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=timberlake,us&units=imperial
    
    Currently calling city number 1701:
    jiwani, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jiwani,pk&units=imperial
    
    Currently calling city number 1702:
    neuquen, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=neuquen,ar&units=imperial
    
    Currently calling city number 1703:
    deep river, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=deep river,ca&units=imperial
    
    Currently calling city number 1704:
    campo maior, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=campo maior,br&units=imperial
    
    Currently calling city number 1705:
    kadungora, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kadungora,id&units=imperial
    
    Currently calling city number 1706:
    clinton, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=clinton,us&units=imperial
    
    Currently calling city number 1707:
    lingao, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lingao,cn&units=imperial
    
    Currently calling city number 1708:
    keningau, my
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=keningau,my&units=imperial
    
    Currently calling city number 1709:
    el tigre, ve
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=el tigre,ve&units=imperial
    
    Currently calling city number 1710:
    shawnee, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shawnee,us&units=imperial
    
    Currently calling city number 1711:
    aranos, na
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aranos,na&units=imperial
    
    Currently calling city number 1712:
    ewa beach, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ewa beach,us&units=imperial
    
    Currently calling city number 1713:
    kluczbork, pl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kluczbork,pl&units=imperial
    
    Currently calling city number 1714:
    marin, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=marin,mx&units=imperial
    
    Currently calling city number 1715:
    churapcha, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=churapcha,ru&units=imperial
    
    Currently calling city number 1716:
    genhe, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=genhe,cn&units=imperial
    
    Currently calling city number 1717:
    akropong, gh
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=akropong,gh&units=imperial
    
    Currently calling city number 1718:
    solvychegodsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=solvychegodsk,ru&units=imperial
    
    Currently calling city number 1719:
    jonesboro, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jonesboro,us&units=imperial
    
    Currently calling city number 1720:
    lumphat, kh
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lumphat,kh&units=imperial
    
    Currently calling city number 1721:
    sokolo, ml
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sokolo,ml&units=imperial
    
    Currently calling city number 1722:
    barreiras, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=barreiras,br&units=imperial
    
    Currently calling city number 1723:
    verkhnetulomskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=verkhnetulomskiy,ru&units=imperial
    
    Currently calling city number 1724:
    dzaoudzi, yt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dzaoudzi,yt&units=imperial
    
    Currently calling city number 1725:
    kijang, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kijang,id&units=imperial
    
    Currently calling city number 1726:
    toktogul, kg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=toktogul,kg&units=imperial
    
    Currently calling city number 1727:
    bafq, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bafq,ir&units=imperial
    
    Currently calling city number 1728:
    meridian, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=meridian,us&units=imperial
    
    Currently calling city number 1729:
    lensk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lensk,ru&units=imperial
    
    Currently calling city number 1730:
    sosnogorsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sosnogorsk,ru&units=imperial
    
    Currently calling city number 1731:
    ondjiva, ao
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ondjiva,ao&units=imperial
    
    Currently calling city number 1732:
    tougan, bf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tougan,bf&units=imperial
    
    Currently calling city number 1733:
    purpe, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=purpe,ru&units=imperial
    
    Currently calling city number 1734:
    miami, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=miami,us&units=imperial
    
    Currently calling city number 1735:
    desbiens, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=desbiens,ca&units=imperial
    
    Currently calling city number 1736:
    barstow, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=barstow,us&units=imperial
    
    Currently calling city number 1737:
    belaya gora, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=belaya gora,ru&units=imperial
    
    Currently calling city number 1738:
    lowicz, pl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lowicz,pl&units=imperial
    
    Currently calling city number 1739:
    puerto del rosario, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=puerto del rosario,es&units=imperial
    
    Currently calling city number 1740:
    jenison, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jenison,us&units=imperial
    
    Currently calling city number 1741:
    phan thiet, vn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=phan thiet,vn&units=imperial
    
    Currently calling city number 1742:
    fernley, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fernley,us&units=imperial
    
    Currently calling city number 1743:
    glasgow, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=glasgow,us&units=imperial
    
    Currently calling city number 1744:
    shouguang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shouguang,cn&units=imperial
    
    Currently calling city number 1745:
    melbourne, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=melbourne,au&units=imperial
    
    Currently calling city number 1746:
    durban, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=durban,za&units=imperial
    
    Currently calling city number 1747:
    boca do acre, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=boca do acre,br&units=imperial
    
    Currently calling city number 1748:
    muzhi, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=muzhi,ru&units=imperial
    
    Currently calling city number 1749:
    vallenar, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vallenar,cl&units=imperial
    
    Currently calling city number 1750:
    jiazi, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jiazi,cn&units=imperial
    
    Currently calling city number 1751:
    nhulunbuy, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nhulunbuy,au&units=imperial
    
    Currently calling city number 1752:
    yantal, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yantal,ru&units=imperial
    
    Currently calling city number 1753:
    nautla, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nautla,mx&units=imperial
    
    Currently calling city number 1754:
    johnstown, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=johnstown,us&units=imperial
    
    Currently calling city number 1755:
    kipini, ke
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kipini,ke&units=imperial
    
    Currently calling city number 1756:
    zhezkazgan, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zhezkazgan,kz&units=imperial
    
    Currently calling city number 1757:
    chegutu, zw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chegutu,zw&units=imperial
    
    Currently calling city number 1758:
    yelovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yelovo,ru&units=imperial
    
    Currently calling city number 1759:
    lamtah, tn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lamtah,tn&units=imperial
    
    Currently calling city number 1760:
    rio verde de mato grosso, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rio verde de mato grosso,br&units=imperial
    
    Currently calling city number 1761:
    burnie, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=burnie,au&units=imperial
    
    Currently calling city number 1762:
    tokonou, gn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tokonou,gn&units=imperial
    
    Currently calling city number 1763:
    reconquista, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=reconquista,ar&units=imperial
    
    Currently calling city number 1764:
    longhua, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=longhua,cn&units=imperial
    
    Currently calling city number 1765:
    kurush, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kurush,ru&units=imperial
    
    Currently calling city number 1766:
    corum, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=corum,tr&units=imperial
    
    Currently calling city number 1767:
    kawalu, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kawalu,id&units=imperial
    
    Currently calling city number 1768:
    mincivan, az
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mincivan,az&units=imperial
    
    Currently calling city number 1769:
    heihe, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=heihe,cn&units=imperial
    
    Currently calling city number 1770:
    poletayevo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=poletayevo,ru&units=imperial
    
    Currently calling city number 1771:
    phetchaburi, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=phetchaburi,th&units=imperial
    
    Currently calling city number 1772:
    emerald, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=emerald,au&units=imperial
    
    Currently calling city number 1773:
    beatrice, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=beatrice,us&units=imperial
    
    Currently calling city number 1774:
    west wendover, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=west wendover,us&units=imperial
    
    Currently calling city number 1775:
    muli, mv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=muli,mv&units=imperial
    
    Currently calling city number 1776:
    tumaco, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tumaco,co&units=imperial
    
    Currently calling city number 1777:
    auka, hn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=auka,hn&units=imperial
    
    Currently calling city number 1778:
    puerto colombia, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=puerto colombia,co&units=imperial
    
    Currently calling city number 1779:
    sfantu gheorghe, ro
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sfantu gheorghe,ro&units=imperial
    
    Currently calling city number 1780:
    boddam, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=boddam,gb&units=imperial
    
    Currently calling city number 1781:
    indianola, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=indianola,us&units=imperial
    
    Currently calling city number 1782:
    aktash, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aktash,ru&units=imperial
    
    Currently calling city number 1783:
    ambo, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ambo,pe&units=imperial
    
    Currently calling city number 1784:
    lianran, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lianran,cn&units=imperial
    
    Currently calling city number 1785:
    denpasar, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=denpasar,id&units=imperial
    
    Currently calling city number 1786:
    roald, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=roald,no&units=imperial
    
    Currently calling city number 1787:
    kez, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kez,ru&units=imperial
    
    Currently calling city number 1788:
    kupang, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kupang,id&units=imperial
    
    Currently calling city number 1789:
    pozo colorado, py
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pozo colorado,py&units=imperial
    
    Currently calling city number 1790:
    abnub, eg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=abnub,eg&units=imperial
    
    Currently calling city number 1791:
    jaque, pa
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jaque,pa&units=imperial
    
    Currently calling city number 1792:
    grand forks, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=grand forks,us&units=imperial
    
    Currently calling city number 1793:
    george, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=george,za&units=imperial
    
    Currently calling city number 1794:
    vitim, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vitim,ru&units=imperial
    
    Currently calling city number 1795:
    agadir, ma
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=agadir,ma&units=imperial
    
    Currently calling city number 1796:
    kushima, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kushima,jp&units=imperial
    
    Currently calling city number 1797:
    makubetsu, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=makubetsu,jp&units=imperial
    
    Currently calling city number 1798:
    udalguri, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=udalguri,in&units=imperial
    
    Currently calling city number 1799:
    carlsbad, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=carlsbad,us&units=imperial
    
    Currently calling city number 1800:
    aktau, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aktau,kz&units=imperial
    
    Currently calling city number 1801:
    sindor, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sindor,ru&units=imperial
    
    Currently calling city number 1802:
    roros, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=roros,no&units=imperial
    
    Currently calling city number 1803:
    chitipa, mw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chitipa,mw&units=imperial
    
    Currently calling city number 1804:
    union de san antonio, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=union de san antonio,mx&units=imperial
    
    Currently calling city number 1805:
    kazachinskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kazachinskoye,ru&units=imperial
    
    Currently calling city number 1806:
    severo-yeniseyskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=severo-yeniseyskiy,ru&units=imperial
    
    Currently calling city number 1807:
    portmore, jm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=portmore,jm&units=imperial
    
    Currently calling city number 1808:
    billings, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=billings,us&units=imperial
    
    Currently calling city number 1809:
    elko, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=elko,us&units=imperial
    
    Currently calling city number 1810:
    kamina, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kamina,cd&units=imperial
    
    Currently calling city number 1811:
    sri aman, my
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sri aman,my&units=imperial
    
    Currently calling city number 1812:
    diego de almagro, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=diego de almagro,cl&units=imperial
    
    Currently calling city number 1813:
    waddan, ly
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=waddan,ly&units=imperial
    
    Currently calling city number 1814:
    standerton, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=standerton,za&units=imperial
    
    Currently calling city number 1815:
    fomboni, km
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fomboni,km&units=imperial
    
    Currently calling city number 1816:
    krutinka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=krutinka,ru&units=imperial
    
    Currently calling city number 1817:
    sita road, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sita road,pk&units=imperial
    
    Currently calling city number 1818:
    turbat, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=turbat,pk&units=imperial
    
    Currently calling city number 1819:
    kichmengskiy gorodok, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kichmengskiy gorodok,ru&units=imperial
    
    Currently calling city number 1820:
    hertford, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hertford,gb&units=imperial
    
    Currently calling city number 1821:
    oum hadjer, td
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=oum hadjer,td&units=imperial
    
    Currently calling city number 1822:
    fayetteville, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fayetteville,us&units=imperial
    
    Currently calling city number 1823:
    markova, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=markova,ru&units=imperial
    
    Currently calling city number 1824:
    diveyevo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=diveyevo,ru&units=imperial
    
    Currently calling city number 1825:
    chapeco, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chapeco,br&units=imperial
    
    Currently calling city number 1826:
    cintalapa, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cintalapa,mx&units=imperial
    
    Currently calling city number 1827:
    asyut, eg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=asyut,eg&units=imperial
    
    Currently calling city number 1828:
    sapucaia, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sapucaia,br&units=imperial
    
    Currently calling city number 1829:
    tortoli, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tortoli,it&units=imperial
    
    Currently calling city number 1830:
    buciumi, ro
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=buciumi,ro&units=imperial
    
    Currently calling city number 1831:
    launceston, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=launceston,au&units=imperial
    
    Currently calling city number 1832:
    domoni, km
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=domoni,km&units=imperial
    
    Currently calling city number 1833:
    ponto novo, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ponto novo,br&units=imperial
    
    Currently calling city number 1834:
    balabac, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=balabac,ph&units=imperial
    
    Currently calling city number 1835:
    yamada, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yamada,jp&units=imperial
    
    Currently calling city number 1836:
    robertsport, lr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=robertsport,lr&units=imperial
    
    Currently calling city number 1837:
    progreso, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=progreso,mx&units=imperial
    
    Currently calling city number 1838:
    miyako, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=miyako,jp&units=imperial
    
    Currently calling city number 1839:
    the pas, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=the pas,ca&units=imperial
    
    Currently calling city number 1840:
    berlin, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=berlin,us&units=imperial
    
    Currently calling city number 1841:
    giaveno, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=giaveno,it&units=imperial
    
    Currently calling city number 1842:
    barra do garcas, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=barra do garcas,br&units=imperial
    
    Currently calling city number 1843:
    sorel, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sorel,ca&units=imperial
    
    Currently calling city number 1844:
    tambovka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tambovka,ru&units=imperial
    
    Currently calling city number 1845:
    cabudare, ve
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cabudare,ve&units=imperial
    
    Currently calling city number 1846:
    mundgod, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mundgod,in&units=imperial
    
    Currently calling city number 1847:
    great bend, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=great bend,us&units=imperial
    
    Currently calling city number 1848:
    nago, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nago,jp&units=imperial
    
    Currently calling city number 1849:
    sheridan, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sheridan,us&units=imperial
    
    Currently calling city number 1850:
    anakapalle, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=anakapalle,in&units=imperial
    
    Currently calling city number 1851:
    dolores, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dolores,ar&units=imperial
    
    Currently calling city number 1852:
    chilca, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chilca,pe&units=imperial
    
    Currently calling city number 1853:
    zvishavane, zw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zvishavane,zw&units=imperial
    
    Currently calling city number 1854:
    verkh-usugli, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=verkh-usugli,ru&units=imperial
    
    Currently calling city number 1855:
    san rafael, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san rafael,ar&units=imperial
    
    Currently calling city number 1856:
    dakoro, ne
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dakoro,ne&units=imperial
    
    Currently calling city number 1857:
    blagoveshchensk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=blagoveshchensk,ru&units=imperial
    
    Currently calling city number 1858:
    tagusao, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tagusao,ph&units=imperial
    
    Currently calling city number 1859:
    turtas, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=turtas,ru&units=imperial
    
    Currently calling city number 1860:
    vieira de leiria, pt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vieira de leiria,pt&units=imperial
    
    Currently calling city number 1861:
    aurillac, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aurillac,fr&units=imperial
    
    Currently calling city number 1862:
    shubarshi, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shubarshi,kz&units=imperial
    
    Currently calling city number 1863:
    jiexiu, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jiexiu,cn&units=imperial
    
    Currently calling city number 1864:
    sinnar, sd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sinnar,sd&units=imperial
    
    Currently calling city number 1865:
    buchanan, lr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=buchanan,lr&units=imperial
    
    Currently calling city number 1866:
    comodoro rivadavia, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=comodoro rivadavia,ar&units=imperial
    
    Currently calling city number 1867:
    rafsanjan, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rafsanjan,ir&units=imperial
    
    Currently calling city number 1868:
    nabire, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nabire,id&units=imperial
    
    Currently calling city number 1869:
    sultanpur, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sultanpur,in&units=imperial
    
    Currently calling city number 1870:
    amurzet, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=amurzet,ru&units=imperial
    
    Currently calling city number 1871:
    turka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=turka,ru&units=imperial
    
    Currently calling city number 1872:
    camopi, gf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=camopi,gf&units=imperial
    
    Currently calling city number 1873:
    steamboat springs, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=steamboat springs,us&units=imperial
    
    Currently calling city number 1874:
    clarence town, bs
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=clarence town,bs&units=imperial
    
    Currently calling city number 1875:
    bage, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bage,br&units=imperial
    
    Currently calling city number 1876:
    marsani, ro
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=marsani,ro&units=imperial
    
    Currently calling city number 1877:
    uruguaiana, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=uruguaiana,br&units=imperial
    
    Currently calling city number 1878:
    ichinoseki, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ichinoseki,jp&units=imperial
    
    Currently calling city number 1879:
    zhangjiakou, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zhangjiakou,cn&units=imperial
    
    Currently calling city number 1880:
    mahanoro, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mahanoro,mg&units=imperial
    
    Currently calling city number 1881:
    djambala, cg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=djambala,cg&units=imperial
    
    Currently calling city number 1882:
    vernon, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vernon,us&units=imperial
    
    Currently calling city number 1883:
    pringsewu, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pringsewu,id&units=imperial
    
    Currently calling city number 1884:
    irbit, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=irbit,ru&units=imperial
    
    Currently calling city number 1885:
    santa cruz de la palma, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santa cruz de la palma,es&units=imperial
    
    Currently calling city number 1886:
    varzea da palma, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=varzea da palma,br&units=imperial
    
    Currently calling city number 1887:
    seddon, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=seddon,nz&units=imperial
    
    Currently calling city number 1888:
    kumla, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kumla,se&units=imperial
    
    Currently calling city number 1889:
    west odessa, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=west odessa,us&units=imperial
    
    Currently calling city number 1890:
    norrtalje, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=norrtalje,se&units=imperial
    
    Currently calling city number 1891:
    andros town, bs
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=andros town,bs&units=imperial
    
    Currently calling city number 1892:
    trelew, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=trelew,ar&units=imperial
    
    Currently calling city number 1893:
    oleksandrivka, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=oleksandrivka,ua&units=imperial
    
    Currently calling city number 1894:
    muscat, om
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=muscat,om&units=imperial
    
    Currently calling city number 1895:
    krasnovishersk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=krasnovishersk,ru&units=imperial
    
    Currently calling city number 1896:
    koutsouras, gr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=koutsouras,gr&units=imperial
    
    Currently calling city number 1897:
    uberlandia, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=uberlandia,br&units=imperial
    
    Currently calling city number 1898:
    kayerkan, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kayerkan,ru&units=imperial
    
    Currently calling city number 1899:
    morondava, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=morondava,mg&units=imperial
    
    Currently calling city number 1900:
    bow island, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bow island,ca&units=imperial
    
    Currently calling city number 1901:
    vryburg, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vryburg,za&units=imperial
    
    Currently calling city number 1902:
    rodrigues alves, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rodrigues alves,br&units=imperial
    
    Currently calling city number 1903:
    southbridge, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=southbridge,nz&units=imperial
    
    Currently calling city number 1904:
    victoria falls, zw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=victoria falls,zw&units=imperial
    
    Currently calling city number 1905:
    chaohu, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chaohu,cn&units=imperial
    
    Currently calling city number 1906:
    pilar, py
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pilar,py&units=imperial
    
    Currently calling city number 1907:
    novyy urgal, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=novyy urgal,ru&units=imperial
    
    Currently calling city number 1908:
    we, nc
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=we,nc&units=imperial
    
    Currently calling city number 1909:
    hastings, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hastings,us&units=imperial
    
    Currently calling city number 1910:
    syasstroy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=syasstroy,ru&units=imperial
    
    Currently calling city number 1911:
    dwarka, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dwarka,in&units=imperial
    
    Currently calling city number 1912:
    kankon, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kankon,in&units=imperial
    
    Currently calling city number 1913:
    alpena, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alpena,us&units=imperial
    
    Currently calling city number 1914:
    staden, be
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=staden,be&units=imperial
    
    Currently calling city number 1915:
    weihai, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=weihai,cn&units=imperial
    
    Currently calling city number 1916:
    anahuac, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=anahuac,mx&units=imperial
    
    Currently calling city number 1917:
    guanhaes, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=guanhaes,br&units=imperial
    
    Currently calling city number 1918:
    puerto rico, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=puerto rico,co&units=imperial
    
    Currently calling city number 1919:
    pinsk, by
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pinsk,by&units=imperial
    
    Currently calling city number 1920:
    nong chik, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nong chik,th&units=imperial
    
    Currently calling city number 1921:
    alamosa, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alamosa,us&units=imperial
    
    Currently calling city number 1922:
    damphu, bt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=damphu,bt&units=imperial
    
    Currently calling city number 1923:
    iranshahr, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=iranshahr,ir&units=imperial
    
    Currently calling city number 1924:
    spring hill, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=spring hill,us&units=imperial
    
    Currently calling city number 1925:
    rosario, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rosario,ar&units=imperial
    
    Currently calling city number 1926:
    lalibela, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lalibela,et&units=imperial
    
    Currently calling city number 1927:
    chirkey, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chirkey,ru&units=imperial
    
    Currently calling city number 1928:
    purwodadi, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=purwodadi,id&units=imperial
    
    Currently calling city number 1929:
    cordoba, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cordoba,ar&units=imperial
    
    Currently calling city number 1930:
    cabadiangan, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cabadiangan,ph&units=imperial
    
    Currently calling city number 1931:
    zapolyarnyy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zapolyarnyy,ru&units=imperial
    
    Currently calling city number 1932:
    pryyutivka, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pryyutivka,ua&units=imperial
    
    Currently calling city number 1933:
    rapid valley, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rapid valley,us&units=imperial
    
    Currently calling city number 1934:
    brokopondo, sr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=brokopondo,sr&units=imperial
    
    Currently calling city number 1935:
    magway, mm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=magway,mm&units=imperial
    
    Currently calling city number 1936:
    abong mbang, cm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=abong mbang,cm&units=imperial
    
    Currently calling city number 1937:
    george town, ky
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=george town,ky&units=imperial
    
    Currently calling city number 1938:
    krasnyy bogatyr, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=krasnyy bogatyr,ru&units=imperial
    
    Currently calling city number 1939:
    mbini, gq
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mbini,gq&units=imperial
    
    Currently calling city number 1940:
    gao, ml
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gao,ml&units=imperial
    
    Currently calling city number 1941:
    louis trichardt, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=louis trichardt,za&units=imperial
    
    Currently calling city number 1942:
    madinat sittah uktubar, eg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=madinat sittah uktubar,eg&units=imperial
    
    Currently calling city number 1943:
    acajutla, sv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=acajutla,sv&units=imperial
    
    Currently calling city number 1944:
    kilkis, gr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kilkis,gr&units=imperial
    
    Currently calling city number 1945:
    diu, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=diu,in&units=imperial
    
    Currently calling city number 1946:
    baoning, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=baoning,cn&units=imperial
    
    Currently calling city number 1947:
    tongren, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tongren,cn&units=imperial
    
    Currently calling city number 1948:
    ankang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ankang,cn&units=imperial
    
    Currently calling city number 1949:
    ghauspur, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ghauspur,pk&units=imperial
    
    Currently calling city number 1950:
    cottonwood, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cottonwood,us&units=imperial
    
    Currently calling city number 1951:
    singaraja, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=singaraja,id&units=imperial
    
    Currently calling city number 1952:
    zangakatun, am
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zangakatun,am&units=imperial
    
    Currently calling city number 1953:
    midland, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=midland,ca&units=imperial
    
    Currently calling city number 1954:
    agra, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=agra,in&units=imperial
    
    Currently calling city number 1955:
    santa marinella, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santa marinella,it&units=imperial
    
    Currently calling city number 1956:
    dawei, mm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dawei,mm&units=imperial
    
    Currently calling city number 1957:
    asbe teferi, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=asbe teferi,et&units=imperial
    
    Currently calling city number 1958:
    makat, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=makat,kz&units=imperial
    
    Currently calling city number 1959:
    khorramshahr, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=khorramshahr,ir&units=imperial
    
    Currently calling city number 1960:
    sobolevo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sobolevo,ru&units=imperial
    
    Currently calling city number 1961:
    besancon, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=besancon,fr&units=imperial
    
    Currently calling city number 1962:
    koshurnikovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=koshurnikovo,ru&units=imperial
    
    Currently calling city number 1963:
    severnyy-kospashskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=severnyy-kospashskiy,ru&units=imperial
    
    Currently calling city number 1964:
    payson, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=payson,us&units=imperial
    
    Currently calling city number 1965:
    djougou, bj
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=djougou,bj&units=imperial
    
    Currently calling city number 1966:
    koster, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=koster,za&units=imperial
    
    Currently calling city number 1967:
    polonnaruwa, lk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=polonnaruwa,lk&units=imperial
    
    Currently calling city number 1968:
    pompeu, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pompeu,br&units=imperial
    
    Currently calling city number 1969:
    bowen, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bowen,au&units=imperial
    
    Currently calling city number 1970:
    piacabucu, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=piacabucu,br&units=imperial
    
    Currently calling city number 1971:
    axim, gh
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=axim,gh&units=imperial
    
    Currently calling city number 1972:
    vendome, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vendome,fr&units=imperial
    
    Currently calling city number 1973:
    jieshi, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jieshi,cn&units=imperial
    
    Currently calling city number 1974:
    yelizovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yelizovo,ru&units=imperial
    
    Currently calling city number 1975:
    saint-joseph, re
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saint-joseph,re&units=imperial
    
    Currently calling city number 1976:
    presidente medici, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=presidente medici,br&units=imperial
    
    Currently calling city number 1977:
    fortuna foothills, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fortuna foothills,us&units=imperial
    
    Currently calling city number 1978:
    troitsko-pechorsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=troitsko-pechorsk,ru&units=imperial
    
    Currently calling city number 1979:
    medea, dz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=medea,dz&units=imperial
    
    Currently calling city number 1980:
    rehoboth, na
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rehoboth,na&units=imperial
    
    Currently calling city number 1981:
    zachary, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zachary,us&units=imperial
    
    Currently calling city number 1982:
    myre, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=myre,no&units=imperial
    
    Currently calling city number 1983:
    sao domingos, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sao domingos,br&units=imperial
    
    Currently calling city number 1984:
    marienburg, sr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=marienburg,sr&units=imperial
    
    Currently calling city number 1985:
    shunyi, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shunyi,cn&units=imperial
    
    Currently calling city number 1986:
    santiago del estero, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santiago del estero,ar&units=imperial
    
    Currently calling city number 1987:
    kreminna, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kreminna,ua&units=imperial
    
    Currently calling city number 1988:
    amboise, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=amboise,fr&units=imperial
    
    Currently calling city number 1989:
    vrangel, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vrangel,ru&units=imperial
    
    Currently calling city number 1990:
    carballo, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=carballo,es&units=imperial
    
    Currently calling city number 1991:
    verkhnyaya inta, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=verkhnyaya inta,ru&units=imperial
    
    Currently calling city number 1992:
    burgeo, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=burgeo,ca&units=imperial
    
    Currently calling city number 1993:
    ndjole, ga
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ndjole,ga&units=imperial
    
    Currently calling city number 1994:
    troitskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=troitskoye,ru&units=imperial
    
    Currently calling city number 1995:
    la plata, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=la plata,ar&units=imperial
    
    Currently calling city number 1996:
    huaicheng, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=huaicheng,cn&units=imperial
    
    Currently calling city number 1997:
    zalantun, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zalantun,cn&units=imperial
    
    Currently calling city number 1998:
    ormara, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ormara,pk&units=imperial
    
    Currently calling city number 1999:
    palu, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=palu,id&units=imperial
    
    Currently calling city number 2000:
    dirba, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dirba,in&units=imperial
    
    Currently calling city number 2001:
    ahuimanu, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ahuimanu,us&units=imperial
    
    Currently calling city number 2002:
    susurluk, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=susurluk,tr&units=imperial
    
    Currently calling city number 2003:
    frontera, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=frontera,mx&units=imperial
    
    Currently calling city number 2004:
    santa isabel do rio negro, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santa isabel do rio negro,br&units=imperial
    
    Currently calling city number 2005:
    johi, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=johi,pk&units=imperial
    
    Currently calling city number 2006:
    sarh, td
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sarh,td&units=imperial
    
    Currently calling city number 2007:
    san luis, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san luis,ar&units=imperial
    
    Currently calling city number 2008:
    paracuru, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=paracuru,br&units=imperial
    
    Currently calling city number 2009:
    ocosingo, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ocosingo,mx&units=imperial
    
    Currently calling city number 2010:
    ashford, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ashford,gb&units=imperial
    
    Currently calling city number 2011:
    derzhavinsk, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=derzhavinsk,kz&units=imperial
    
    Currently calling city number 2012:
    kanniyakumari, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kanniyakumari,in&units=imperial
    
    Currently calling city number 2013:
    kaura namoda, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kaura namoda,ng&units=imperial
    
    Currently calling city number 2014:
    altamont, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=altamont,us&units=imperial
    
    Currently calling city number 2015:
    mlowo, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mlowo,tz&units=imperial
    
    Currently calling city number 2016:
    jiutai, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jiutai,cn&units=imperial
    
    Currently calling city number 2017:
    falam, mm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=falam,mm&units=imperial
    
    Currently calling city number 2018:
    magomeni, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=magomeni,tz&units=imperial
    
    Currently calling city number 2019:
    meybod, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=meybod,ir&units=imperial
    
    Currently calling city number 2020:
    kalmunai, lk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kalmunai,lk&units=imperial
    
    Currently calling city number 2021:
    fizesu gherlii, ro
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fizesu gherlii,ro&units=imperial
    
    Currently calling city number 2022:
    rundu, na
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rundu,na&units=imperial
    
    Currently calling city number 2023:
    rabaul, pg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rabaul,pg&units=imperial
    
    Currently calling city number 2024:
    pilar, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pilar,ph&units=imperial
    
    Currently calling city number 2025:
    aginskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aginskoye,ru&units=imperial
    
    Currently calling city number 2026:
    aventura, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aventura,us&units=imperial
    
    Currently calling city number 2027:
    muleba, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=muleba,tz&units=imperial
    
    Currently calling city number 2028:
    papetoai, pf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=papetoai,pf&units=imperial
    
    Currently calling city number 2029:
    poykovskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=poykovskiy,ru&units=imperial
    
    Currently calling city number 2030:
    birjand, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=birjand,ir&units=imperial
    
    Currently calling city number 2031:
    panguna, pg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=panguna,pg&units=imperial
    
    Currently calling city number 2032:
    kuala terengganu, my
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kuala terengganu,my&units=imperial
    
    Currently calling city number 2033:
    warrington, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=warrington,us&units=imperial
    
    Currently calling city number 2034:
    mezen, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mezen,ru&units=imperial
    
    Currently calling city number 2035:
    osoyoos, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=osoyoos,ca&units=imperial
    
    Currently calling city number 2036:
    orlovskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=orlovskiy,ru&units=imperial
    
    Currently calling city number 2037:
    lixourion, gr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lixourion,gr&units=imperial
    
    Currently calling city number 2038:
    lujan, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lujan,ar&units=imperial
    
    Currently calling city number 2039:
    wana, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wana,pk&units=imperial
    
    Currently calling city number 2040:
    abu zabad, sd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=abu zabad,sd&units=imperial
    
    Currently calling city number 2041:
    conceicao do araguaia, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=conceicao do araguaia,br&units=imperial
    
    Currently calling city number 2042:
    nisia floresta, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nisia floresta,br&units=imperial
    
    Currently calling city number 2043:
    vilhena, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vilhena,br&units=imperial
    
    Currently calling city number 2044:
    coihueco, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=coihueco,cl&units=imperial
    
    Currently calling city number 2045:
    tarko-sale, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tarko-sale,ru&units=imperial
    
    Currently calling city number 2046:
    ostrovskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ostrovskoye,ru&units=imperial
    
    Currently calling city number 2047:
    shaunavon, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shaunavon,ca&units=imperial
    
    Currently calling city number 2048:
    santa fe, cu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santa fe,cu&units=imperial
    
    Currently calling city number 2049:
    zavetnoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zavetnoye,ru&units=imperial
    
    Currently calling city number 2050:
    traverse city, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=traverse city,us&units=imperial
    
    Currently calling city number 2051:
    lexington park, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lexington park,us&units=imperial
    
    Currently calling city number 2052:
    kamoke, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kamoke,pk&units=imperial
    
    Currently calling city number 2053:
    moroni, km
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=moroni,km&units=imperial
    
    Currently calling city number 2054:
    arman, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=arman,ru&units=imperial
    
    Currently calling city number 2055:
    uvat, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=uvat,ru&units=imperial
    
    Currently calling city number 2056:
    opuwo, na
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=opuwo,na&units=imperial
    
    Currently calling city number 2057:
    riga, lv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=riga,lv&units=imperial
    
    Currently calling city number 2058:
    sao joaquim, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sao joaquim,br&units=imperial
    
    Currently calling city number 2059:
    omboue, ga
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=omboue,ga&units=imperial
    
    Currently calling city number 2060:
    cedar city, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cedar city,us&units=imperial
    
    Currently calling city number 2061:
    smiths falls, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=smiths falls,ca&units=imperial
    
    Currently calling city number 2062:
    novaya ladoga, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=novaya ladoga,ru&units=imperial
    
    Currently calling city number 2063:
    anchorage, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=anchorage,us&units=imperial
    
    Currently calling city number 2064:
    rosarito, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rosarito,mx&units=imperial
    
    Currently calling city number 2065:
    dalen, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dalen,no&units=imperial
    
    Currently calling city number 2066:
    casper, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=casper,us&units=imperial
    
    Currently calling city number 2067:
    tacarigua, ve
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tacarigua,ve&units=imperial
    
    Currently calling city number 2068:
    road town, vg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=road town,vg&units=imperial
    
    Currently calling city number 2069:
    esso, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=esso,ru&units=imperial
    
    Currently calling city number 2070:
    asuncion, py
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=asuncion,py&units=imperial
    
    Currently calling city number 2071:
    klyuchi, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=klyuchi,ru&units=imperial
    
    Currently calling city number 2072:
    den helder, nl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=den helder,nl&units=imperial
    
    Currently calling city number 2073:
    zabol, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zabol,ir&units=imperial
    
    Currently calling city number 2074:
    hokitika, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hokitika,nz&units=imperial
    
    Currently calling city number 2075:
    surt, ly
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=surt,ly&units=imperial
    
    Currently calling city number 2076:
    mutoko, zw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mutoko,zw&units=imperial
    
    Currently calling city number 2077:
    miquelon, pm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=miquelon,pm&units=imperial
    
    Currently calling city number 2078:
    villa maria, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=villa maria,ar&units=imperial
    
    Currently calling city number 2079:
    kintampo, gh
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kintampo,gh&units=imperial
    
    Currently calling city number 2080:
    ruidoso, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ruidoso,us&units=imperial
    
    Currently calling city number 2081:
    marabba, sd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=marabba,sd&units=imperial
    
    Currently calling city number 2082:
    san buenaventura, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san buenaventura,mx&units=imperial
    
    Currently calling city number 2083:
    fort smith, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fort smith,us&units=imperial
    
    Currently calling city number 2084:
    khuzhir, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=khuzhir,ru&units=imperial
    
    Currently calling city number 2085:
    manyana, bw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=manyana,bw&units=imperial
    
    Currently calling city number 2086:
    blankenberge, be
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=blankenberge,be&units=imperial
    
    Currently calling city number 2087:
    cuamba, mz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cuamba,mz&units=imperial
    
    Currently calling city number 2088:
    baghdad, iq
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=baghdad,iq&units=imperial
    
    Currently calling city number 2089:
    xai-xai, mz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=xai-xai,mz&units=imperial
    
    Currently calling city number 2090:
    gweta, bw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gweta,bw&units=imperial
    
    Currently calling city number 2091:
    kita, ml
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kita,ml&units=imperial
    
    Currently calling city number 2092:
    daia, ro
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=daia,ro&units=imperial
    
    Currently calling city number 2093:
    dobryanka, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dobryanka,ua&units=imperial
    
    Currently calling city number 2094:
    koutiala, ml
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=koutiala,ml&units=imperial
    
    Currently calling city number 2095:
    morag, pl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=morag,pl&units=imperial
    
    Currently calling city number 2096:
    ayna, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ayna,pe&units=imperial
    
    Currently calling city number 2097:
    gberia fotombu, sl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gberia fotombu,sl&units=imperial
    
    Currently calling city number 2098:
    mandapeta, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mandapeta,in&units=imperial
    
    Currently calling city number 2099:
    vigrestad, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vigrestad,no&units=imperial
    
    Currently calling city number 2100:
    arinos, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=arinos,br&units=imperial
    
    Currently calling city number 2101:
    bagdarin, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bagdarin,ru&units=imperial
    
    Currently calling city number 2102:
    danshui, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=danshui,cn&units=imperial
    
    Currently calling city number 2103:
    san patricio, py
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san patricio,py&units=imperial
    
    Currently calling city number 2104:
    savannah bight, hn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=savannah bight,hn&units=imperial
    
    Currently calling city number 2105:
    cartagena, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cartagena,co&units=imperial
    
    Currently calling city number 2106:
    lashio, mm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lashio,mm&units=imperial
    
    Currently calling city number 2107:
    prabumulih, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=prabumulih,id&units=imperial
    
    Currently calling city number 2108:
    wabana, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wabana,ca&units=imperial
    
    Currently calling city number 2109:
    constantine, dz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=constantine,dz&units=imperial
    
    Currently calling city number 2110:
    acari, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=acari,pe&units=imperial
    
    Currently calling city number 2111:
    kanigoro, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kanigoro,id&units=imperial
    
    Currently calling city number 2112:
    buritama, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=buritama,br&units=imperial
    
    Currently calling city number 2113:
    canyon, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=canyon,us&units=imperial
    
    Currently calling city number 2114:
    rosita, ni
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rosita,ni&units=imperial
    
    Currently calling city number 2115:
    samagaltay, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=samagaltay,ru&units=imperial
    
    Currently calling city number 2116:
    chumphon, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chumphon,th&units=imperial
    
    Currently calling city number 2117:
    neustadt, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=neustadt,de&units=imperial
    
    Currently calling city number 2118:
    kuching, my
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kuching,my&units=imperial
    
    Currently calling city number 2119:
    changtu, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=changtu,cn&units=imperial
    
    Currently calling city number 2120:
    holoby, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=holoby,ua&units=imperial
    
    Currently calling city number 2121:
    ossora, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ossora,ru&units=imperial
    
    Currently calling city number 2122:
    agadez, ne
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=agadez,ne&units=imperial
    
    Currently calling city number 2123:
    gueret, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gueret,fr&units=imperial
    
    Currently calling city number 2124:
    votkinsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=votkinsk,ru&units=imperial
    
    Currently calling city number 2125:
    burley, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=burley,us&units=imperial
    
    Currently calling city number 2126:
    yasnyy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yasnyy,ru&units=imperial
    
    Currently calling city number 2127:
    amboasary, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=amboasary,mg&units=imperial
    
    Currently calling city number 2128:
    montes altos, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=montes altos,br&units=imperial
    
    Currently calling city number 2129:
    octeville, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=octeville,fr&units=imperial
    
    Currently calling city number 2130:
    recodo, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=recodo,ph&units=imperial
    
    Currently calling city number 2131:
    bukama, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bukama,cd&units=imperial
    
    Currently calling city number 2132:
    carpentras, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=carpentras,fr&units=imperial
    
    Currently calling city number 2133:
    zerbst, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zerbst,de&units=imperial
    
    Currently calling city number 2134:
    hoyanger, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hoyanger,no&units=imperial
    
    Currently calling city number 2135:
    estevan, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=estevan,ca&units=imperial
    
    Currently calling city number 2136:
    saquena, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saquena,pe&units=imperial
    
    Currently calling city number 2137:
    milledgeville, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=milledgeville,us&units=imperial
    
    Currently calling city number 2138:
    pathein, mm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pathein,mm&units=imperial
    
    Currently calling city number 2139:
    sao gabriel da cachoeira, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sao gabriel da cachoeira,br&units=imperial
    
    Currently calling city number 2140:
    cervo, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cervo,es&units=imperial
    
    Currently calling city number 2141:
    agbor, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=agbor,ng&units=imperial
    
    Currently calling city number 2142:
    puerto baquerizo moreno, ec
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=puerto baquerizo moreno,ec&units=imperial
    
    Currently calling city number 2143:
    utevka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=utevka,ru&units=imperial
    
    Currently calling city number 2144:
    alibag, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alibag,in&units=imperial
    
    Currently calling city number 2145:
    rorvik, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rorvik,no&units=imperial
    
    Currently calling city number 2146:
    tres arroyos, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tres arroyos,ar&units=imperial
    
    Currently calling city number 2147:
    kovdor, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kovdor,ru&units=imperial
    
    Currently calling city number 2148:
    amalfi, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=amalfi,co&units=imperial
    
    Currently calling city number 2149:
    zalesovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zalesovo,ru&units=imperial
    
    Currently calling city number 2150:
    lubao, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lubao,cd&units=imperial
    
    Currently calling city number 2151:
    yarkovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yarkovo,ru&units=imperial
    
    Currently calling city number 2152:
    srikakulam, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=srikakulam,in&units=imperial
    
    Currently calling city number 2153:
    boa viagem, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=boa viagem,br&units=imperial
    
    Currently calling city number 2154:
    powell river, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=powell river,ca&units=imperial
    
    Currently calling city number 2155:
    scottsboro, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=scottsboro,us&units=imperial
    
    Currently calling city number 2156:
    bandar-e lengeh, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bandar-e lengeh,ir&units=imperial
    
    Currently calling city number 2157:
    indi, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=indi,in&units=imperial
    
    Currently calling city number 2158:
    asosa, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=asosa,et&units=imperial
    
    Currently calling city number 2159:
    tsiroanomandidy, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tsiroanomandidy,mg&units=imperial
    
    Currently calling city number 2160:
    zhengjiatun, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zhengjiatun,cn&units=imperial
    
    Currently calling city number 2161:
    nobres, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nobres,br&units=imperial
    
    Currently calling city number 2162:
    ouallam, ne
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ouallam,ne&units=imperial
    
    Currently calling city number 2163:
    zwedru, lr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zwedru,lr&units=imperial
    
    Currently calling city number 2164:
    tibati, cm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tibati,cm&units=imperial
    
    Currently calling city number 2165:
    kencong, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kencong,id&units=imperial
    
    Currently calling city number 2166:
    caranavi, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=caranavi,bo&units=imperial
    
    Currently calling city number 2167:
    porto belo, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=porto belo,br&units=imperial
    
    Currently calling city number 2168:
    tucurui, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tucurui,br&units=imperial
    
    Currently calling city number 2169:
    norden, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=norden,de&units=imperial
    
    Currently calling city number 2170:
    prainha, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=prainha,br&units=imperial
    
    Currently calling city number 2171:
    bhadra, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bhadra,in&units=imperial
    
    Currently calling city number 2172:
    lukovetskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lukovetskiy,ru&units=imperial
    
    Currently calling city number 2173:
    guaruja, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=guaruja,br&units=imperial
    
    Currently calling city number 2174:
    enid, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=enid,us&units=imperial
    
    Currently calling city number 2175:
    sechenovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sechenovo,ru&units=imperial
    
    Currently calling city number 2176:
    macheng, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=macheng,cn&units=imperial
    
    Currently calling city number 2177:
    kilembe, ug
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kilembe,ug&units=imperial
    
    Currently calling city number 2178:
    balakhninskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=balakhninskiy,ru&units=imperial
    
    Currently calling city number 2179:
    kribi, cm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kribi,cm&units=imperial
    
    Currently calling city number 2180:
    upala, cr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=upala,cr&units=imperial
    
    Currently calling city number 2181:
    poronaysk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=poronaysk,ru&units=imperial
    
    Currently calling city number 2182:
    pamanukan, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pamanukan,id&units=imperial
    
    Currently calling city number 2183:
    marystown, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=marystown,ca&units=imperial
    
    Currently calling city number 2184:
    macapa, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=macapa,br&units=imperial
    
    Currently calling city number 2185:
    phangnga, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=phangnga,th&units=imperial
    
    Currently calling city number 2186:
    avanigadda, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=avanigadda,in&units=imperial
    
    Currently calling city number 2187:
    calatayud, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=calatayud,es&units=imperial
    
    Currently calling city number 2188:
    mogwase, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mogwase,za&units=imperial
    
    Currently calling city number 2189:
    helong, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=helong,cn&units=imperial
    
    Currently calling city number 2190:
    mfou, cm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mfou,cm&units=imperial
    
    Currently calling city number 2191:
    nushki, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nushki,pk&units=imperial
    
    Currently calling city number 2192:
    xiongzhou, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=xiongzhou,cn&units=imperial
    
    Currently calling city number 2193:
    bhaderwah, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bhaderwah,in&units=imperial
    
    Currently calling city number 2194:
    amberley, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=amberley,nz&units=imperial
    
    Currently calling city number 2195:
    oudtshoorn, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=oudtshoorn,za&units=imperial
    
    Currently calling city number 2196:
    sirvintos, lt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sirvintos,lt&units=imperial
    
    Currently calling city number 2197:
    anisoc, gq
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=anisoc,gq&units=imperial
    
    Currently calling city number 2198:
    ingham, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ingham,au&units=imperial
    
    Currently calling city number 2199:
    mokhsogollokh, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mokhsogollokh,ru&units=imperial
    
    Currently calling city number 2200:
    romny, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=romny,ru&units=imperial
    
    Currently calling city number 2201:
    amapa, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=amapa,br&units=imperial
    
    Currently calling city number 2202:
    salym, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=salym,ru&units=imperial
    
    Currently calling city number 2203:
    villarrica, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=villarrica,cl&units=imperial
    
    Currently calling city number 2204:
    karonga, mw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=karonga,mw&units=imperial
    
    Currently calling city number 2205:
    jishu, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jishu,cn&units=imperial
    
    Currently calling city number 2206:
    gibara, cu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gibara,cu&units=imperial
    
    Currently calling city number 2207:
    agropoli, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=agropoli,it&units=imperial
    
    Currently calling city number 2208:
    shiyan, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shiyan,cn&units=imperial
    
    Currently calling city number 2209:
    herat, af
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=herat,af&units=imperial
    
    Currently calling city number 2210:
    ati, td
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ati,td&units=imperial
    
    Currently calling city number 2211:
    henderson, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=henderson,us&units=imperial
    
    Currently calling city number 2212:
    saint-raymond, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saint-raymond,ca&units=imperial
    
    Currently calling city number 2213:
    boyle, ie
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=boyle,ie&units=imperial
    
    Currently calling city number 2214:
    zhuanghe, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zhuanghe,cn&units=imperial
    
    Currently calling city number 2215:
    biltine, td
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=biltine,td&units=imperial
    
    Currently calling city number 2216:
    damara, cf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=damara,cf&units=imperial
    
    Currently calling city number 2217:
    laredo, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=laredo,us&units=imperial
    
    Currently calling city number 2218:
    shimonoseki, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shimonoseki,jp&units=imperial
    
    Currently calling city number 2219:
    krasnoyarsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=krasnoyarsk,ru&units=imperial
    
    Currently calling city number 2220:
    camara de lobos, pt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=camara de lobos,pt&units=imperial
    
    Currently calling city number 2221:
    zhangye, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zhangye,cn&units=imperial
    
    Currently calling city number 2222:
    kondinskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kondinskoye,ru&units=imperial
    
    Currently calling city number 2223:
    edson, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=edson,ca&units=imperial
    
    Currently calling city number 2224:
    coolum beach, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=coolum beach,au&units=imperial
    
    Currently calling city number 2225:
    durusu, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=durusu,tr&units=imperial
    
    Currently calling city number 2226:
    ivybridge, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ivybridge,gb&units=imperial
    
    Currently calling city number 2227:
    millville, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=millville,us&units=imperial
    
    Currently calling city number 2228:
    chitral, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chitral,pk&units=imperial
    
    Currently calling city number 2229:
    sal rei, cv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sal rei,cv&units=imperial
    
    Currently calling city number 2230:
    temirtau, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=temirtau,kz&units=imperial
    
    Currently calling city number 2231:
    mafra, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mafra,br&units=imperial
    
    Currently calling city number 2232:
    archidona, ec
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=archidona,ec&units=imperial
    
    Currently calling city number 2233:
    zelenogorskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zelenogorskiy,ru&units=imperial
    
    Currently calling city number 2234:
    gumdag, tm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gumdag,tm&units=imperial
    
    Currently calling city number 2235:
    zermatt, ch
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zermatt,ch&units=imperial
    
    Currently calling city number 2236:
    hermon, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hermon,us&units=imperial
    
    Currently calling city number 2237:
    capreol, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=capreol,ca&units=imperial
    
    Currently calling city number 2238:
    sinazongwe, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sinazongwe,zm&units=imperial
    
    Currently calling city number 2239:
    alugan, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alugan,ph&units=imperial
    
    Currently calling city number 2240:
    nijar, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nijar,es&units=imperial
    
    Currently calling city number 2241:
    ojinaga, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ojinaga,mx&units=imperial
    
    Currently calling city number 2242:
    anaconda, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=anaconda,us&units=imperial
    
    Currently calling city number 2243:
    adet, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=adet,et&units=imperial
    
    Currently calling city number 2244:
    mariental, na
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mariental,na&units=imperial
    
    Currently calling city number 2245:
    canchungo, gw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=canchungo,gw&units=imperial
    
    Currently calling city number 2246:
    portree, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=portree,gb&units=imperial
    
    Currently calling city number 2247:
    wuchang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wuchang,cn&units=imperial
    
    Currently calling city number 2248:
    price, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=price,us&units=imperial
    
    Currently calling city number 2249:
    grandview, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=grandview,us&units=imperial
    
    Currently calling city number 2250:
    srirampur, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=srirampur,in&units=imperial
    
    Currently calling city number 2251:
    balaipungut, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=balaipungut,id&units=imperial
    
    Currently calling city number 2252:
    san antonio, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san antonio,cl&units=imperial
    
    Currently calling city number 2253:
    sabha, ly
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sabha,ly&units=imperial
    
    Currently calling city number 2254:
    caxito, ao
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=caxito,ao&units=imperial
    
    Currently calling city number 2255:
    gobabis, na
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gobabis,na&units=imperial
    
    Currently calling city number 2256:
    maneadero, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=maneadero,mx&units=imperial
    
    Currently calling city number 2257:
    malindi, ke
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=malindi,ke&units=imperial
    
    Currently calling city number 2258:
    nipawin, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nipawin,ca&units=imperial
    
    Currently calling city number 2259:
    payakumbuh, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=payakumbuh,id&units=imperial
    
    Currently calling city number 2260:
    mambolo, sl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mambolo,sl&units=imperial
    
    Currently calling city number 2261:
    rafai, cf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rafai,cf&units=imperial
    
    Currently calling city number 2262:
    halifax, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=halifax,ca&units=imperial
    
    Currently calling city number 2263:
    paracatu, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=paracatu,br&units=imperial
    
    Currently calling city number 2264:
    outlook, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=outlook,ca&units=imperial
    
    Currently calling city number 2265:
    labuan, my
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=labuan,my&units=imperial
    
    Currently calling city number 2266:
    jacmel, ht
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jacmel,ht&units=imperial
    
    Currently calling city number 2267:
    wanxian, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wanxian,cn&units=imperial
    
    Currently calling city number 2268:
    galesong, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=galesong,id&units=imperial
    
    Currently calling city number 2269:
    lumut, my
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lumut,my&units=imperial
    
    Currently calling city number 2270:
    nadvoitsy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nadvoitsy,ru&units=imperial
    
    Currently calling city number 2271:
    nueva loja, ec
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nueva loja,ec&units=imperial
    
    Currently calling city number 2272:
    zhmerynka, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zhmerynka,ua&units=imperial
    
    Currently calling city number 2273:
    rognan, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rognan,no&units=imperial
    
    Currently calling city number 2274:
    ko samui, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ko samui,th&units=imperial
    
    Currently calling city number 2275:
    susanville, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=susanville,us&units=imperial
    
    Currently calling city number 2276:
    sheoganj, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sheoganj,in&units=imperial
    
    Currently calling city number 2277:
    charleston, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=charleston,us&units=imperial
    
    Currently calling city number 2278:
    vilyuysk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vilyuysk,ru&units=imperial
    
    Currently calling city number 2279:
    newry, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=newry,gb&units=imperial
    
    Currently calling city number 2280:
    westerland, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=westerland,de&units=imperial
    
    Currently calling city number 2281:
    bozhou, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bozhou,cn&units=imperial
    
    Currently calling city number 2282:
    xuanhua, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=xuanhua,cn&units=imperial
    
    Currently calling city number 2283:
    murindo, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=murindo,co&units=imperial
    
    Currently calling city number 2284:
    zemio, cf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zemio,cf&units=imperial
    
    Currently calling city number 2285:
    monchegorsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=monchegorsk,ru&units=imperial
    
    Currently calling city number 2286:
    eenhana, na
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=eenhana,na&units=imperial
    
    Currently calling city number 2287:
    porteirinha, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=porteirinha,br&units=imperial
    
    Currently calling city number 2288:
    rio tuba, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rio tuba,ph&units=imperial
    
    Currently calling city number 2289:
    saryg-sep, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saryg-sep,ru&units=imperial
    
    Currently calling city number 2290:
    bati, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bati,et&units=imperial
    
    Currently calling city number 2291:
    kapiri mposhi, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kapiri mposhi,zm&units=imperial
    
    Currently calling city number 2292:
    tubuala, pa
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tubuala,pa&units=imperial
    
    Currently calling city number 2293:
    boo, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=boo,se&units=imperial
    
    Currently calling city number 2294:
    dmitriyevka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dmitriyevka,ru&units=imperial
    
    Currently calling city number 2295:
    nacala, mz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nacala,mz&units=imperial
    
    Currently calling city number 2296:
    ziyang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ziyang,cn&units=imperial
    
    Currently calling city number 2297:
    nanma, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nanma,cn&units=imperial
    
    Currently calling city number 2298:
    cinnaminson, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cinnaminson,us&units=imperial
    
    Currently calling city number 2299:
    shorapani, ge
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shorapani,ge&units=imperial
    
    Currently calling city number 2300:
    kwinana, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kwinana,au&units=imperial
    
    Currently calling city number 2301:
    davila, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=davila,ph&units=imperial
    
    Currently calling city number 2302:
    itaunja, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=itaunja,in&units=imperial
    
    Currently calling city number 2303:
    manutuke, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=manutuke,nz&units=imperial
    
    Currently calling city number 2304:
    kanashevo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kanashevo,ru&units=imperial
    
    Currently calling city number 2305:
    kostino, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kostino,ru&units=imperial
    
    Currently calling city number 2306:
    abeche, td
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=abeche,td&units=imperial
    
    Currently calling city number 2307:
    sooke, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sooke,ca&units=imperial
    
    Currently calling city number 2308:
    springfield, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=springfield,us&units=imperial
    
    Currently calling city number 2309:
    mirandola, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mirandola,it&units=imperial
    
    Currently calling city number 2310:
    chernyshevskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chernyshevskiy,ru&units=imperial
    
    Currently calling city number 2311:
    bad hofgastein, at
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bad hofgastein,at&units=imperial
    
    Currently calling city number 2312:
    gangelt, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gangelt,de&units=imperial
    
    Currently calling city number 2313:
    punta gorda, bz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=punta gorda,bz&units=imperial
    
    Currently calling city number 2314:
    bartica, gy
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bartica,gy&units=imperial
    
    Currently calling city number 2315:
    gushikawa, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gushikawa,jp&units=imperial
    
    Currently calling city number 2316:
    guozhen, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=guozhen,cn&units=imperial
    
    Currently calling city number 2317:
    tokur, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tokur,ru&units=imperial
    
    Currently calling city number 2318:
    beipiao, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=beipiao,cn&units=imperial
    
    Currently calling city number 2319:
    hazorasp, uz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hazorasp,uz&units=imperial
    
    Currently calling city number 2320:
    kirensk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kirensk,ru&units=imperial
    
    Currently calling city number 2321:
    kpandu, gh
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kpandu,gh&units=imperial
    
    Currently calling city number 2322:
    mountain home, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mountain home,us&units=imperial
    
    Currently calling city number 2323:
    afrikanda, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=afrikanda,ru&units=imperial
    
    Currently calling city number 2324:
    matay, eg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=matay,eg&units=imperial
    
    Currently calling city number 2325:
    malur, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=malur,in&units=imperial
    
    Currently calling city number 2326:
    terra santa, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=terra santa,br&units=imperial
    
    Currently calling city number 2327:
    tshela, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tshela,cd&units=imperial
    
    Currently calling city number 2328:
    kourou, gf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kourou,gf&units=imperial
    
    Currently calling city number 2329:
    pinega, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pinega,ru&units=imperial
    
    Currently calling city number 2330:
    jeremie, ht
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jeremie,ht&units=imperial
    
    Currently calling city number 2331:
    solwezi, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=solwezi,zm&units=imperial
    
    Currently calling city number 2332:
    cavalcante, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cavalcante,br&units=imperial
    
    Currently calling city number 2333:
    volokolamsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=volokolamsk,ru&units=imperial
    
    Currently calling city number 2334:
    paso de carrasco, uy
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=paso de carrasco,uy&units=imperial
    
    Currently calling city number 2335:
    gorontalo, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gorontalo,id&units=imperial
    
    Currently calling city number 2336:
    la pila, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=la pila,mx&units=imperial
    
    Currently calling city number 2337:
    rebrikha, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rebrikha,ru&units=imperial
    
    Currently calling city number 2338:
    ayia marina, gr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ayia marina,gr&units=imperial
    
    Currently calling city number 2339:
    florence, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=florence,us&units=imperial
    
    Currently calling city number 2340:
    collie, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=collie,au&units=imperial
    
    Currently calling city number 2341:
    tosya, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tosya,tr&units=imperial
    
    Currently calling city number 2342:
    murray bridge, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=murray bridge,au&units=imperial
    
    Currently calling city number 2343:
    filadelfia, py
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=filadelfia,py&units=imperial
    
    Currently calling city number 2344:
    waterfoot, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=waterfoot,gb&units=imperial
    
    Currently calling city number 2345:
    ode, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ode,ng&units=imperial
    
    Currently calling city number 2346:
    nakamura, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nakamura,jp&units=imperial
    
    Currently calling city number 2347:
    lahat, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lahat,id&units=imperial
    
    Currently calling city number 2348:
    evensk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=evensk,ru&units=imperial
    
    Currently calling city number 2349:
    fevik, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fevik,no&units=imperial
    
    Currently calling city number 2350:
    kamsack, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kamsack,ca&units=imperial
    
    Currently calling city number 2351:
    garhi khairo, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=garhi khairo,pk&units=imperial
    
    Currently calling city number 2352:
    sulmona, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sulmona,it&units=imperial
    
    Currently calling city number 2353:
    hirado, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hirado,jp&units=imperial
    
    Currently calling city number 2354:
    ilhabela, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ilhabela,br&units=imperial
    
    Currently calling city number 2355:
    nayudupeta, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nayudupeta,in&units=imperial
    
    Currently calling city number 2356:
    banepa, np
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=banepa,np&units=imperial
    
    Currently calling city number 2357:
    lyskovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lyskovo,ru&units=imperial
    
    Currently calling city number 2358:
    beruwala, lk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=beruwala,lk&units=imperial
    
    Currently calling city number 2359:
    batken, kg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=batken,kg&units=imperial
    
    Currently calling city number 2360:
    purranque, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=purranque,cl&units=imperial
    
    Currently calling city number 2361:
    cam ranh, vn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cam ranh,vn&units=imperial
    
    Currently calling city number 2362:
    lima, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lima,pe&units=imperial
    
    Currently calling city number 2363:
    motygino, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=motygino,ru&units=imperial
    
    Currently calling city number 2364:
    vuktyl, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vuktyl,ru&units=imperial
    
    Currently calling city number 2365:
    sao gotardo, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sao gotardo,br&units=imperial
    
    Currently calling city number 2366:
    conway, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=conway,us&units=imperial
    
    Currently calling city number 2367:
    bemidji, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bemidji,us&units=imperial
    
    Currently calling city number 2368:
    cefalu, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cefalu,it&units=imperial
    
    Currently calling city number 2369:
    sembe, cg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sembe,cg&units=imperial
    
    Currently calling city number 2370:
    algiers, dz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=algiers,dz&units=imperial
    
    Currently calling city number 2371:
    kamalpur, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kamalpur,in&units=imperial
    
    Currently calling city number 2372:
    makhu, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=makhu,in&units=imperial
    
    Currently calling city number 2373:
    dalianwan, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dalianwan,cn&units=imperial
    
    Currently calling city number 2374:
    srivardhan, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=srivardhan,in&units=imperial
    
    Currently calling city number 2375:
    talavera de la reina, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=talavera de la reina,es&units=imperial
    
    Currently calling city number 2376:
    yucaipa, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yucaipa,us&units=imperial
    
    Currently calling city number 2377:
    honningsvag, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=honningsvag,no&units=imperial
    
    Currently calling city number 2378:
    isagarh, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=isagarh,in&units=imperial
    
    Currently calling city number 2379:
    zalaszentgrot, hu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zalaszentgrot,hu&units=imperial
    
    Currently calling city number 2380:
    tucumcari, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tucumcari,us&units=imperial
    
    Currently calling city number 2381:
    koygorodok, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=koygorodok,ru&units=imperial
    
    Currently calling city number 2382:
    bodden town, ky
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bodden town,ky&units=imperial
    
    Currently calling city number 2383:
    vazhiny, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vazhiny,ru&units=imperial
    
    Currently calling city number 2384:
    banff, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=banff,ca&units=imperial
    
    Currently calling city number 2385:
    pingxiang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pingxiang,cn&units=imperial
    
    Currently calling city number 2386:
    nokaneng, bw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nokaneng,bw&units=imperial
    
    Currently calling city number 2387:
    nguru, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nguru,ng&units=imperial
    
    Currently calling city number 2388:
    linqiong, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=linqiong,cn&units=imperial
    
    Currently calling city number 2389:
    umarkot, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=umarkot,in&units=imperial
    
    Currently calling city number 2390:
    tingi, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tingi,tz&units=imperial
    
    Currently calling city number 2391:
    tunceli, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tunceli,tr&units=imperial
    
    Currently calling city number 2392:
    tibiri, ne
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tibiri,ne&units=imperial
    
    Currently calling city number 2393:
    diest, be
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=diest,be&units=imperial
    
    Currently calling city number 2394:
    mentougou, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mentougou,cn&units=imperial
    
    Currently calling city number 2395:
    xiongyue, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=xiongyue,cn&units=imperial
    
    Currently calling city number 2396:
    liwale, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=liwale,tz&units=imperial
    
    Currently calling city number 2397:
    carlos chagas, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=carlos chagas,br&units=imperial
    
    Currently calling city number 2398:
    cusuna, hn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cusuna,hn&units=imperial
    
    Currently calling city number 2399:
    mount barker, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mount barker,au&units=imperial
    
    Currently calling city number 2400:
    chivilcoy, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chivilcoy,ar&units=imperial
    
    Currently calling city number 2401:
    andra, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=andra,ru&units=imperial
    
    Currently calling city number 2402:
    gilbues, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gilbues,br&units=imperial
    
    Currently calling city number 2403:
    urdoma, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=urdoma,ru&units=imperial
    
    Currently calling city number 2404:
    uspenka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=uspenka,ru&units=imperial
    
    Currently calling city number 2405:
    nevsehir, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nevsehir,tr&units=imperial
    
    Currently calling city number 2406:
    wadi, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wadi,in&units=imperial
    
    Currently calling city number 2407:
    inverness, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=inverness,ca&units=imperial
    
    Currently calling city number 2408:
    magadan, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=magadan,ru&units=imperial
    
    Currently calling city number 2409:
    maumere, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=maumere,id&units=imperial
    
    Currently calling city number 2410:
    tshikapa, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tshikapa,cd&units=imperial
    
    Currently calling city number 2411:
    dayong, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dayong,cn&units=imperial
    
    Currently calling city number 2412:
    fraserburgh, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fraserburgh,gb&units=imperial
    
    Currently calling city number 2413:
    cedar rapids, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cedar rapids,us&units=imperial
    
    Currently calling city number 2414:
    burns lake, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=burns lake,ca&units=imperial
    
    Currently calling city number 2415:
    wisconsin rapids, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wisconsin rapids,us&units=imperial
    
    Currently calling city number 2416:
    ornskoldsvik, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ornskoldsvik,se&units=imperial
    
    Currently calling city number 2417:
    mangai, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mangai,cd&units=imperial
    
    Currently calling city number 2418:
    lakes entrance, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lakes entrance,au&units=imperial
    
    Currently calling city number 2419:
    sao miguel do araguaia, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sao miguel do araguaia,br&units=imperial
    
    Currently calling city number 2420:
    nova olinda do norte, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nova olinda do norte,br&units=imperial
    
    Currently calling city number 2421:
    manzhouli, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=manzhouli,cn&units=imperial
    
    Currently calling city number 2422:
    shizunai, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shizunai,jp&units=imperial
    
    Currently calling city number 2423:
    tinskoy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tinskoy,ru&units=imperial
    
    Currently calling city number 2424:
    mandapam, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mandapam,in&units=imperial
    
    Currently calling city number 2425:
    tabory, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tabory,ru&units=imperial
    
    Currently calling city number 2426:
    uusikaupunki, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=uusikaupunki,fi&units=imperial
    
    Currently calling city number 2427:
    predivinsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=predivinsk,ru&units=imperial
    
    Currently calling city number 2428:
    bafoulabe, ml
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bafoulabe,ml&units=imperial
    
    Currently calling city number 2429:
    zhumadian, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zhumadian,cn&units=imperial
    
    Currently calling city number 2430:
    codajas, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=codajas,br&units=imperial
    
    Currently calling city number 2431:
    joensuu, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=joensuu,fi&units=imperial
    
    Currently calling city number 2432:
    berja, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=berja,es&units=imperial
    
    Currently calling city number 2433:
    plerin, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=plerin,fr&units=imperial
    
    Currently calling city number 2434:
    vikyrovice, cz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vikyrovice,cz&units=imperial
    
    Currently calling city number 2435:
    bundaberg, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bundaberg,au&units=imperial
    
    Currently calling city number 2436:
    los algarrobos, pa
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=los algarrobos,pa&units=imperial
    
    Currently calling city number 2437:
    sobinka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sobinka,ru&units=imperial
    
    Currently calling city number 2438:
    lowestoft, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lowestoft,gb&units=imperial
    
    Currently calling city number 2439:
    bela vista, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bela vista,br&units=imperial
    
    Currently calling city number 2440:
    temple, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=temple,us&units=imperial
    
    Currently calling city number 2441:
    cheyenne, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cheyenne,us&units=imperial
    
    Currently calling city number 2442:
    yanam, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yanam,in&units=imperial
    
    Currently calling city number 2443:
    gurupi, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gurupi,br&units=imperial
    
    Currently calling city number 2444:
    mawlaik, mm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mawlaik,mm&units=imperial
    
    Currently calling city number 2445:
    bintulu, my
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bintulu,my&units=imperial
    
    Currently calling city number 2446:
    pratapgarh, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pratapgarh,in&units=imperial
    
    Currently calling city number 2447:
    dno, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dno,ru&units=imperial
    
    Currently calling city number 2448:
    langenburg, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=langenburg,ca&units=imperial
    
    Currently calling city number 2449:
    siedlce, pl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=siedlce,pl&units=imperial
    
    Currently calling city number 2450:
    murgab, tm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=murgab,tm&units=imperial
    
    Currently calling city number 2451:
    aksu, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aksu,kz&units=imperial
    
    Currently calling city number 2452:
    guryevsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=guryevsk,ru&units=imperial
    
    Currently calling city number 2453:
    cabrero, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cabrero,cl&units=imperial
    
    Currently calling city number 2454:
    hohhot, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hohhot,cn&units=imperial
    
    Currently calling city number 2455:
    chingirlau, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chingirlau,kz&units=imperial
    
    Currently calling city number 2456:
    gua musang, my
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gua musang,my&units=imperial
    
    Currently calling city number 2457:
    bend, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bend,us&units=imperial
    
    Currently calling city number 2458:
    hamadan, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hamadan,ir&units=imperial
    
    Currently calling city number 2459:
    labytnangi, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=labytnangi,ru&units=imperial
    
    Currently calling city number 2460:
    wageningen, sr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wageningen,sr&units=imperial
    
    Currently calling city number 2461:
    angouleme, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=angouleme,fr&units=imperial
    
    Currently calling city number 2462:
    krasnokholmskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=krasnokholmskiy,ru&units=imperial
    
    Currently calling city number 2463:
    otane, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=otane,nz&units=imperial
    
    Currently calling city number 2464:
    sosua, do
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sosua,do&units=imperial
    
    Currently calling city number 2465:
    adre, td
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=adre,td&units=imperial
    
    Currently calling city number 2466:
    nuevo ideal, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nuevo ideal,mx&units=imperial
    
    Currently calling city number 2467:
    magdalena, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=magdalena,ph&units=imperial
    
    Currently calling city number 2468:
    hede, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hede,cn&units=imperial
    
    Currently calling city number 2469:
    mbaiki, cf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mbaiki,cf&units=imperial
    
    Currently calling city number 2470:
    suhbaatar, mn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=suhbaatar,mn&units=imperial
    
    Currently calling city number 2471:
    brindisi, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=brindisi,it&units=imperial
    
    Currently calling city number 2472:
    blackwater, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=blackwater,au&units=imperial
    
    Currently calling city number 2473:
    yorosso, ml
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yorosso,ml&units=imperial
    
    Currently calling city number 2474:
    majene, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=majene,id&units=imperial
    
    Currently calling city number 2475:
    mukhen, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mukhen,ru&units=imperial
    
    Currently calling city number 2476:
    chhabra, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chhabra,in&units=imperial
    
    Currently calling city number 2477:
    balakovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=balakovo,ru&units=imperial
    
    Currently calling city number 2478:
    mazabuka, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mazabuka,zm&units=imperial
    
    Currently calling city number 2479:
    ugoofaaru, mv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ugoofaaru,mv&units=imperial
    
    Currently calling city number 2480:
    hanyang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hanyang,cn&units=imperial
    
    Currently calling city number 2481:
    nayagarh, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nayagarh,in&units=imperial
    
    Currently calling city number 2482:
    kapustin yar, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kapustin yar,ru&units=imperial
    
    Currently calling city number 2483:
    lagos, pt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lagos,pt&units=imperial
    
    Currently calling city number 2484:
    braganca, pt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=braganca,pt&units=imperial
    
    Currently calling city number 2485:
    kokopo, pg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kokopo,pg&units=imperial
    
    Currently calling city number 2486:
    pitanga, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pitanga,br&units=imperial
    
    Currently calling city number 2487:
    dhangadhi, np
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dhangadhi,np&units=imperial
    
    Currently calling city number 2488:
    dickinson, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dickinson,us&units=imperial
    
    Currently calling city number 2489:
    laizhou, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=laizhou,cn&units=imperial
    
    Currently calling city number 2490:
    okato, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=okato,nz&units=imperial
    
    Currently calling city number 2491:
    sharjah, ae
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sharjah,ae&units=imperial
    
    Currently calling city number 2492:
    kampong chhnang, kh
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kampong chhnang,kh&units=imperial
    
    Currently calling city number 2493:
    rypefjord, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rypefjord,no&units=imperial
    
    Currently calling city number 2494:
    eucaliptus, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=eucaliptus,bo&units=imperial
    
    Currently calling city number 2495:
    conguaco, gt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=conguaco,gt&units=imperial
    
    Currently calling city number 2496:
    bam, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bam,ir&units=imperial
    
    Currently calling city number 2497:
    kalaisan, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kalaisan,ph&units=imperial
    
    Currently calling city number 2498:
    forestville, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=forestville,ca&units=imperial
    
    Currently calling city number 2499:
    bozovici, ro
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bozovici,ro&units=imperial
    
    Currently calling city number 2500:
    chitungwiza, zw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chitungwiza,zw&units=imperial
    
    Currently calling city number 2501:
    narnaul, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=narnaul,in&units=imperial
    
    Currently calling city number 2502:
    karangampel, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=karangampel,id&units=imperial
    
    Currently calling city number 2503:
    beira, mz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=beira,mz&units=imperial
    
    Currently calling city number 2504:
    kovernino, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kovernino,ru&units=imperial
    
    Currently calling city number 2505:
    ambunti, pg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ambunti,pg&units=imperial
    
    Currently calling city number 2506:
    kennewick, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kennewick,us&units=imperial
    
    Currently calling city number 2507:
    chabahar, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chabahar,ir&units=imperial
    
    Currently calling city number 2508:
    culpeper, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=culpeper,us&units=imperial
    
    Currently calling city number 2509:
    samara, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=samara,ru&units=imperial
    
    Currently calling city number 2510:
    warwick, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=warwick,au&units=imperial
    
    Currently calling city number 2511:
    hachinohe, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hachinohe,jp&units=imperial
    
    Currently calling city number 2512:
    avrameni, ro
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=avrameni,ro&units=imperial
    
    Currently calling city number 2513:
    san juan, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san juan,us&units=imperial
    
    Currently calling city number 2514:
    gladstone, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gladstone,au&units=imperial
    
    Currently calling city number 2515:
    belyy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=belyy,ru&units=imperial
    
    Currently calling city number 2516:
    fuxin, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fuxin,cn&units=imperial
    
    Currently calling city number 2517:
    tempio pausania, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tempio pausania,it&units=imperial
    
    Currently calling city number 2518:
    kishi, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kishi,ng&units=imperial
    
    Currently calling city number 2519:
    mazagao, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mazagao,br&units=imperial
    
    Currently calling city number 2520:
    consett, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=consett,gb&units=imperial
    
    Currently calling city number 2521:
    sazonovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sazonovo,ru&units=imperial
    
    Currently calling city number 2522:
    dukat, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dukat,ru&units=imperial
    
    Currently calling city number 2523:
    felipe carrillo puerto, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=felipe carrillo puerto,mx&units=imperial
    
    Currently calling city number 2524:
    yurga, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yurga,ru&units=imperial
    
    Currently calling city number 2525:
    nanyamba, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nanyamba,tz&units=imperial
    
    Currently calling city number 2526:
    gaoua, bf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gaoua,bf&units=imperial
    
    Currently calling city number 2527:
    balsas, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=balsas,br&units=imperial
    
    Currently calling city number 2528:
    sharan, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sharan,ru&units=imperial
    
    Currently calling city number 2529:
    rosendal, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rosendal,no&units=imperial
    
    Currently calling city number 2530:
    bongandanga, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bongandanga,cd&units=imperial
    
    Currently calling city number 2531:
    moree, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=moree,au&units=imperial
    
    Currently calling city number 2532:
    znamenskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=znamenskoye,ru&units=imperial
    
    Currently calling city number 2533:
    lena, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lena,es&units=imperial
    
    Currently calling city number 2534:
    polyarnyy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=polyarnyy,ru&units=imperial
    
    Currently calling city number 2535:
    zhaodong, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zhaodong,cn&units=imperial
    
    Currently calling city number 2536:
    kalengwa, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kalengwa,zm&units=imperial
    
    Currently calling city number 2537:
    cadiz, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cadiz,es&units=imperial
    
    Currently calling city number 2538:
    jagdalpur, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jagdalpur,in&units=imperial
    
    Currently calling city number 2539:
    bokoro, td
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bokoro,td&units=imperial
    
    Currently calling city number 2540:
    muyezerskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=muyezerskiy,ru&units=imperial
    
    Currently calling city number 2541:
    barvinkove, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=barvinkove,ua&units=imperial
    
    Currently calling city number 2542:
    aviles, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aviles,es&units=imperial
    
    Currently calling city number 2543:
    canton, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=canton,us&units=imperial
    
    Currently calling city number 2544:
    subiaco, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=subiaco,it&units=imperial
    
    Currently calling city number 2545:
    sorrento, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sorrento,it&units=imperial
    
    Currently calling city number 2546:
    proti, gr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=proti,gr&units=imperial
    
    Currently calling city number 2547:
    aracuai, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aracuai,br&units=imperial
    
    Currently calling city number 2548:
    benjamin constant, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=benjamin constant,br&units=imperial
    
    Currently calling city number 2549:
    bima, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bima,id&units=imperial
    
    Currently calling city number 2550:
    yekaterinoslavka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yekaterinoslavka,ru&units=imperial
    
    Currently calling city number 2551:
    dogondoutchi, ne
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dogondoutchi,ne&units=imperial
    
    Currently calling city number 2552:
    lisiy nos, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lisiy nos,ru&units=imperial
    
    Currently calling city number 2553:
    zhenlai, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zhenlai,cn&units=imperial
    
    Currently calling city number 2554:
    alausi, ec
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alausi,ec&units=imperial
    
    Currently calling city number 2555:
    zagreb, hr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zagreb,hr&units=imperial
    
    Currently calling city number 2556:
    lar, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lar,ir&units=imperial
    
    Currently calling city number 2557:
    parrita, cr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=parrita,cr&units=imperial
    
    Currently calling city number 2558:
    puerto cabezas, ni
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=puerto cabezas,ni&units=imperial
    
    Currently calling city number 2559:
    misratah, ly
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=misratah,ly&units=imperial
    
    Currently calling city number 2560:
    ntcheu, mw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ntcheu,mw&units=imperial
    
    Currently calling city number 2561:
    lalpur, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lalpur,in&units=imperial
    
    Currently calling city number 2562:
    oyotun, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=oyotun,pe&units=imperial
    
    Currently calling city number 2563:
    charlestown, kn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=charlestown,kn&units=imperial
    
    Currently calling city number 2564:
    sarakhs, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sarakhs,ir&units=imperial
    
    Currently calling city number 2565:
    kastamonu, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kastamonu,tr&units=imperial
    
    Currently calling city number 2566:
    kassala, sd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kassala,sd&units=imperial
    
    Currently calling city number 2567:
    ruiz, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ruiz,mx&units=imperial
    
    Currently calling city number 2568:
    halmeu, ro
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=halmeu,ro&units=imperial
    
    Currently calling city number 2569:
    aksay, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aksay,kz&units=imperial
    
    Currently calling city number 2570:
    san pedro, bz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san pedro,bz&units=imperial
    
    Currently calling city number 2571:
    garh maharaja, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=garh maharaja,pk&units=imperial
    
    Currently calling city number 2572:
    chancay, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chancay,pe&units=imperial
    
    Currently calling city number 2573:
    san pedro, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san pedro,bo&units=imperial
    
    Currently calling city number 2574:
    gazli, uz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gazli,uz&units=imperial
    
    Currently calling city number 2575:
    serowe, bw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=serowe,bw&units=imperial
    
    Currently calling city number 2576:
    remedios, pa
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=remedios,pa&units=imperial
    
    Currently calling city number 2577:
    ouadda, cf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ouadda,cf&units=imperial
    
    Currently calling city number 2578:
    severobaykalsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=severobaykalsk,ru&units=imperial
    
    Currently calling city number 2579:
    kotka, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kotka,fi&units=imperial
    
    Currently calling city number 2580:
    narasannapeta, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=narasannapeta,in&units=imperial
    
    Currently calling city number 2581:
    linkuva, lt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=linkuva,lt&units=imperial
    
    Currently calling city number 2582:
    viramgam, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=viramgam,in&units=imperial
    
    Currently calling city number 2583:
    challans, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=challans,fr&units=imperial
    
    Currently calling city number 2584:
    concepcion, py
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=concepcion,py&units=imperial
    
    Currently calling city number 2585:
    bulungu, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bulungu,cd&units=imperial
    
    Currently calling city number 2586:
    terryville, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=terryville,us&units=imperial
    
    Currently calling city number 2587:
    dongying, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dongying,cn&units=imperial
    
    Currently calling city number 2588:
    shasta lake, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shasta lake,us&units=imperial
    
    Currently calling city number 2589:
    volot, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=volot,ru&units=imperial
    
    Currently calling city number 2590:
    bandipur, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bandipur,in&units=imperial
    
    Currently calling city number 2591:
    ashland, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ashland,us&units=imperial
    
    Currently calling city number 2592:
    sriperumbudur, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sriperumbudur,in&units=imperial
    
    Currently calling city number 2593:
    petit goave, ht
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=petit goave,ht&units=imperial
    
    Currently calling city number 2594:
    belle glade, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=belle glade,us&units=imperial
    
    Currently calling city number 2595:
    marang, my
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=marang,my&units=imperial
    
    Currently calling city number 2596:
    graaff-reinet, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=graaff-reinet,za&units=imperial
    
    Currently calling city number 2597:
    abilene, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=abilene,us&units=imperial
    
    Currently calling city number 2598:
    dangriga, bz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dangriga,bz&units=imperial
    
    Currently calling city number 2599:
    piranshahr, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=piranshahr,ir&units=imperial
    
    Currently calling city number 2600:
    chifeng, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chifeng,cn&units=imperial
    
    Currently calling city number 2601:
    teno, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=teno,cl&units=imperial
    
    Currently calling city number 2602:
    pontes e lacerda, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pontes e lacerda,br&units=imperial
    
    Currently calling city number 2603:
    chunoyar, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chunoyar,ru&units=imperial
    
    Currently calling city number 2604:
    aketi, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aketi,cd&units=imperial
    
    Currently calling city number 2605:
    salina, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=salina,us&units=imperial
    
    Currently calling city number 2606:
    omura, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=omura,jp&units=imperial
    
    Currently calling city number 2607:
    lichuan, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lichuan,cn&units=imperial
    
    Currently calling city number 2608:
    semey, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=semey,kz&units=imperial
    
    Currently calling city number 2609:
    port said, eg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=port said,eg&units=imperial
    
    Currently calling city number 2610:
    chongwe, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chongwe,zm&units=imperial
    
    Currently calling city number 2611:
    otautau, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=otautau,nz&units=imperial
    
    Currently calling city number 2612:
    ishim, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ishim,ru&units=imperial
    
    Currently calling city number 2613:
    bulawayo, zw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bulawayo,zw&units=imperial
    
    Currently calling city number 2614:
    viking, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=viking,ca&units=imperial
    
    Currently calling city number 2615:
    edgewater, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=edgewater,us&units=imperial
    
    Currently calling city number 2616:
    along, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=along,in&units=imperial
    
    Currently calling city number 2617:
    kolosovka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kolosovka,ru&units=imperial
    
    Currently calling city number 2618:
    shumskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shumskiy,ru&units=imperial
    
    Currently calling city number 2619:
    visnes, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=visnes,no&units=imperial
    
    Currently calling city number 2620:
    san felipe, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san felipe,mx&units=imperial
    
    Currently calling city number 2621:
    cacoal, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cacoal,br&units=imperial
    
    Currently calling city number 2622:
    sargatskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sargatskoye,ru&units=imperial
    
    Currently calling city number 2623:
    karlskrona, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=karlskrona,se&units=imperial
    
    Currently calling city number 2624:
    malangali, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=malangali,tz&units=imperial
    
    Currently calling city number 2625:
    burriana, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=burriana,es&units=imperial
    
    Currently calling city number 2626:
    novobirilyussy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=novobirilyussy,ru&units=imperial
    
    Currently calling city number 2627:
    manaus, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=manaus,br&units=imperial
    
    Currently calling city number 2628:
    grafton, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=grafton,au&units=imperial
    
    Currently calling city number 2629:
    lichinga, mz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lichinga,mz&units=imperial
    
    Currently calling city number 2630:
    rockhampton, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rockhampton,au&units=imperial
    
    Currently calling city number 2631:
    chinchani, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chinchani,in&units=imperial
    
    Currently calling city number 2632:
    den chai, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=den chai,th&units=imperial
    
    Currently calling city number 2633:
    togur, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=togur,ru&units=imperial
    
    Currently calling city number 2634:
    gallipoli, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gallipoli,it&units=imperial
    
    Currently calling city number 2635:
    velyka lepetykha, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=velyka lepetykha,ua&units=imperial
    
    Currently calling city number 2636:
    iwanai, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=iwanai,jp&units=imperial
    
    Currently calling city number 2637:
    gistrup, dk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gistrup,dk&units=imperial
    
    Currently calling city number 2638:
    rurrenabaque, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rurrenabaque,bo&units=imperial
    
    Currently calling city number 2639:
    moba, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=moba,cd&units=imperial
    
    Currently calling city number 2640:
    morant bay, jm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=morant bay,jm&units=imperial
    
    Currently calling city number 2641:
    petropavlovskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=petropavlovskoye,ru&units=imperial
    
    Currently calling city number 2642:
    manicore, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=manicore,br&units=imperial
    
    Currently calling city number 2643:
    claresholm, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=claresholm,ca&units=imperial
    
    Currently calling city number 2644:
    nnewi, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nnewi,ng&units=imperial
    
    Currently calling city number 2645:
    omaruru, na
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=omaruru,na&units=imperial
    
    Currently calling city number 2646:
    mikhaylovka, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mikhaylovka,kz&units=imperial
    
    Currently calling city number 2647:
    ozinki, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ozinki,ru&units=imperial
    
    Currently calling city number 2648:
    gescher, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gescher,de&units=imperial
    
    Currently calling city number 2649:
    jian, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jian,cn&units=imperial
    
    Currently calling city number 2650:
    senanga, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=senanga,zm&units=imperial
    
    Currently calling city number 2651:
    pecos, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pecos,us&units=imperial
    
    Currently calling city number 2652:
    taua, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=taua,br&units=imperial
    
    Currently calling city number 2653:
    bismarck, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bismarck,us&units=imperial
    
    Currently calling city number 2654:
    san miguel, pa
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san miguel,pa&units=imperial
    
    Currently calling city number 2655:
    kirkwood, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kirkwood,za&units=imperial
    
    Currently calling city number 2656:
    rossland, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rossland,ca&units=imperial
    
    Currently calling city number 2657:
    igarka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=igarka,ru&units=imperial
    
    Currently calling city number 2658:
    banda, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=banda,in&units=imperial
    
    Currently calling city number 2659:
    urucara, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=urucara,br&units=imperial
    
    Currently calling city number 2660:
    sawakin, sd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sawakin,sd&units=imperial
    
    Currently calling city number 2661:
    cao bang, vn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cao bang,vn&units=imperial
    
    Currently calling city number 2662:
    manta, ec
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=manta,ec&units=imperial
    
    Currently calling city number 2663:
    sidi ali, dz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sidi ali,dz&units=imperial
    
    Currently calling city number 2664:
    coari, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=coari,br&units=imperial
    
    Currently calling city number 2665:
    placido de castro, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=placido de castro,br&units=imperial
    
    Currently calling city number 2666:
    kerouane, gn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kerouane,gn&units=imperial
    
    Currently calling city number 2667:
    sesheke, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sesheke,zm&units=imperial
    
    Currently calling city number 2668:
    ciudad bolivar, ve
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ciudad bolivar,ve&units=imperial
    
    Currently calling city number 2669:
    chiguayante, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chiguayante,cl&units=imperial
    
    Currently calling city number 2670:
    pop, uz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pop,uz&units=imperial
    
    Currently calling city number 2671:
    jaromerice nad rokytnou, cz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jaromerice nad rokytnou,cz&units=imperial
    
    Currently calling city number 2672:
    linfen, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=linfen,cn&units=imperial
    
    Currently calling city number 2673:
    adrian, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=adrian,us&units=imperial
    
    Currently calling city number 2674:
    dom pedrito, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dom pedrito,br&units=imperial
    
    Currently calling city number 2675:
    blagoveshchenka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=blagoveshchenka,ru&units=imperial
    
    Currently calling city number 2676:
    tashara, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tashara,ru&units=imperial
    
    Currently calling city number 2677:
    coxim, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=coxim,br&units=imperial
    
    Currently calling city number 2678:
    voh, nc
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=voh,nc&units=imperial
    
    Currently calling city number 2679:
    santa cruz cabralia, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santa cruz cabralia,br&units=imperial
    
    Currently calling city number 2680:
    durham, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=durham,us&units=imperial
    
    Currently calling city number 2681:
    kranea elassonos, gr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kranea elassonos,gr&units=imperial
    
    Currently calling city number 2682:
    anloga, gh
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=anloga,gh&units=imperial
    
    Currently calling city number 2683:
    terme, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=terme,tr&units=imperial
    
    Currently calling city number 2684:
    rock sound, bs
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rock sound,bs&units=imperial
    
    Currently calling city number 2685:
    aurora, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aurora,br&units=imperial
    
    Currently calling city number 2686:
    trat, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=trat,th&units=imperial
    
    Currently calling city number 2687:
    puerto maldonado, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=puerto maldonado,pe&units=imperial
    
    Currently calling city number 2688:
    odienne, ci
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=odienne,ci&units=imperial
    
    Currently calling city number 2689:
    oeiras, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=oeiras,br&units=imperial
    
    Currently calling city number 2690:
    gympie, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gympie,au&units=imperial
    
    Currently calling city number 2691:
    iisalmi, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=iisalmi,fi&units=imperial
    
    Currently calling city number 2692:
    toamasina, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=toamasina,mg&units=imperial
    
    Currently calling city number 2693:
    pacifica, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pacifica,us&units=imperial
    
    Currently calling city number 2694:
    badulla, lk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=badulla,lk&units=imperial
    
    Currently calling city number 2695:
    lenine, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lenine,ua&units=imperial
    
    Currently calling city number 2696:
    marolambo, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=marolambo,mg&units=imperial
    
    Currently calling city number 2697:
    itarantim, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=itarantim,br&units=imperial
    
    Currently calling city number 2698:
    rtishchevo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rtishchevo,ru&units=imperial
    
    Currently calling city number 2699:
    harer, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=harer,et&units=imperial
    
    Currently calling city number 2700:
    dubna, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dubna,ru&units=imperial
    
    Currently calling city number 2701:
    bombay, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bombay,nz&units=imperial
    
    Currently calling city number 2702:
    beroroha, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=beroroha,mg&units=imperial
    
    Currently calling city number 2703:
    emirdag, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=emirdag,tr&units=imperial
    
    Currently calling city number 2704:
    bolgar, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bolgar,ru&units=imperial
    
    Currently calling city number 2705:
    gorno-altaysk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gorno-altaysk,ru&units=imperial
    
    Currently calling city number 2706:
    plastun, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=plastun,ru&units=imperial
    
    Currently calling city number 2707:
    fujin, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fujin,cn&units=imperial
    
    Currently calling city number 2708:
    north branch, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=north branch,us&units=imperial
    
    Currently calling city number 2709:
    rastolita, ro
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rastolita,ro&units=imperial
    
    Currently calling city number 2710:
    amherst, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=amherst,ca&units=imperial
    
    Currently calling city number 2711:
    yekaterinovka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yekaterinovka,ru&units=imperial
    
    Currently calling city number 2712:
    vieste, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vieste,it&units=imperial
    
    Currently calling city number 2713:
    kroya, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kroya,id&units=imperial
    
    Currently calling city number 2714:
    fayaoue, nc
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fayaoue,nc&units=imperial
    
    Currently calling city number 2715:
    kadambur, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kadambur,in&units=imperial
    
    Currently calling city number 2716:
    maiduguri, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=maiduguri,ng&units=imperial
    
    Currently calling city number 2717:
    rantepao, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rantepao,id&units=imperial
    
    Currently calling city number 2718:
    tahta, eg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tahta,eg&units=imperial
    
    Currently calling city number 2719:
    ovalle, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ovalle,cl&units=imperial
    
    Currently calling city number 2720:
    la serena, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=la serena,cl&units=imperial
    
    Currently calling city number 2721:
    shimanovsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shimanovsk,ru&units=imperial
    
    Currently calling city number 2722:
    curuca, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=curuca,br&units=imperial
    
    Currently calling city number 2723:
    palora, ec
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=palora,ec&units=imperial
    
    Currently calling city number 2724:
    ask, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ask,no&units=imperial
    
    Currently calling city number 2725:
    clifton, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=clifton,us&units=imperial
    
    Currently calling city number 2726:
    saint-michel-des-saints, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saint-michel-des-saints,ca&units=imperial
    
    Currently calling city number 2727:
    juegang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=juegang,cn&units=imperial
    
    Currently calling city number 2728:
    cernat, ro
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cernat,ro&units=imperial
    
    Currently calling city number 2729:
    cachoeira do sul, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cachoeira do sul,br&units=imperial
    
    Currently calling city number 2730:
    urucui, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=urucui,br&units=imperial
    
    Currently calling city number 2731:
    lumeje, ao
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lumeje,ao&units=imperial
    
    Currently calling city number 2732:
    pangai, to
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pangai,to&units=imperial
    
    Currently calling city number 2733:
    grenada, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=grenada,us&units=imperial
    
    Currently calling city number 2734:
    midland, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=midland,us&units=imperial
    
    Currently calling city number 2735:
    soubre, ci
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=soubre,ci&units=imperial
    
    Currently calling city number 2736:
    taunggyi, mm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=taunggyi,mm&units=imperial
    
    Currently calling city number 2737:
    aguas formosas, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aguas formosas,br&units=imperial
    
    Currently calling city number 2738:
    tandlianwala, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tandlianwala,pk&units=imperial
    
    Currently calling city number 2739:
    trabzon, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=trabzon,tr&units=imperial
    
    Currently calling city number 2740:
    itamarandiba, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=itamarandiba,br&units=imperial
    
    Currently calling city number 2741:
    ritto, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ritto,jp&units=imperial
    
    Currently calling city number 2742:
    moratuwa, lk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=moratuwa,lk&units=imperial
    
    Currently calling city number 2743:
    mirnyy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mirnyy,ru&units=imperial
    
    Currently calling city number 2744:
    bozuyuk, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bozuyuk,tr&units=imperial
    
    Currently calling city number 2745:
    caramay, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=caramay,ph&units=imperial
    
    Currently calling city number 2746:
    east wenatchee, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=east wenatchee,us&units=imperial
    
    Currently calling city number 2747:
    burlington, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=burlington,us&units=imperial
    
    Currently calling city number 2748:
    jackson, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jackson,us&units=imperial
    
    Currently calling city number 2749:
    chimore, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chimore,bo&units=imperial
    
    Currently calling city number 2750:
    chor, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chor,pk&units=imperial
    
    Currently calling city number 2751:
    satipo, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=satipo,pe&units=imperial
    
    Currently calling city number 2752:
    hecelchakan, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hecelchakan,mx&units=imperial
    
    Currently calling city number 2753:
    manvi, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=manvi,in&units=imperial
    
    Currently calling city number 2754:
    nederland, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nederland,us&units=imperial
    
    Currently calling city number 2755:
    gombong, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gombong,id&units=imperial
    
    Currently calling city number 2756:
    santa maria, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santa maria,br&units=imperial
    
    Currently calling city number 2757:
    buriti, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=buriti,br&units=imperial
    
    Currently calling city number 2758:
    messini, gr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=messini,gr&units=imperial
    
    Currently calling city number 2759:
    anjozorobe, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=anjozorobe,mg&units=imperial
    
    Currently calling city number 2760:
    paradise valley, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=paradise valley,us&units=imperial
    
    Currently calling city number 2761:
    poim, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=poim,ru&units=imperial
    
    Currently calling city number 2762:
    rock hill, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rock hill,us&units=imperial
    
    Currently calling city number 2763:
    conakry, gn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=conakry,gn&units=imperial
    
    Currently calling city number 2764:
    troianul, ro
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=troianul,ro&units=imperial
    
    Currently calling city number 2765:
    alliance, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alliance,us&units=imperial
    
    Currently calling city number 2766:
    chino valley, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chino valley,us&units=imperial
    
    Currently calling city number 2767:
    karoi, zw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=karoi,zw&units=imperial
    
    Currently calling city number 2768:
    kerchevskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kerchevskiy,ru&units=imperial
    
    Currently calling city number 2769:
    kungurtug, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kungurtug,ru&units=imperial
    
    Currently calling city number 2770:
    novyy svit, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=novyy svit,ua&units=imperial
    
    Currently calling city number 2771:
    enshi, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=enshi,cn&units=imperial
    
    Currently calling city number 2772:
    at-bashi, kg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=at-bashi,kg&units=imperial
    
    Currently calling city number 2773:
    olavarria, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=olavarria,ar&units=imperial
    
    Currently calling city number 2774:
    yuzhno-kurilsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yuzhno-kurilsk,ru&units=imperial
    
    Currently calling city number 2775:
    panzhihua, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=panzhihua,cn&units=imperial
    
    Currently calling city number 2776:
    tromso, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tromso,no&units=imperial
    
    Currently calling city number 2777:
    ballina, ie
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ballina,ie&units=imperial
    
    Currently calling city number 2778:
    bom jardim, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bom jardim,br&units=imperial
    
    Currently calling city number 2779:
    espinosa, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=espinosa,br&units=imperial
    
    Currently calling city number 2780:
    padrauna, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=padrauna,in&units=imperial
    
    Currently calling city number 2781:
    nyaunglebin, mm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nyaunglebin,mm&units=imperial
    
    Currently calling city number 2782:
    sakti, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sakti,in&units=imperial
    
    Currently calling city number 2783:
    gravelbourg, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gravelbourg,ca&units=imperial
    
    Currently calling city number 2784:
    severnoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=severnoye,ru&units=imperial
    
    Currently calling city number 2785:
    junction city, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=junction city,us&units=imperial
    
    Currently calling city number 2786:
    thunder bay, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=thunder bay,ca&units=imperial
    
    Currently calling city number 2787:
    khoy, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=khoy,ir&units=imperial
    
    Currently calling city number 2788:
    gazanjyk, tm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gazanjyk,tm&units=imperial
    
    Currently calling city number 2789:
    kingman, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kingman,us&units=imperial
    
    Currently calling city number 2790:
    jimma, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jimma,et&units=imperial
    
    Currently calling city number 2791:
    voghera, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=voghera,it&units=imperial
    
    Currently calling city number 2792:
    nagpur, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nagpur,in&units=imperial
    
    Currently calling city number 2793:
    wasilla, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wasilla,us&units=imperial
    
    Currently calling city number 2794:
    abbeville, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=abbeville,us&units=imperial
    
    Currently calling city number 2795:
    nyrob, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nyrob,ru&units=imperial
    
    Currently calling city number 2796:
    moramanga, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=moramanga,mg&units=imperial
    
    Currently calling city number 2797:
    ardabil, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ardabil,ir&units=imperial
    
    Currently calling city number 2798:
    porirua, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=porirua,nz&units=imperial
    
    Currently calling city number 2799:
    son la, vn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=son la,vn&units=imperial
    
    Currently calling city number 2800:
    mathbaria, bd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mathbaria,bd&units=imperial
    
    Currently calling city number 2801:
    el dorado, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=el dorado,us&units=imperial
    
    Currently calling city number 2802:
    nizwa, om
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nizwa,om&units=imperial
    
    Currently calling city number 2803:
    sao joao da ponte, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sao joao da ponte,br&units=imperial
    
    Currently calling city number 2804:
    lahij, ye
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lahij,ye&units=imperial
    
    Currently calling city number 2805:
    tayoltita, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tayoltita,mx&units=imperial
    
    Currently calling city number 2806:
    rio novo do sul, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rio novo do sul,br&units=imperial
    
    Currently calling city number 2807:
    terny, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=terny,ua&units=imperial
    
    Currently calling city number 2808:
    kamyshla, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kamyshla,ru&units=imperial
    
    Currently calling city number 2809:
    maicao, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=maicao,co&units=imperial
    
    Currently calling city number 2810:
    minas, uy
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=minas,uy&units=imperial
    
    Currently calling city number 2811:
    bogalusa, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bogalusa,us&units=imperial
    
    Currently calling city number 2812:
    gualeguay, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gualeguay,ar&units=imperial
    
    Currently calling city number 2813:
    bajos de haina, do
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bajos de haina,do&units=imperial
    
    Currently calling city number 2814:
    tongliao, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tongliao,cn&units=imperial
    
    Currently calling city number 2815:
    whyalla, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=whyalla,au&units=imperial
    
    Currently calling city number 2816:
    hargeysa, so
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hargeysa,so&units=imperial
    
    Currently calling city number 2817:
    sinjai, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sinjai,id&units=imperial
    
    Currently calling city number 2818:
    terney, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=terney,ru&units=imperial
    
    Currently calling city number 2819:
    kazerun, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kazerun,ir&units=imperial
    
    Currently calling city number 2820:
    zhaozhou, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zhaozhou,cn&units=imperial
    
    Currently calling city number 2821:
    krutikha, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=krutikha,ru&units=imperial
    
    Currently calling city number 2822:
    de aar, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=de aar,za&units=imperial
    
    Currently calling city number 2823:
    general bravo, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=general bravo,mx&units=imperial
    
    Currently calling city number 2824:
    langham, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=langham,ca&units=imperial
    
    Currently calling city number 2825:
    dubti, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dubti,et&units=imperial
    
    Currently calling city number 2826:
    moose factory, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=moose factory,ca&units=imperial
    
    Currently calling city number 2827:
    pirgos, gr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pirgos,gr&units=imperial
    
    Currently calling city number 2828:
    musenita, ro
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=musenita,ro&units=imperial
    
    Currently calling city number 2829:
    hoquiam, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hoquiam,us&units=imperial
    
    Currently calling city number 2830:
    midrand, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=midrand,za&units=imperial
    
    Currently calling city number 2831:
    osinovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=osinovo,ru&units=imperial
    
    Currently calling city number 2832:
    urzhum, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=urzhum,ru&units=imperial
    
    Currently calling city number 2833:
    santa rosa, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santa rosa,bo&units=imperial
    
    Currently calling city number 2834:
    kearney, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kearney,us&units=imperial
    
    Currently calling city number 2835:
    dongli, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dongli,cn&units=imperial
    
    Currently calling city number 2836:
    kibaya, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kibaya,tz&units=imperial
    
    Currently calling city number 2837:
    cukai, my
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cukai,my&units=imperial
    
    Currently calling city number 2838:
    wanganui, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wanganui,nz&units=imperial
    
    Currently calling city number 2839:
    were ilu, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=were ilu,et&units=imperial
    
    Currently calling city number 2840:
    mortka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mortka,ru&units=imperial
    
    Currently calling city number 2841:
    tornio, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tornio,fi&units=imperial
    
    Currently calling city number 2842:
    tiebissou, ci
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tiebissou,ci&units=imperial
    
    Currently calling city number 2843:
    san agustin acasaguastlan, gt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san agustin acasaguastlan,gt&units=imperial
    
    Currently calling city number 2844:
    kaduqli, sd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kaduqli,sd&units=imperial
    
    Currently calling city number 2845:
    oyama, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=oyama,jp&units=imperial
    
    Currently calling city number 2846:
    tandil, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tandil,ar&units=imperial
    
    Currently calling city number 2847:
    russas, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=russas,br&units=imperial
    
    Currently calling city number 2848:
    bertinoro, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bertinoro,it&units=imperial
    
    Currently calling city number 2849:
    kerman, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kerman,ir&units=imperial
    
    Currently calling city number 2850:
    esfarayen, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=esfarayen,ir&units=imperial
    
    Currently calling city number 2851:
    pyinmana, mm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pyinmana,mm&units=imperial
    
    Currently calling city number 2852:
    novyy urengoy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=novyy urengoy,ru&units=imperial
    
    Currently calling city number 2853:
    arroyo, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=arroyo,us&units=imperial
    
    Currently calling city number 2854:
    tumpat, my
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tumpat,my&units=imperial
    
    Currently calling city number 2855:
    simao, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=simao,cn&units=imperial
    
    Currently calling city number 2856:
    juybar, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=juybar,ir&units=imperial
    
    Currently calling city number 2857:
    balad, iq
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=balad,iq&units=imperial
    
    Currently calling city number 2858:
    tursunzoda, tj
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tursunzoda,tj&units=imperial
    
    Currently calling city number 2859:
    miramar, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=miramar,mx&units=imperial
    
    Currently calling city number 2860:
    pavilosta, lv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pavilosta,lv&units=imperial
    
    Currently calling city number 2861:
    tumwater, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tumwater,us&units=imperial
    
    Currently calling city number 2862:
    key largo, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=key largo,us&units=imperial
    
    Currently calling city number 2863:
    freeport, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=freeport,us&units=imperial
    
    Currently calling city number 2864:
    puerto quijarro, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=puerto quijarro,bo&units=imperial
    
    Currently calling city number 2865:
    tondano, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tondano,id&units=imperial
    
    Currently calling city number 2866:
    dera bugti, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dera bugti,pk&units=imperial
    
    Currently calling city number 2867:
    kamphaeng phet, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kamphaeng phet,th&units=imperial
    
    Currently calling city number 2868:
    dhankuta, np
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dhankuta,np&units=imperial
    
    Currently calling city number 2869:
    clearwater, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=clearwater,us&units=imperial
    
    Currently calling city number 2870:
    aloleng, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aloleng,ph&units=imperial
    
    Currently calling city number 2871:
    gizycko, pl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gizycko,pl&units=imperial
    
    Currently calling city number 2872:
    karur, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=karur,in&units=imperial
    
    Currently calling city number 2873:
    dzheguta, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dzheguta,ru&units=imperial
    
    Currently calling city number 2874:
    pankrushikha, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pankrushikha,ru&units=imperial
    
    Currently calling city number 2875:
    bacolod, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bacolod,ph&units=imperial
    
    Currently calling city number 2876:
    salamanca, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=salamanca,cl&units=imperial
    
    Currently calling city number 2877:
    wajir, ke
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wajir,ke&units=imperial
    
    Currently calling city number 2878:
    duluth, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=duluth,us&units=imperial
    
    Currently calling city number 2879:
    mazsalaca, lv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mazsalaca,lv&units=imperial
    
    Currently calling city number 2880:
    manzanillo, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=manzanillo,mx&units=imperial
    
    Currently calling city number 2881:
    luzino, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=luzino,ru&units=imperial
    
    Currently calling city number 2882:
    sovetskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sovetskiy,ru&units=imperial
    
    Currently calling city number 2883:
    jatiroto, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jatiroto,id&units=imperial
    
    Currently calling city number 2884:
    breyten, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=breyten,za&units=imperial
    
    Currently calling city number 2885:
    deulgaon raja, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=deulgaon raja,in&units=imperial
    
    Currently calling city number 2886:
    morwa, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=morwa,in&units=imperial
    
    Currently calling city number 2887:
    meadow lake, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=meadow lake,ca&units=imperial
    
    Currently calling city number 2888:
    rio claro, tt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rio claro,tt&units=imperial
    
    Currently calling city number 2889:
    rochegda, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rochegda,ru&units=imperial
    
    Currently calling city number 2890:
    ybycui, py
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ybycui,py&units=imperial
    
    Currently calling city number 2891:
    san jose de buan, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san jose de buan,ph&units=imperial
    
    Currently calling city number 2892:
    moissala, td
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=moissala,td&units=imperial
    
    Currently calling city number 2893:
    manono, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=manono,cd&units=imperial
    
    Currently calling city number 2894:
    kainantu, pg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kainantu,pg&units=imperial
    
    Currently calling city number 2895:
    ariquemes, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ariquemes,br&units=imperial
    
    Currently calling city number 2896:
    tarauaca, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tarauaca,br&units=imperial
    
    Currently calling city number 2897:
    kenitra, ma
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kenitra,ma&units=imperial
    
    Currently calling city number 2898:
    macamic, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=macamic,ca&units=imperial
    
    Currently calling city number 2899:
    esil, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=esil,kz&units=imperial
    
    Currently calling city number 2900:
    khasan, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=khasan,ru&units=imperial
    
    Currently calling city number 2901:
    ciudad guayana, ve
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ciudad guayana,ve&units=imperial
    
    Currently calling city number 2902:
    amurrio, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=amurrio,es&units=imperial
    
    Currently calling city number 2903:
    fairfield, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fairfield,us&units=imperial
    
    Currently calling city number 2904:
    torrox, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=torrox,es&units=imperial
    
    Currently calling city number 2905:
    palmares do sul, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=palmares do sul,br&units=imperial
    
    Currently calling city number 2906:
    marquette, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=marquette,us&units=imperial
    
    Currently calling city number 2907:
    gopalpur, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gopalpur,in&units=imperial
    
    Currently calling city number 2908:
    owosso, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=owosso,us&units=imperial
    
    Currently calling city number 2909:
    beaupre, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=beaupre,ca&units=imperial
    
    Currently calling city number 2910:
    puerto el triunfo, sv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=puerto el triunfo,sv&units=imperial
    
    Currently calling city number 2911:
    pauri, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pauri,in&units=imperial
    
    Currently calling city number 2912:
    nehalim, il
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nehalim,il&units=imperial
    
    Currently calling city number 2913:
    shchors, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shchors,ua&units=imperial
    
    Currently calling city number 2914:
    yueyang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yueyang,cn&units=imperial
    
    Currently calling city number 2915:
    presidente venceslau, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=presidente venceslau,br&units=imperial
    
    Currently calling city number 2916:
    aden, ye
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aden,ye&units=imperial
    
    Currently calling city number 2917:
    sedkyrkeshch, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sedkyrkeshch,ru&units=imperial
    
    Currently calling city number 2918:
    garrel, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=garrel,de&units=imperial
    
    Currently calling city number 2919:
    douglas, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=douglas,us&units=imperial
    
    Currently calling city number 2920:
    beaverlodge, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=beaverlodge,ca&units=imperial
    
    Currently calling city number 2921:
    tambacounda, sn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tambacounda,sn&units=imperial
    
    Currently calling city number 2922:
    kaniama, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kaniama,cd&units=imperial
    
    Currently calling city number 2923:
    can, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=can,tr&units=imperial
    
    Currently calling city number 2924:
    carthage, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=carthage,us&units=imperial
    
    Currently calling city number 2925:
    dawson creek, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dawson creek,ca&units=imperial
    
    Currently calling city number 2926:
    buriti alegre, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=buriti alegre,br&units=imperial
    
    Currently calling city number 2927:
    khatra, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=khatra,in&units=imperial
    
    Currently calling city number 2928:
    macau, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=macau,br&units=imperial
    
    Currently calling city number 2929:
    ubauro, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ubauro,pk&units=imperial
    
    Currently calling city number 2930:
    urubamba, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=urubamba,pe&units=imperial
    
    Currently calling city number 2931:
    chiang klang, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chiang klang,th&units=imperial
    
    Currently calling city number 2932:
    inverell, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=inverell,au&units=imperial
    
    Currently calling city number 2933:
    calbuco, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=calbuco,cl&units=imperial
    
    Currently calling city number 2934:
    juruti, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=juruti,br&units=imperial
    
    Currently calling city number 2935:
    mangan, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mangan,in&units=imperial
    
    Currently calling city number 2936:
    ngorongoro, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ngorongoro,tz&units=imperial
    
    Currently calling city number 2937:
    krasnoyarskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=krasnoyarskiy,ru&units=imperial
    
    Currently calling city number 2938:
    vorchdorf, at
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vorchdorf,at&units=imperial
    
    Currently calling city number 2939:
    dillon, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dillon,us&units=imperial
    
    Currently calling city number 2940:
    san mateo del mar, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san mateo del mar,mx&units=imperial
    
    Currently calling city number 2941:
    shelburne, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shelburne,ca&units=imperial
    
    Currently calling city number 2942:
    kouango, cf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kouango,cf&units=imperial
    
    Currently calling city number 2943:
    kadoma, zw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kadoma,zw&units=imperial
    
    Currently calling city number 2944:
    shushenskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shushenskoye,ru&units=imperial
    
    Currently calling city number 2945:
    sucre, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sucre,co&units=imperial
    
    Currently calling city number 2946:
    jiayuguan, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jiayuguan,cn&units=imperial
    
    Currently calling city number 2947:
    heze, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=heze,cn&units=imperial
    
    Currently calling city number 2948:
    merrill, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=merrill,us&units=imperial
    
    Currently calling city number 2949:
    ardakan, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ardakan,ir&units=imperial
    
    Currently calling city number 2950:
    moranbah, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=moranbah,au&units=imperial
    
    Currently calling city number 2951:
    zacatlan, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zacatlan,mx&units=imperial
    
    Currently calling city number 2952:
    beboto, td
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=beboto,td&units=imperial
    
    Currently calling city number 2953:
    puerto leguizamo, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=puerto leguizamo,co&units=imperial
    
    Currently calling city number 2954:
    svolvaer, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=svolvaer,no&units=imperial
    
    Currently calling city number 2955:
    banamba, ml
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=banamba,ml&units=imperial
    
    Currently calling city number 2956:
    segezha, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=segezha,ru&units=imperial
    
    Currently calling city number 2957:
    ternate, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ternate,id&units=imperial
    
    Currently calling city number 2958:
    qianan, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=qianan,cn&units=imperial
    
    Currently calling city number 2959:
    vermillion, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vermillion,us&units=imperial
    
    Currently calling city number 2960:
    aitape, pg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aitape,pg&units=imperial
    
    Currently calling city number 2961:
    muromtsevo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=muromtsevo,ru&units=imperial
    
    Currently calling city number 2962:
    cuyo, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cuyo,ph&units=imperial
    
    Currently calling city number 2963:
    puksoozero, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=puksoozero,ru&units=imperial
    
    Currently calling city number 2964:
    itoigawa, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=itoigawa,jp&units=imperial
    
    Currently calling city number 2965:
    tuensang, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tuensang,in&units=imperial
    
    Currently calling city number 2966:
    sotnikovskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sotnikovskoye,ru&units=imperial
    
    Currently calling city number 2967:
    chapleau, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chapleau,ca&units=imperial
    
    Currently calling city number 2968:
    mbinga, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mbinga,tz&units=imperial
    
    Currently calling city number 2969:
    kondoa, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kondoa,tz&units=imperial
    
    Currently calling city number 2970:
    ylivieska, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ylivieska,fi&units=imperial
    
    Currently calling city number 2971:
    madimba, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=madimba,tz&units=imperial
    
    Currently calling city number 2972:
    high rock, bs
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=high rock,bs&units=imperial
    
    Currently calling city number 2973:
    san borja, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san borja,bo&units=imperial
    
    Currently calling city number 2974:
    twentynine palms, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=twentynine palms,us&units=imperial
    
    Currently calling city number 2975:
    almeirim, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=almeirim,br&units=imperial
    
    Currently calling city number 2976:
    jatibonico, cu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jatibonico,cu&units=imperial
    
    Currently calling city number 2977:
    jonuta, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jonuta,mx&units=imperial
    
    Currently calling city number 2978:
    karaidel, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=karaidel,ru&units=imperial
    
    Currently calling city number 2979:
    luorong, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=luorong,cn&units=imperial
    
    Currently calling city number 2980:
    bracebridge, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bracebridge,ca&units=imperial
    
    Currently calling city number 2981:
    churachandpur, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=churachandpur,in&units=imperial
    
    Currently calling city number 2982:
    sijunjung, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sijunjung,id&units=imperial
    
    Currently calling city number 2983:
    bangassou, cf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bangassou,cf&units=imperial
    
    Currently calling city number 2984:
    thayetmyo, mm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=thayetmyo,mm&units=imperial
    
    Currently calling city number 2985:
    volchansk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=volchansk,ru&units=imperial
    
    Currently calling city number 2986:
    talcahuano, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=talcahuano,cl&units=imperial
    
    Currently calling city number 2987:
    vytegra, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vytegra,ru&units=imperial
    
    Currently calling city number 2988:
    owando, cg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=owando,cg&units=imperial
    
    Currently calling city number 2989:
    ciudad real, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ciudad real,es&units=imperial
    
    Currently calling city number 2990:
    ujhani, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ujhani,in&units=imperial
    
    Currently calling city number 2991:
    kaduy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kaduy,ru&units=imperial
    
    Currently calling city number 2992:
    kyren, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kyren,ru&units=imperial
    
    Currently calling city number 2993:
    miguelopolis, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=miguelopolis,br&units=imperial
    
    Currently calling city number 2994:
    moerai, pf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=moerai,pf&units=imperial
    
    Currently calling city number 2995:
    kautokeino, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kautokeino,no&units=imperial
    
    Currently calling city number 2996:
    stolin, by
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=stolin,by&units=imperial
    
    Currently calling city number 2997:
    uribia, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=uribia,co&units=imperial
    
    Currently calling city number 2998:
    madison, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=madison,us&units=imperial
    
    Currently calling city number 2999:
    katherine, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=katherine,au&units=imperial
    
    Currently calling city number 3000:
    srisailam, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=srisailam,in&units=imperial
    
    Currently calling city number 3001:
    mazamitla, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mazamitla,mx&units=imperial
    
    Currently calling city number 3002:
    elk point, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=elk point,ca&units=imperial
    
    Currently calling city number 3003:
    zyryanovsk, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zyryanovsk,kz&units=imperial
    
    Currently calling city number 3004:
    kaa-khem, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kaa-khem,ru&units=imperial
    
    Currently calling city number 3005:
    aldan, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aldan,ru&units=imperial
    
    Currently calling city number 3006:
    sukumo, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sukumo,jp&units=imperial
    
    Currently calling city number 3007:
    saraland, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saraland,us&units=imperial
    
    Currently calling city number 3008:
    takayama, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=takayama,jp&units=imperial
    
    Currently calling city number 3009:
    libertad, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=libertad,ph&units=imperial
    
    Currently calling city number 3010:
    tygda, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tygda,ru&units=imperial
    
    Currently calling city number 3011:
    rantauprapat, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rantauprapat,id&units=imperial
    
    Currently calling city number 3012:
    meyzieu, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=meyzieu,fr&units=imperial
    
    Currently calling city number 3013:
    zaraza, ve
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zaraza,ve&units=imperial
    
    Currently calling city number 3014:
    grootfontein, na
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=grootfontein,na&units=imperial
    
    Currently calling city number 3015:
    salvacion, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=salvacion,ph&units=imperial
    
    Currently calling city number 3016:
    sukhothai, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sukhothai,th&units=imperial
    
    Currently calling city number 3017:
    sernur, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sernur,ru&units=imperial
    
    Currently calling city number 3018:
    camaqua, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=camaqua,br&units=imperial
    
    Currently calling city number 3019:
    bukoba, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bukoba,tz&units=imperial
    
    Currently calling city number 3020:
    ust-tarka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ust-tarka,ru&units=imperial
    
    Currently calling city number 3021:
    waterloo, sl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=waterloo,sl&units=imperial
    
    Currently calling city number 3022:
    altamira, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=altamira,br&units=imperial
    
    Currently calling city number 3023:
    porosozero, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=porosozero,ru&units=imperial
    
    Currently calling city number 3024:
    fereydunshahr, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fereydunshahr,ir&units=imperial
    
    Currently calling city number 3025:
    aksaray, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aksaray,tr&units=imperial
    
    Currently calling city number 3026:
    ous, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ous,ru&units=imperial
    
    Currently calling city number 3027:
    sinegorskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sinegorskiy,ru&units=imperial
    
    Currently calling city number 3028:
    manali, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=manali,in&units=imperial
    
    Currently calling city number 3029:
    bumba, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bumba,cd&units=imperial
    
    Currently calling city number 3030:
    mariestad, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mariestad,se&units=imperial
    
    Currently calling city number 3031:
    qandala, so
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=qandala,so&units=imperial
    
    Currently calling city number 3032:
    coos bay, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=coos bay,us&units=imperial
    
    Currently calling city number 3033:
    babstovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=babstovo,ru&units=imperial
    
    Currently calling city number 3034:
    yuanping, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yuanping,cn&units=imperial
    
    Currently calling city number 3035:
    watertown, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=watertown,us&units=imperial
    
    Currently calling city number 3036:
    zigong, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zigong,cn&units=imperial
    
    Currently calling city number 3037:
    tefe, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tefe,br&units=imperial
    
    Currently calling city number 3038:
    ostashkov, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ostashkov,ru&units=imperial
    
    Currently calling city number 3039:
    sun city west, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sun city west,us&units=imperial
    
    Currently calling city number 3040:
    kakonko, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kakonko,tz&units=imperial
    
    Currently calling city number 3041:
    mackenzie, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mackenzie,ca&units=imperial
    
    Currently calling city number 3042:
    zatoka, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zatoka,ua&units=imperial
    
    Currently calling city number 3043:
    pasni, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pasni,pk&units=imperial
    
    Currently calling city number 3044:
    aracaju, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aracaju,br&units=imperial
    
    Currently calling city number 3045:
    basse-pointe, mq
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=basse-pointe,mq&units=imperial
    
    Currently calling city number 3046:
    temir, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=temir,kz&units=imperial
    
    Currently calling city number 3047:
    atagay, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=atagay,ru&units=imperial
    
    Currently calling city number 3048:
    almeria, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=almeria,es&units=imperial
    
    Currently calling city number 3049:
    witbank, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=witbank,za&units=imperial
    
    Currently calling city number 3050:
    changli, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=changli,cn&units=imperial
    
    Currently calling city number 3051:
    exeter, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=exeter,us&units=imperial
    
    Currently calling city number 3052:
    saint-paul-les-dax, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saint-paul-les-dax,fr&units=imperial
    
    Currently calling city number 3053:
    malumfashi, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=malumfashi,ng&units=imperial
    
    Currently calling city number 3054:
    porto de moz, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=porto de moz,br&units=imperial
    
    Currently calling city number 3055:
    sabzevar, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sabzevar,ir&units=imperial
    
    Currently calling city number 3056:
    smoky lake, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=smoky lake,ca&units=imperial
    
    Currently calling city number 3057:
    aneho, tg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aneho,tg&units=imperial
    
    Currently calling city number 3058:
    pangkalanbuun, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pangkalanbuun,id&units=imperial
    
    Currently calling city number 3059:
    wajid, so
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wajid,so&units=imperial
    
    Currently calling city number 3060:
    hvide sande, dk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hvide sande,dk&units=imperial
    
    Currently calling city number 3061:
    nimaparha, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nimaparha,in&units=imperial
    
    Currently calling city number 3062:
    assiniboia, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=assiniboia,ca&units=imperial
    
    Currently calling city number 3063:
    yining, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yining,cn&units=imperial
    
    Currently calling city number 3064:
    sherlovaya gora, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sherlovaya gora,ru&units=imperial
    
    Currently calling city number 3065:
    damavand, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=damavand,ir&units=imperial
    
    Currently calling city number 3066:
    izhma, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=izhma,ru&units=imperial
    
    Currently calling city number 3067:
    reforma, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=reforma,mx&units=imperial
    
    Currently calling city number 3068:
    porto velho, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=porto velho,br&units=imperial
    
    Currently calling city number 3069:
    lugazi, ug
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lugazi,ug&units=imperial
    
    Currently calling city number 3070:
    camacupa, ao
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=camacupa,ao&units=imperial
    
    Currently calling city number 3071:
    kitui, ke
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kitui,ke&units=imperial
    
    Currently calling city number 3072:
    king city, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=king city,us&units=imperial
    
    Currently calling city number 3073:
    ribas do rio pardo, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ribas do rio pardo,br&units=imperial
    
    Currently calling city number 3074:
    mali, gn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mali,gn&units=imperial
    
    Currently calling city number 3075:
    hamilton, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hamilton,au&units=imperial
    
    Currently calling city number 3076:
    maningrida, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=maningrida,au&units=imperial
    
    Currently calling city number 3077:
    kremenki, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kremenki,ru&units=imperial
    
    Currently calling city number 3078:
    keta, gh
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=keta,gh&units=imperial
    
    Currently calling city number 3079:
    phek, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=phek,in&units=imperial
    
    Currently calling city number 3080:
    jinsha, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jinsha,cn&units=imperial
    
    Currently calling city number 3081:
    carmarthen, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=carmarthen,gb&units=imperial
    
    Currently calling city number 3082:
    carbonear, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=carbonear,ca&units=imperial
    
    Currently calling city number 3083:
    shar, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shar,kz&units=imperial
    
    Currently calling city number 3084:
    manadhoo, mv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=manadhoo,mv&units=imperial
    
    Currently calling city number 3085:
    damietta, eg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=damietta,eg&units=imperial
    
    Currently calling city number 3086:
    lira, ug
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lira,ug&units=imperial
    
    Currently calling city number 3087:
    bronnoysund, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bronnoysund,no&units=imperial
    
    Currently calling city number 3088:
    ibirite, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ibirite,br&units=imperial
    
    Currently calling city number 3089:
    carinhanha, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=carinhanha,br&units=imperial
    
    Currently calling city number 3090:
    knysna, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=knysna,za&units=imperial
    
    Currently calling city number 3091:
    hong gai, vn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hong gai,vn&units=imperial
    
    Currently calling city number 3092:
    balakhta, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=balakhta,ru&units=imperial
    
    Currently calling city number 3093:
    malpe, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=malpe,in&units=imperial
    
    Currently calling city number 3094:
    hongjiang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hongjiang,cn&units=imperial
    
    Currently calling city number 3095:
    macroom, ie
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=macroom,ie&units=imperial
    
    Currently calling city number 3096:
    kraluv dvur, cz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kraluv dvur,cz&units=imperial
    
    Currently calling city number 3097:
    green river, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=green river,us&units=imperial
    
    Currently calling city number 3098:
    sheregesh, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sheregesh,ru&units=imperial
    
    Currently calling city number 3099:
    whitehaven, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=whitehaven,gb&units=imperial
    
    Currently calling city number 3100:
    mwene-ditu, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mwene-ditu,cd&units=imperial
    
    Currently calling city number 3101:
    veseloyarsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=veseloyarsk,ru&units=imperial
    
    Currently calling city number 3102:
    eten, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=eten,pe&units=imperial
    
    Currently calling city number 3103:
    armizonskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=armizonskoye,ru&units=imperial
    
    Currently calling city number 3104:
    shu, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shu,kz&units=imperial
    
    Currently calling city number 3105:
    soligalich, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=soligalich,ru&units=imperial
    
    Currently calling city number 3106:
    loutros, gr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=loutros,gr&units=imperial
    
    Currently calling city number 3107:
    sassenberg, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sassenberg,de&units=imperial
    
    Currently calling city number 3108:
    mamaku, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mamaku,nz&units=imperial
    
    Currently calling city number 3109:
    hell, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hell,no&units=imperial
    
    Currently calling city number 3110:
    debre tabor, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=debre tabor,et&units=imperial
    
    Currently calling city number 3111:
    kotido, ug
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kotido,ug&units=imperial
    
    Currently calling city number 3112:
    darhan, mn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=darhan,mn&units=imperial
    
    Currently calling city number 3113:
    longkou, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=longkou,cn&units=imperial
    
    Currently calling city number 3114:
    cruzeiro do sul, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cruzeiro do sul,br&units=imperial
    
    Currently calling city number 3115:
    copperas cove, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=copperas cove,us&units=imperial
    
    Currently calling city number 3116:
    nusaybin, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nusaybin,tr&units=imperial
    
    Currently calling city number 3117:
    kingussie, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kingussie,gb&units=imperial
    
    Currently calling city number 3118:
    fatehpur, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fatehpur,in&units=imperial
    
    Currently calling city number 3119:
    kollam, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kollam,in&units=imperial
    
    Currently calling city number 3120:
    santa cruz del sur, cu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santa cruz del sur,cu&units=imperial
    
    Currently calling city number 3121:
    rockport, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rockport,us&units=imperial
    
    Currently calling city number 3122:
    letpadan, mm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=letpadan,mm&units=imperial
    
    Currently calling city number 3123:
    bucarasica, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bucarasica,co&units=imperial
    
    Currently calling city number 3124:
    feijo, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=feijo,br&units=imperial
    
    Currently calling city number 3125:
    yakeshi, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yakeshi,cn&units=imperial
    
    Currently calling city number 3126:
    callaway, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=callaway,us&units=imperial
    
    Currently calling city number 3127:
    vesele, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vesele,ua&units=imperial
    
    Currently calling city number 3128:
    kraskino, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kraskino,ru&units=imperial
    
    Currently calling city number 3129:
    maraa, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=maraa,br&units=imperial
    
    Currently calling city number 3130:
    petropavlovka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=petropavlovka,ru&units=imperial
    
    Currently calling city number 3131:
    viseu, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=viseu,br&units=imperial
    
    Currently calling city number 3132:
    brazzaville, cg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=brazzaville,cg&units=imperial
    
    Currently calling city number 3133:
    bridlington, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bridlington,gb&units=imperial
    
    Currently calling city number 3134:
    middleton, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=middleton,ca&units=imperial
    
    Currently calling city number 3135:
    baracoa, cu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=baracoa,cu&units=imperial
    
    Currently calling city number 3136:
    sault sainte marie, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sault sainte marie,ca&units=imperial
    
    Currently calling city number 3137:
    fuzhou, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fuzhou,cn&units=imperial
    
    Currently calling city number 3138:
    lewisville, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lewisville,us&units=imperial
    
    Currently calling city number 3139:
    abashiri, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=abashiri,jp&units=imperial
    
    Currently calling city number 3140:
    punto fijo, ve
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=punto fijo,ve&units=imperial
    
    Currently calling city number 3141:
    angren, uz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=angren,uz&units=imperial
    
    Currently calling city number 3142:
    cravo norte, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cravo norte,co&units=imperial
    
    Currently calling city number 3143:
    vokhtoga, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vokhtoga,ru&units=imperial
    
    Currently calling city number 3144:
    rio tercero, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rio tercero,ar&units=imperial
    
    Currently calling city number 3145:
    copacabana, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=copacabana,bo&units=imperial
    
    Currently calling city number 3146:
    dalbandin, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dalbandin,pk&units=imperial
    
    Currently calling city number 3147:
    abai, py
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=abai,py&units=imperial
    
    Currently calling city number 3148:
    tongling, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tongling,cn&units=imperial
    
    Currently calling city number 3149:
    thomaston, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=thomaston,us&units=imperial
    
    Currently calling city number 3150:
    mae ramat, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mae ramat,th&units=imperial
    
    Currently calling city number 3151:
    haldia, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=haldia,in&units=imperial
    
    Currently calling city number 3152:
    san luis, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san luis,co&units=imperial
    
    Currently calling city number 3153:
    don sak, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=don sak,th&units=imperial
    
    Currently calling city number 3154:
    pozoblanco, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pozoblanco,es&units=imperial
    
    Currently calling city number 3155:
    wainwright, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wainwright,ca&units=imperial
    
    Currently calling city number 3156:
    sabancuy, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sabancuy,mx&units=imperial
    
    Currently calling city number 3157:
    salta, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=salta,ar&units=imperial
    
    Currently calling city number 3158:
    langsa, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=langsa,id&units=imperial
    
    Currently calling city number 3159:
    kandrian, pg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kandrian,pg&units=imperial
    
    Currently calling city number 3160:
    grand bank, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=grand bank,ca&units=imperial
    
    Currently calling city number 3161:
    gorom-gorom, bf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gorom-gorom,bf&units=imperial
    
    Currently calling city number 3162:
    salvador, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=salvador,br&units=imperial
    
    Currently calling city number 3163:
    alekseyevsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alekseyevsk,ru&units=imperial
    
    Currently calling city number 3164:
    ulundi, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ulundi,za&units=imperial
    
    Currently calling city number 3165:
    neftcala, az
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=neftcala,az&units=imperial
    
    Currently calling city number 3166:
    bograd, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bograd,ru&units=imperial
    
    Currently calling city number 3167:
    mala, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mala,pe&units=imperial
    
    Currently calling city number 3168:
    idil, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=idil,tr&units=imperial
    
    Currently calling city number 3169:
    vagamo, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vagamo,no&units=imperial
    
    Currently calling city number 3170:
    karla, ee
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=karla,ee&units=imperial
    
    Currently calling city number 3171:
    barnbach, at
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=barnbach,at&units=imperial
    
    Currently calling city number 3172:
    gainesville, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gainesville,us&units=imperial
    
    Currently calling city number 3173:
    nandu, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nandu,cn&units=imperial
    
    Currently calling city number 3174:
    pouso alegre, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pouso alegre,br&units=imperial
    
    Currently calling city number 3175:
    maloy, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=maloy,no&units=imperial
    
    Currently calling city number 3176:
    tarnow, pl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tarnow,pl&units=imperial
    
    Currently calling city number 3177:
    kaabong, ug
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kaabong,ug&units=imperial
    
    Currently calling city number 3178:
    yuncheng, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yuncheng,cn&units=imperial
    
    Currently calling city number 3179:
    catuday, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=catuday,ph&units=imperial
    
    Currently calling city number 3180:
    mulege, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mulege,mx&units=imperial
    
    Currently calling city number 3181:
    kreuztal, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kreuztal,de&units=imperial
    
    Currently calling city number 3182:
    lawrenceburg, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lawrenceburg,us&units=imperial
    
    Currently calling city number 3183:
    novosheshminsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=novosheshminsk,ru&units=imperial
    
    Currently calling city number 3184:
    abramovka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=abramovka,ru&units=imperial
    
    Currently calling city number 3185:
    mizan teferi, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mizan teferi,et&units=imperial
    
    Currently calling city number 3186:
    peterhead, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=peterhead,gb&units=imperial
    
    Currently calling city number 3187:
    qaracala, az
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=qaracala,az&units=imperial
    
    Currently calling city number 3188:
    buchmany, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=buchmany,ua&units=imperial
    
    Currently calling city number 3189:
    chiusi, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chiusi,it&units=imperial
    
    Currently calling city number 3190:
    el guamo, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=el guamo,co&units=imperial
    
    Currently calling city number 3191:
    hwange, zw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hwange,zw&units=imperial
    
    Currently calling city number 3192:
    loviisa, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=loviisa,fi&units=imperial
    
    Currently calling city number 3193:
    barela, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=barela,in&units=imperial
    
    Currently calling city number 3194:
    seguela, ci
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=seguela,ci&units=imperial
    
    Currently calling city number 3195:
    kimberley, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kimberley,za&units=imperial
    
    Currently calling city number 3196:
    guilin, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=guilin,cn&units=imperial
    
    Currently calling city number 3197:
    gilgit, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gilgit,pk&units=imperial
    
    Currently calling city number 3198:
    sao jose dos campos, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sao jose dos campos,br&units=imperial
    
    Currently calling city number 3199:
    la malbaie, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=la malbaie,ca&units=imperial
    
    Currently calling city number 3200:
    kashi, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kashi,cn&units=imperial
    
    Currently calling city number 3201:
    itapeva, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=itapeva,br&units=imperial
    
    Currently calling city number 3202:
    rio brilhante, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rio brilhante,br&units=imperial
    
    Currently calling city number 3203:
    guatire, ve
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=guatire,ve&units=imperial
    
    Currently calling city number 3204:
    tashtagol, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tashtagol,ru&units=imperial
    
    Currently calling city number 3205:
    saint-raphael, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saint-raphael,fr&units=imperial
    
    Currently calling city number 3206:
    bo phloi, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bo phloi,th&units=imperial
    
    Currently calling city number 3207:
    jawa, jo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jawa,jo&units=imperial
    
    Currently calling city number 3208:
    athy, ie
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=athy,ie&units=imperial
    
    Currently calling city number 3209:
    rochelle, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rochelle,us&units=imperial
    
    Currently calling city number 3210:
    laramie, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=laramie,us&units=imperial
    
    Currently calling city number 3211:
    carberry, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=carberry,ca&units=imperial
    
    Currently calling city number 3212:
    buribay, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=buribay,ru&units=imperial
    
    Currently calling city number 3213:
    dembi dolo, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dembi dolo,et&units=imperial
    
    Currently calling city number 3214:
    chaihe, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chaihe,cn&units=imperial
    
    Currently calling city number 3215:
    surab, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=surab,pk&units=imperial
    
    Currently calling city number 3216:
    biaora, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=biaora,in&units=imperial
    
    Currently calling city number 3217:
    sawtell, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sawtell,au&units=imperial
    
    Currently calling city number 3218:
    westlock, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=westlock,ca&units=imperial
    
    Currently calling city number 3219:
    ystad, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ystad,se&units=imperial
    
    Currently calling city number 3220:
    tambo, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tambo,pe&units=imperial
    
    Currently calling city number 3221:
    samos, gr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=samos,gr&units=imperial
    
    Currently calling city number 3222:
    tsabong, bw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tsabong,bw&units=imperial
    
    Currently calling city number 3223:
    sivas, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sivas,tr&units=imperial
    
    Currently calling city number 3224:
    hikari, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hikari,jp&units=imperial
    
    Currently calling city number 3225:
    borovskoy, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=borovskoy,kz&units=imperial
    
    Currently calling city number 3226:
    numan, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=numan,ng&units=imperial
    
    Currently calling city number 3227:
    osorheiu, ro
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=osorheiu,ro&units=imperial
    
    Currently calling city number 3228:
    tulle, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tulle,fr&units=imperial
    
    Currently calling city number 3229:
    kafanchan, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kafanchan,ng&units=imperial
    
    Currently calling city number 3230:
    daltenganj, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=daltenganj,in&units=imperial
    
    Currently calling city number 3231:
    myanaung, mm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=myanaung,mm&units=imperial
    
    Currently calling city number 3232:
    zavallya, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zavallya,ua&units=imperial
    
    Currently calling city number 3233:
    kemin, kg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kemin,kg&units=imperial
    
    Currently calling city number 3234:
    saint-malo, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saint-malo,fr&units=imperial
    
    Currently calling city number 3235:
    armidale, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=armidale,au&units=imperial
    
    Currently calling city number 3236:
    uige, ao
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=uige,ao&units=imperial
    
    Currently calling city number 3237:
    polovinnoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=polovinnoye,ru&units=imperial
    
    Currently calling city number 3238:
    calmar, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=calmar,ca&units=imperial
    
    Currently calling city number 3239:
    kingisepp, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kingisepp,ru&units=imperial
    
    Currently calling city number 3240:
    sierra vista, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sierra vista,us&units=imperial
    
    Currently calling city number 3241:
    alabat, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alabat,ph&units=imperial
    
    Currently calling city number 3242:
    tyup, kg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tyup,kg&units=imperial
    
    Currently calling city number 3243:
    quang ngai, vn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=quang ngai,vn&units=imperial
    
    Currently calling city number 3244:
    curvelo, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=curvelo,br&units=imperial
    
    Currently calling city number 3245:
    ndago, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ndago,tz&units=imperial
    
    Currently calling city number 3246:
    lincoln, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lincoln,nz&units=imperial
    
    Currently calling city number 3247:
    vagay, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vagay,ru&units=imperial
    
    Currently calling city number 3248:
    djibo, bf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=djibo,bf&units=imperial
    
    Currently calling city number 3249:
    leshukonskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=leshukonskoye,ru&units=imperial
    
    Currently calling city number 3250:
    mahdalynivka, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mahdalynivka,ua&units=imperial
    
    Currently calling city number 3251:
    nagato, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nagato,jp&units=imperial
    
    Currently calling city number 3252:
    paris, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=paris,us&units=imperial
    
    Currently calling city number 3253:
    pierre, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pierre,us&units=imperial
    
    Currently calling city number 3254:
    montego bay, jm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=montego bay,jm&units=imperial
    
    Currently calling city number 3255:
    mogok, mm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mogok,mm&units=imperial
    
    Currently calling city number 3256:
    sikandarpur, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sikandarpur,in&units=imperial
    
    Currently calling city number 3257:
    aqtobe, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aqtobe,kz&units=imperial
    
    Currently calling city number 3258:
    poso, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=poso,id&units=imperial
    
    Currently calling city number 3259:
    araxa, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=araxa,br&units=imperial
    
    Currently calling city number 3260:
    kui buri, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kui buri,th&units=imperial
    
    Currently calling city number 3261:
    lyuban, by
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lyuban,by&units=imperial
    
    Currently calling city number 3262:
    caucaia, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=caucaia,br&units=imperial
    
    Currently calling city number 3263:
    marica, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=marica,br&units=imperial
    
    Currently calling city number 3264:
    shahrud, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shahrud,ir&units=imperial
    
    Currently calling city number 3265:
    munai, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=munai,ph&units=imperial
    
    Currently calling city number 3266:
    dryden, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dryden,ca&units=imperial
    
    Currently calling city number 3267:
    hauterive, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hauterive,ca&units=imperial
    
    Currently calling city number 3268:
    nova olimpia, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nova olimpia,br&units=imperial
    
    Currently calling city number 3269:
    awallan, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=awallan,ph&units=imperial
    
    Currently calling city number 3270:
    nizhniy tsasuchey, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nizhniy tsasuchey,ru&units=imperial
    
    Currently calling city number 3271:
    luoyang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=luoyang,cn&units=imperial
    
    Currently calling city number 3272:
    kokkola, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kokkola,fi&units=imperial
    
    Currently calling city number 3273:
    norwich, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=norwich,gb&units=imperial
    
    Currently calling city number 3274:
    mandurah, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mandurah,au&units=imperial
    
    Currently calling city number 3275:
    marathon, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=marathon,us&units=imperial
    
    Currently calling city number 3276:
    yima, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yima,cn&units=imperial
    
    Currently calling city number 3277:
    troy, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=troy,us&units=imperial
    
    Currently calling city number 3278:
    oeiras do para, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=oeiras do para,br&units=imperial
    
    Currently calling city number 3279:
    ust-kalmanka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ust-kalmanka,ru&units=imperial
    
    Currently calling city number 3280:
    camacari, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=camacari,br&units=imperial
    
    Currently calling city number 3281:
    zambezi, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zambezi,zm&units=imperial
    
    Currently calling city number 3282:
    jadu, ly
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jadu,ly&units=imperial
    
    Currently calling city number 3283:
    lichtenburg, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lichtenburg,za&units=imperial
    
    Currently calling city number 3284:
    stephenville, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=stephenville,ca&units=imperial
    
    Currently calling city number 3285:
    kyzyl-mazhalyk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kyzyl-mazhalyk,ru&units=imperial
    
    Currently calling city number 3286:
    lerwick, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lerwick,gb&units=imperial
    
    Currently calling city number 3287:
    policoro, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=policoro,it&units=imperial
    
    Currently calling city number 3288:
    pionerskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pionerskiy,ru&units=imperial
    
    Currently calling city number 3289:
    farah, af
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=farah,af&units=imperial
    
    Currently calling city number 3290:
    syracuse, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=syracuse,us&units=imperial
    
    Currently calling city number 3291:
    miles city, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=miles city,us&units=imperial
    
    Currently calling city number 3292:
    tyukhtet, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tyukhtet,ru&units=imperial
    
    Currently calling city number 3293:
    tolaga bay, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tolaga bay,nz&units=imperial
    
    Currently calling city number 3294:
    kalyazin, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kalyazin,ru&units=imperial
    
    Currently calling city number 3295:
    nicoya, cr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nicoya,cr&units=imperial
    
    Currently calling city number 3296:
    krasnystaw, pl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=krasnystaw,pl&units=imperial
    
    Currently calling city number 3297:
    jeremoabo, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jeremoabo,br&units=imperial
    
    Currently calling city number 3298:
    nevelsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nevelsk,ru&units=imperial
    
    Currently calling city number 3299:
    pucallpa, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pucallpa,pe&units=imperial
    
    Currently calling city number 3300:
    gambela, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gambela,et&units=imperial
    
    Currently calling city number 3301:
    zaton, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zaton,ru&units=imperial
    
    Currently calling city number 3302:
    moyobamba, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=moyobamba,pe&units=imperial
    
    Currently calling city number 3303:
    melville, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=melville,ca&units=imperial
    
    Currently calling city number 3304:
    kodar, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kodar,in&units=imperial
    
    Currently calling city number 3305:
    kumphawapi, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kumphawapi,th&units=imperial
    
    Currently calling city number 3306:
    jutai, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jutai,br&units=imperial
    
    Currently calling city number 3307:
    libenge, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=libenge,cd&units=imperial
    
    Currently calling city number 3308:
    liliani, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=liliani,pk&units=imperial
    
    Currently calling city number 3309:
    saint george, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saint george,us&units=imperial
    
    Currently calling city number 3310:
    bastrop, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bastrop,us&units=imperial
    
    Currently calling city number 3311:
    kulu, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kulu,tr&units=imperial
    
    Currently calling city number 3312:
    jurado, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jurado,co&units=imperial
    
    Currently calling city number 3313:
    ust-uda, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ust-uda,ru&units=imperial
    
    Currently calling city number 3314:
    lokoja, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lokoja,ng&units=imperial
    
    Currently calling city number 3315:
    el banco, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=el banco,co&units=imperial
    
    Currently calling city number 3316:
    datong, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=datong,cn&units=imperial
    
    Currently calling city number 3317:
    sukabumi, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sukabumi,id&units=imperial
    
    Currently calling city number 3318:
    puntarenas, cr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=puntarenas,cr&units=imperial
    
    Currently calling city number 3319:
    jaisalmer, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jaisalmer,in&units=imperial
    
    Currently calling city number 3320:
    kochi, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kochi,in&units=imperial
    
    Currently calling city number 3321:
    uray, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=uray,ru&units=imperial
    
    Currently calling city number 3322:
    taveta, ke
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=taveta,ke&units=imperial
    
    Currently calling city number 3323:
    randazzo, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=randazzo,it&units=imperial
    
    Currently calling city number 3324:
    urbana, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=urbana,us&units=imperial
    
    Currently calling city number 3325:
    angoram, pg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=angoram,pg&units=imperial
    
    Currently calling city number 3326:
    jitauna, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jitauna,br&units=imperial
    
    Currently calling city number 3327:
    ladario, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ladario,br&units=imperial
    
    Currently calling city number 3328:
    santiago, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santiago,ph&units=imperial
    
    Currently calling city number 3329:
    kinsale, ie
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kinsale,ie&units=imperial
    
    Currently calling city number 3330:
    kualakapuas, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kualakapuas,id&units=imperial
    
    Currently calling city number 3331:
    staroaleyskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=staroaleyskoye,ru&units=imperial
    
    Currently calling city number 3332:
    rongcheng, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rongcheng,cn&units=imperial
    
    Currently calling city number 3333:
    radishchevo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=radishchevo,ru&units=imperial
    
    Currently calling city number 3334:
    farafangana, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=farafangana,mg&units=imperial
    
    Currently calling city number 3335:
    myitkyina, mm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=myitkyina,mm&units=imperial
    
    Currently calling city number 3336:
    dargaville, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dargaville,nz&units=imperial
    
    Currently calling city number 3337:
    shenzhen, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shenzhen,cn&units=imperial
    
    Currently calling city number 3338:
    yelandur, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yelandur,in&units=imperial
    
    Currently calling city number 3339:
    murdochville, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=murdochville,ca&units=imperial
    
    Currently calling city number 3340:
    barnstaple, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=barnstaple,gb&units=imperial
    
    Currently calling city number 3341:
    takapau, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=takapau,nz&units=imperial
    
    Currently calling city number 3342:
    yuyao, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yuyao,cn&units=imperial
    
    Currently calling city number 3343:
    molchanovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=molchanovo,ru&units=imperial
    
    Currently calling city number 3344:
    onguday, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=onguday,ru&units=imperial
    
    Currently calling city number 3345:
    verkhovazhye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=verkhovazhye,ru&units=imperial
    
    Currently calling city number 3346:
    kamen-rybolov, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kamen-rybolov,ru&units=imperial
    
    Currently calling city number 3347:
    lucapa, ao
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lucapa,ao&units=imperial
    
    Currently calling city number 3348:
    cochrane, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cochrane,ca&units=imperial
    
    Currently calling city number 3349:
    maralal, ke
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=maralal,ke&units=imperial
    
    Currently calling city number 3350:
    angermunde, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=angermunde,de&units=imperial
    
    Currently calling city number 3351:
    santa vitoria do palmar, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santa vitoria do palmar,br&units=imperial
    
    Currently calling city number 3352:
    hagere hiywet, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hagere hiywet,et&units=imperial
    
    Currently calling city number 3353:
    laranja da terra, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=laranja da terra,br&units=imperial
    
    Currently calling city number 3354:
    uruzgan, af
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=uruzgan,af&units=imperial
    
    Currently calling city number 3355:
    koulamoutou, ga
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=koulamoutou,ga&units=imperial
    
    Currently calling city number 3356:
    skotterud, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=skotterud,no&units=imperial
    
    Currently calling city number 3357:
    cocobeach, ga
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cocobeach,ga&units=imperial
    
    Currently calling city number 3358:
    narrabri, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=narrabri,au&units=imperial
    
    Currently calling city number 3359:
    cabot, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cabot,us&units=imperial
    
    Currently calling city number 3360:
    mpanda, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mpanda,tz&units=imperial
    
    Currently calling city number 3361:
    wewak, pg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wewak,pg&units=imperial
    
    Currently calling city number 3362:
    akureyri, is
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=akureyri,is&units=imperial
    
    Currently calling city number 3363:
    santa comba, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santa comba,es&units=imperial
    
    Currently calling city number 3364:
    doka, sd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=doka,sd&units=imperial
    
    Currently calling city number 3365:
    gretna, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gretna,gb&units=imperial
    
    Currently calling city number 3366:
    alta, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alta,no&units=imperial
    
    Currently calling city number 3367:
    marau, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=marau,br&units=imperial
    
    Currently calling city number 3368:
    medford, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=medford,us&units=imperial
    
    Currently calling city number 3369:
    aztec, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aztec,us&units=imperial
    
    Currently calling city number 3370:
    jupiter, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jupiter,us&units=imperial
    
    Currently calling city number 3371:
    kommunar, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kommunar,ru&units=imperial
    
    Currently calling city number 3372:
    leland, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=leland,no&units=imperial
    
    Currently calling city number 3373:
    pella, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pella,us&units=imperial
    
    Currently calling city number 3374:
    jizan, sa
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jizan,sa&units=imperial
    
    Currently calling city number 3375:
    aiquile, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aiquile,bo&units=imperial
    
    Currently calling city number 3376:
    cave spring, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cave spring,us&units=imperial
    
    Currently calling city number 3377:
    zahnitkiv, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zahnitkiv,ua&units=imperial
    
    Currently calling city number 3378:
    jinotega, ni
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jinotega,ni&units=imperial
    
    Currently calling city number 3379:
    tonantins, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tonantins,br&units=imperial
    
    Currently calling city number 3380:
    tucano, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tucano,br&units=imperial
    
    Currently calling city number 3381:
    marsaxlokk, mt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=marsaxlokk,mt&units=imperial
    
    Currently calling city number 3382:
    qinhuangdao, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=qinhuangdao,cn&units=imperial
    
    Currently calling city number 3383:
    raduzhnyy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=raduzhnyy,ru&units=imperial
    
    Currently calling city number 3384:
    saint andrews, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saint andrews,ca&units=imperial
    
    Currently calling city number 3385:
    shangzhi, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shangzhi,cn&units=imperial
    
    Currently calling city number 3386:
    caraballeda, ve
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=caraballeda,ve&units=imperial
    
    Currently calling city number 3387:
    valdivia, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=valdivia,cl&units=imperial
    
    Currently calling city number 3388:
    luwingu, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=luwingu,zm&units=imperial
    
    Currently calling city number 3389:
    kolondieba, ml
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kolondieba,ml&units=imperial
    
    Currently calling city number 3390:
    bokajan, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bokajan,in&units=imperial
    
    Currently calling city number 3391:
    gboko, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gboko,ng&units=imperial
    
    Currently calling city number 3392:
    bouloupari, nc
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bouloupari,nc&units=imperial
    
    Currently calling city number 3393:
    krosno odrzanskie, pl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=krosno odrzanskie,pl&units=imperial
    
    Currently calling city number 3394:
    visby, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=visby,se&units=imperial
    
    Currently calling city number 3395:
    gonda, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gonda,in&units=imperial
    
    Currently calling city number 3396:
    dzialdowo, pl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dzialdowo,pl&units=imperial
    
    Currently calling city number 3397:
    irece, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=irece,br&units=imperial
    
    Currently calling city number 3398:
    landau, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=landau,de&units=imperial
    
    Currently calling city number 3399:
    dong hoi, vn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dong hoi,vn&units=imperial
    
    Currently calling city number 3400:
    kurmanayevka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kurmanayevka,ru&units=imperial
    
    Currently calling city number 3401:
    dalvik, is
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dalvik,is&units=imperial
    
    Currently calling city number 3402:
    ratnagiri, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ratnagiri,in&units=imperial
    
    Currently calling city number 3403:
    moroto, ug
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=moroto,ug&units=imperial
    
    Currently calling city number 3404:
    kudahuvadhoo, mv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kudahuvadhoo,mv&units=imperial
    
    Currently calling city number 3405:
    guacimo, cr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=guacimo,cr&units=imperial
    
    Currently calling city number 3406:
    jacala, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jacala,mx&units=imperial
    
    Currently calling city number 3407:
    pismo beach, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pismo beach,us&units=imperial
    
    Currently calling city number 3408:
    riviere-au-renard, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=riviere-au-renard,ca&units=imperial
    
    Currently calling city number 3409:
    freeport, bs
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=freeport,bs&units=imperial
    
    Currently calling city number 3410:
    laurel, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=laurel,us&units=imperial
    
    Currently calling city number 3411:
    vadso, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vadso,no&units=imperial
    
    Currently calling city number 3412:
    azare, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=azare,ng&units=imperial
    
    Currently calling city number 3413:
    ulety, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ulety,ru&units=imperial
    
    Currently calling city number 3414:
    christiana, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=christiana,za&units=imperial
    
    Currently calling city number 3415:
    madaoua, ne
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=madaoua,ne&units=imperial
    
    Currently calling city number 3416:
    tahe, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tahe,cn&units=imperial
    
    Currently calling city number 3417:
    tarbagatay, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tarbagatay,ru&units=imperial
    
    Currently calling city number 3418:
    gromiljak, ba
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gromiljak,ba&units=imperial
    
    Currently calling city number 3419:
    mkushi, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mkushi,zm&units=imperial
    
    Currently calling city number 3420:
    uglovskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=uglovskoye,ru&units=imperial
    
    Currently calling city number 3421:
    jaicos, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jaicos,br&units=imperial
    
    Currently calling city number 3422:
    kaiu, ee
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kaiu,ee&units=imperial
    
    Currently calling city number 3423:
    saint-junien, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saint-junien,fr&units=imperial
    
    Currently calling city number 3424:
    cataingan, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cataingan,ph&units=imperial
    
    Currently calling city number 3425:
    vung tau, vn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vung tau,vn&units=imperial
    
    Currently calling city number 3426:
    rumoi, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rumoi,jp&units=imperial
    
    Currently calling city number 3427:
    sicamous, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sicamous,ca&units=imperial
    
    Currently calling city number 3428:
    nahrin, af
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nahrin,af&units=imperial
    
    Currently calling city number 3429:
    georgetown, gy
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=georgetown,gy&units=imperial
    
    Currently calling city number 3430:
    sumenep, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sumenep,id&units=imperial
    
    Currently calling city number 3431:
    casa nova, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=casa nova,br&units=imperial
    
    Currently calling city number 3432:
    san carlos del zulia, ve
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san carlos del zulia,ve&units=imperial
    
    Currently calling city number 3433:
    nanyang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nanyang,cn&units=imperial
    
    Currently calling city number 3434:
    ilheus, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ilheus,br&units=imperial
    
    Currently calling city number 3435:
    stephenville, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=stephenville,us&units=imperial
    
    Currently calling city number 3436:
    west lake stevens, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=west lake stevens,us&units=imperial
    
    Currently calling city number 3437:
    sajoszoged, hu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sajoszoged,hu&units=imperial
    
    Currently calling city number 3438:
    castrillon, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=castrillon,es&units=imperial
    
    Currently calling city number 3439:
    rach gia, vn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rach gia,vn&units=imperial
    
    Currently calling city number 3440:
    bogandinskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bogandinskiy,ru&units=imperial
    
    Currently calling city number 3441:
    palatka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=palatka,ru&units=imperial
    
    Currently calling city number 3442:
    velyka bahachka, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=velyka bahachka,ua&units=imperial
    
    Currently calling city number 3443:
    bhairab bazar, bd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bhairab bazar,bd&units=imperial
    
    Currently calling city number 3444:
    longonjo, ao
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=longonjo,ao&units=imperial
    
    Currently calling city number 3445:
    young, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=young,au&units=imperial
    
    Currently calling city number 3446:
    garden city, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=garden city,us&units=imperial
    
    Currently calling city number 3447:
    dabat, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dabat,et&units=imperial
    
    Currently calling city number 3448:
    zharkent, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zharkent,kz&units=imperial
    
    Currently calling city number 3449:
    chinsali, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chinsali,zm&units=imperial
    
    Currently calling city number 3450:
    loikaw, mm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=loikaw,mm&units=imperial
    
    Currently calling city number 3451:
    jingdezhen, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jingdezhen,cn&units=imperial
    
    Currently calling city number 3452:
    bad salzdetfurth, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bad salzdetfurth,de&units=imperial
    
    Currently calling city number 3453:
    cabra, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cabra,ph&units=imperial
    
    Currently calling city number 3454:
    anito, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=anito,ph&units=imperial
    
    Currently calling city number 3455:
    yenotayevka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yenotayevka,ru&units=imperial
    
    Currently calling city number 3456:
    chadiza, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chadiza,zm&units=imperial
    
    Currently calling city number 3457:
    uruacu, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=uruacu,br&units=imperial
    
    Currently calling city number 3458:
    karwar, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=karwar,in&units=imperial
    
    Currently calling city number 3459:
    koroni, gr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=koroni,gr&units=imperial
    
    Currently calling city number 3460:
    qazvin, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=qazvin,ir&units=imperial
    
    Currently calling city number 3461:
    ibiapina, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ibiapina,br&units=imperial
    
    Currently calling city number 3462:
    borujerd, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=borujerd,ir&units=imperial
    
    Currently calling city number 3463:
    pangoa, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pangoa,pe&units=imperial
    
    Currently calling city number 3464:
    verona, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=verona,us&units=imperial
    
    Currently calling city number 3465:
    show low, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=show low,us&units=imperial
    
    Currently calling city number 3466:
    uniontown, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=uniontown,us&units=imperial
    
    Currently calling city number 3467:
    siddipet, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=siddipet,in&units=imperial
    
    Currently calling city number 3468:
    phuket, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=phuket,th&units=imperial
    
    Currently calling city number 3469:
    kaseda, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kaseda,jp&units=imperial
    
    Currently calling city number 3470:
    coroata, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=coroata,br&units=imperial
    
    Currently calling city number 3471:
    pula, hr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pula,hr&units=imperial
    
    Currently calling city number 3472:
    quiruvilca, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=quiruvilca,pe&units=imperial
    
    Currently calling city number 3473:
    medina del campo, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=medina del campo,es&units=imperial
    
    Currently calling city number 3474:
    chunian, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chunian,pk&units=imperial
    
    Currently calling city number 3475:
    okakarara, na
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=okakarara,na&units=imperial
    
    Currently calling city number 3476:
    le pradet, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=le pradet,fr&units=imperial
    
    Currently calling city number 3477:
    alghero, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alghero,it&units=imperial
    
    Currently calling city number 3478:
    devavanya, hu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=devavanya,hu&units=imperial
    
    Currently calling city number 3479:
    nguruka, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nguruka,tz&units=imperial
    
    Currently calling city number 3480:
    uaua, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=uaua,br&units=imperial
    
    Currently calling city number 3481:
    nanchang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nanchang,cn&units=imperial
    
    Currently calling city number 3482:
    marsabit, ke
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=marsabit,ke&units=imperial
    
    Currently calling city number 3483:
    dannenberg, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dannenberg,de&units=imperial
    
    Currently calling city number 3484:
    morris, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=morris,ca&units=imperial
    
    Currently calling city number 3485:
    cotui, do
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cotui,do&units=imperial
    
    Currently calling city number 3486:
    huaiyuan, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=huaiyuan,cn&units=imperial
    
    Currently calling city number 3487:
    lahijan, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lahijan,ir&units=imperial
    
    Currently calling city number 3488:
    zverinogolovskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zverinogolovskoye,ru&units=imperial
    
    Currently calling city number 3489:
    elk grove village, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=elk grove village,us&units=imperial
    
    Currently calling city number 3490:
    petlawad, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=petlawad,in&units=imperial
    
    Currently calling city number 3491:
    rangapara, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rangapara,in&units=imperial
    
    Currently calling city number 3492:
    cerveteri, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cerveteri,it&units=imperial
    
    Currently calling city number 3493:
    kitale, ke
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kitale,ke&units=imperial
    
    Currently calling city number 3494:
    albacete, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=albacete,es&units=imperial
    
    Currently calling city number 3495:
    varberg, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=varberg,se&units=imperial
    
    Currently calling city number 3496:
    galashki, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=galashki,ru&units=imperial
    
    Currently calling city number 3497:
    oksfjord, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=oksfjord,no&units=imperial
    
    Currently calling city number 3498:
    palembang, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=palembang,id&units=imperial
    
    Currently calling city number 3499:
    sabinas, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sabinas,mx&units=imperial
    
    Currently calling city number 3500:
    narok, ke
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=narok,ke&units=imperial
    
    Currently calling city number 3501:
    mossendjo, cg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mossendjo,cg&units=imperial
    
    Currently calling city number 3502:
    mufumbwe, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mufumbwe,zm&units=imperial
    
    Currently calling city number 3503:
    valdez, ec
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=valdez,ec&units=imperial
    
    Currently calling city number 3504:
    gerash, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gerash,ir&units=imperial
    
    Currently calling city number 3505:
    bahir dar, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bahir dar,et&units=imperial
    
    Currently calling city number 3506:
    batticaloa, lk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=batticaloa,lk&units=imperial
    
    Currently calling city number 3507:
    guane, cu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=guane,cu&units=imperial
    
    Currently calling city number 3508:
    massa marittima, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=massa marittima,it&units=imperial
    
    Currently calling city number 3509:
    huaibei, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=huaibei,cn&units=imperial
    
    Currently calling city number 3510:
    ocampo, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ocampo,mx&units=imperial
    
    Currently calling city number 3511:
    ellisras, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ellisras,za&units=imperial
    
    Currently calling city number 3512:
    san pedro, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san pedro,ar&units=imperial
    
    Currently calling city number 3513:
    onega, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=onega,ru&units=imperial
    
    Currently calling city number 3514:
    riohacha, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=riohacha,co&units=imperial
    
    Currently calling city number 3515:
    chernaya kholunitsa, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chernaya kholunitsa,ru&units=imperial
    
    Currently calling city number 3516:
    truro, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=truro,ca&units=imperial
    
    Currently calling city number 3517:
    tysmenytsya, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tysmenytsya,ua&units=imperial
    
    Currently calling city number 3518:
    bogovarovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bogovarovo,ru&units=imperial
    
    Currently calling city number 3519:
    clarksburg, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=clarksburg,us&units=imperial
    
    Currently calling city number 3520:
    tazmalt, dz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tazmalt,dz&units=imperial
    
    Currently calling city number 3521:
    romitan, uz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=romitan,uz&units=imperial
    
    Currently calling city number 3522:
    khajuraho, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=khajuraho,in&units=imperial
    
    Currently calling city number 3523:
    kigali, rw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kigali,rw&units=imperial
    
    Currently calling city number 3524:
    bordighera, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bordighera,it&units=imperial
    
    Currently calling city number 3525:
    binzhou, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=binzhou,cn&units=imperial
    
    Currently calling city number 3526:
    si sa ket, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=si sa ket,th&units=imperial
    
    Currently calling city number 3527:
    fergus falls, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fergus falls,us&units=imperial
    
    Currently calling city number 3528:
    mount pleasant, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mount pleasant,us&units=imperial
    
    Currently calling city number 3529:
    baisha, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=baisha,cn&units=imperial
    
    Currently calling city number 3530:
    kananga, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kananga,cd&units=imperial
    
    Currently calling city number 3531:
    warragul, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=warragul,au&units=imperial
    
    Currently calling city number 3532:
    kropachevo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kropachevo,ru&units=imperial
    
    Currently calling city number 3533:
    mitsukaido, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mitsukaido,jp&units=imperial
    
    Currently calling city number 3534:
    east brainerd, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=east brainerd,us&units=imperial
    
    Currently calling city number 3535:
    lanigan, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lanigan,ca&units=imperial
    
    Currently calling city number 3536:
    parichhatgarh, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=parichhatgarh,in&units=imperial
    
    Currently calling city number 3537:
    tuban, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tuban,id&units=imperial
    
    Currently calling city number 3538:
    bisert, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bisert,ru&units=imperial
    
    Currently calling city number 3539:
    sidrolandia, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sidrolandia,br&units=imperial
    
    Currently calling city number 3540:
    jamestown, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jamestown,us&units=imperial
    
    Currently calling city number 3541:
    paidha, ug
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=paidha,ug&units=imperial
    
    Currently calling city number 3542:
    yankton, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yankton,us&units=imperial
    
    Currently calling city number 3543:
    erdaojiang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=erdaojiang,cn&units=imperial
    
    Currently calling city number 3544:
    samfya, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=samfya,zm&units=imperial
    
    Currently calling city number 3545:
    otaru, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=otaru,jp&units=imperial
    
    Currently calling city number 3546:
    drezdenko, pl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=drezdenko,pl&units=imperial
    
    Currently calling city number 3547:
    san pablo, gt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san pablo,gt&units=imperial
    
    Currently calling city number 3548:
    koungou, yt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=koungou,yt&units=imperial
    
    Currently calling city number 3549:
    kurchum, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kurchum,kz&units=imperial
    
    Currently calling city number 3550:
    healdsburg, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=healdsburg,us&units=imperial
    
    Currently calling city number 3551:
    emporia, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=emporia,us&units=imperial
    
    Currently calling city number 3552:
    puerto madryn, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=puerto madryn,ar&units=imperial
    
    Currently calling city number 3553:
    pembroke, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pembroke,ca&units=imperial
    
    Currently calling city number 3554:
    vasilsursk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vasilsursk,ru&units=imperial
    
    Currently calling city number 3555:
    rangoon, mm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rangoon,mm&units=imperial
    
    Currently calling city number 3556:
    zabaykalsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zabaykalsk,ru&units=imperial
    
    Currently calling city number 3557:
    alindao, cf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alindao,cf&units=imperial
    
    Currently calling city number 3558:
    kurumkan, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kurumkan,ru&units=imperial
    
    Currently calling city number 3559:
    pirna, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pirna,de&units=imperial
    
    Currently calling city number 3560:
    povenets, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=povenets,ru&units=imperial
    
    Currently calling city number 3561:
    suoyarvi, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=suoyarvi,ru&units=imperial
    
    Currently calling city number 3562:
    ranot, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ranot,th&units=imperial
    
    Currently calling city number 3563:
    riihimaki, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=riihimaki,fi&units=imperial
    
    Currently calling city number 3564:
    kobayashi, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kobayashi,jp&units=imperial
    
    Currently calling city number 3565:
    orda, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=orda,ru&units=imperial
    
    Currently calling city number 3566:
    santa ana, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santa ana,pe&units=imperial
    
    Currently calling city number 3567:
    batabano, cu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=batabano,cu&units=imperial
    
    Currently calling city number 3568:
    kyle, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kyle,us&units=imperial
    
    Currently calling city number 3569:
    bugasong, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bugasong,ph&units=imperial
    
    Currently calling city number 3570:
    matinha, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=matinha,br&units=imperial
    
    Currently calling city number 3571:
    linkoping, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=linkoping,se&units=imperial
    
    Currently calling city number 3572:
    polis, cy
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=polis,cy&units=imperial
    
    Currently calling city number 3573:
    las vegas, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=las vegas,us&units=imperial
    
    Currently calling city number 3574:
    novosibirsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=novosibirsk,ru&units=imperial
    
    Currently calling city number 3575:
    nordfjordeid, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nordfjordeid,no&units=imperial
    
    Currently calling city number 3576:
    san francisco, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san francisco,ar&units=imperial
    
    Currently calling city number 3577:
    leon, ni
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=leon,ni&units=imperial
    
    Currently calling city number 3578:
    calamar, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=calamar,co&units=imperial
    
    Currently calling city number 3579:
    danane, ci
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=danane,ci&units=imperial
    
    Currently calling city number 3580:
    lincoln, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lincoln,ar&units=imperial
    
    Currently calling city number 3581:
    nsanje, mw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nsanje,mw&units=imperial
    
    Currently calling city number 3582:
    caorle, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=caorle,it&units=imperial
    
    Currently calling city number 3583:
    las varas, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=las varas,mx&units=imperial
    
    Currently calling city number 3584:
    bulaevo, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bulaevo,kz&units=imperial
    
    Currently calling city number 3585:
    bucerias, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bucerias,mx&units=imperial
    
    Currently calling city number 3586:
    notse, tg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=notse,tg&units=imperial
    
    Currently calling city number 3587:
    det udom, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=det udom,th&units=imperial
    
    Currently calling city number 3588:
    boston, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=boston,gb&units=imperial
    
    Currently calling city number 3589:
    pakxan, la
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pakxan,la&units=imperial
    
    Currently calling city number 3590:
    staryy krym, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=staryy krym,ua&units=imperial
    
    Currently calling city number 3591:
    santo antonio do taua, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santo antonio do taua,br&units=imperial
    
    Currently calling city number 3592:
    cherdyn, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cherdyn,ru&units=imperial
    
    Currently calling city number 3593:
    rzeszow, pl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rzeszow,pl&units=imperial
    
    Currently calling city number 3594:
    panjab, af
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=panjab,af&units=imperial
    
    Currently calling city number 3595:
    thanh hoa, vn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=thanh hoa,vn&units=imperial
    
    Currently calling city number 3596:
    lawrenceville, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lawrenceville,us&units=imperial
    
    Currently calling city number 3597:
    pszczyna, pl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pszczyna,pl&units=imperial
    
    Currently calling city number 3598:
    lac du bonnet, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lac du bonnet,ca&units=imperial
    
    Currently calling city number 3599:
    kichera, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kichera,ru&units=imperial
    
    Currently calling city number 3600:
    pastos bons, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pastos bons,br&units=imperial
    
    Currently calling city number 3601:
    murliganj, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=murliganj,in&units=imperial
    
    Currently calling city number 3602:
    diofior, sn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=diofior,sn&units=imperial
    
    Currently calling city number 3603:
    moche, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=moche,pe&units=imperial
    
    Currently calling city number 3604:
    eufaula, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=eufaula,us&units=imperial
    
    Currently calling city number 3605:
    southaven, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=southaven,us&units=imperial
    
    Currently calling city number 3606:
    lyantonde, ug
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lyantonde,ug&units=imperial
    
    Currently calling city number 3607:
    caohai, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=caohai,cn&units=imperial
    
    Currently calling city number 3608:
    loreto, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=loreto,mx&units=imperial
    
    Currently calling city number 3609:
    dabhol, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dabhol,in&units=imperial
    
    Currently calling city number 3610:
    fulton, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fulton,us&units=imperial
    
    Currently calling city number 3611:
    laem sing, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=laem sing,th&units=imperial
    
    Currently calling city number 3612:
    sretensk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sretensk,ru&units=imperial
    
    Currently calling city number 3613:
    auchi, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=auchi,ng&units=imperial
    
    Currently calling city number 3614:
    bandiagara, ml
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bandiagara,ml&units=imperial
    
    Currently calling city number 3615:
    barreirinhas, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=barreirinhas,br&units=imperial
    
    Currently calling city number 3616:
    arkhangelsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=arkhangelsk,ru&units=imperial
    
    Currently calling city number 3617:
    mingyue, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mingyue,cn&units=imperial
    
    Currently calling city number 3618:
    mandsaur, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mandsaur,in&units=imperial
    
    Currently calling city number 3619:
    minot, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=minot,us&units=imperial
    
    Currently calling city number 3620:
    bethanien, na
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bethanien,na&units=imperial
    
    Currently calling city number 3621:
    pontal, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pontal,br&units=imperial
    
    Currently calling city number 3622:
    adzope, ci
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=adzope,ci&units=imperial
    
    Currently calling city number 3623:
    ayorou, ne
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ayorou,ne&units=imperial
    
    Currently calling city number 3624:
    svobody, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=svobody,ru&units=imperial
    
    Currently calling city number 3625:
    playas, ec
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=playas,ec&units=imperial
    
    Currently calling city number 3626:
    synya, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=synya,ru&units=imperial
    
    Currently calling city number 3627:
    savonlinna, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=savonlinna,fi&units=imperial
    
    Currently calling city number 3628:
    haimen, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=haimen,cn&units=imperial
    
    Currently calling city number 3629:
    merke, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=merke,kz&units=imperial
    
    Currently calling city number 3630:
    rubiataba, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rubiataba,br&units=imperial
    
    Currently calling city number 3631:
    pitsunda, ge
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pitsunda,ge&units=imperial
    
    Currently calling city number 3632:
    manjacaze, mz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=manjacaze,mz&units=imperial
    
    Currently calling city number 3633:
    sayville, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sayville,us&units=imperial
    
    Currently calling city number 3634:
    kigoma, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kigoma,tz&units=imperial
    
    Currently calling city number 3635:
    elliot, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=elliot,za&units=imperial
    
    Currently calling city number 3636:
    traipu, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=traipu,br&units=imperial
    
    Currently calling city number 3637:
    nola, cf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nola,cf&units=imperial
    
    Currently calling city number 3638:
    chatra, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chatra,in&units=imperial
    
    Currently calling city number 3639:
    ryotsu, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ryotsu,jp&units=imperial
    
    Currently calling city number 3640:
    ridgecrest, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ridgecrest,us&units=imperial
    
    Currently calling city number 3641:
    bajil, ye
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bajil,ye&units=imperial
    
    Currently calling city number 3642:
    jos, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jos,ng&units=imperial
    
    Currently calling city number 3643:
    athni, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=athni,in&units=imperial
    
    Currently calling city number 3644:
    yulin, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yulin,cn&units=imperial
    
    Currently calling city number 3645:
    kilrush, ie
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kilrush,ie&units=imperial
    
    Currently calling city number 3646:
    linhares, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=linhares,br&units=imperial
    
    Currently calling city number 3647:
    monterey, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=monterey,us&units=imperial
    
    Currently calling city number 3648:
    pavlodar, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pavlodar,kz&units=imperial
    
    Currently calling city number 3649:
    stockach, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=stockach,de&units=imperial
    
    Currently calling city number 3650:
    mogzon, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mogzon,ru&units=imperial
    
    Currently calling city number 3651:
    vinhedo, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vinhedo,br&units=imperial
    
    Currently calling city number 3652:
    neryungri, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=neryungri,ru&units=imperial
    
    Currently calling city number 3653:
    mazamari, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mazamari,pe&units=imperial
    
    Currently calling city number 3654:
    hesla, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hesla,in&units=imperial
    
    Currently calling city number 3655:
    forbes, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=forbes,au&units=imperial
    
    Currently calling city number 3656:
    telfs, at
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=telfs,at&units=imperial
    
    Currently calling city number 3657:
    andrushivka, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=andrushivka,ua&units=imperial
    
    Currently calling city number 3658:
    moscow, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=moscow,us&units=imperial
    
    Currently calling city number 3659:
    leskovac, rs
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=leskovac,rs&units=imperial
    
    Currently calling city number 3660:
    le vauclin, mq
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=le vauclin,mq&units=imperial
    
    Currently calling city number 3661:
    kirillov, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kirillov,ru&units=imperial
    
    Currently calling city number 3662:
    perene, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=perene,pe&units=imperial
    
    Currently calling city number 3663:
    kocser, hu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kocser,hu&units=imperial
    
    Currently calling city number 3664:
    two rivers, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=two rivers,us&units=imperial
    
    Currently calling city number 3665:
    kalas, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kalas,in&units=imperial
    
    Currently calling city number 3666:
    larsnes, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=larsnes,no&units=imperial
    
    Currently calling city number 3667:
    south valley, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=south valley,us&units=imperial
    
    Currently calling city number 3668:
    lukulu, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lukulu,zm&units=imperial
    
    Currently calling city number 3669:
    seligenstadt, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=seligenstadt,de&units=imperial
    
    Currently calling city number 3670:
    corner brook, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=corner brook,ca&units=imperial
    
    Currently calling city number 3671:
    bay-ang, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bay-ang,ph&units=imperial
    
    Currently calling city number 3672:
    xudat, az
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=xudat,az&units=imperial
    
    Currently calling city number 3673:
    alcamo, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alcamo,it&units=imperial
    
    Currently calling city number 3674:
    san giorgio del sannio, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san giorgio del sannio,it&units=imperial
    
    Currently calling city number 3675:
    kursenai, lt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kursenai,lt&units=imperial
    
    Currently calling city number 3676:
    san rafael, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san rafael,ph&units=imperial
    
    Currently calling city number 3677:
    manturovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=manturovo,ru&units=imperial
    
    Currently calling city number 3678:
    mgandu, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mgandu,tz&units=imperial
    
    Currently calling city number 3679:
    west allis, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=west allis,us&units=imperial
    
    Currently calling city number 3680:
    tevaitoa, pf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tevaitoa,pf&units=imperial
    
    Currently calling city number 3681:
    rudnaya pristan, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rudnaya pristan,ru&units=imperial
    
    Currently calling city number 3682:
    yafran, ly
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yafran,ly&units=imperial
    
    Currently calling city number 3683:
    shakhtinsk, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shakhtinsk,kz&units=imperial
    
    Currently calling city number 3684:
    peru, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=peru,us&units=imperial
    
    Currently calling city number 3685:
    peceneaga, ro
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=peceneaga,ro&units=imperial
    
    Currently calling city number 3686:
    blyth, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=blyth,gb&units=imperial
    
    Currently calling city number 3687:
    leverkusen, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=leverkusen,de&units=imperial
    
    Currently calling city number 3688:
    gandajika, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gandajika,cd&units=imperial
    
    Currently calling city number 3689:
    san benito, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san benito,ph&units=imperial
    
    Currently calling city number 3690:
    rosario, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rosario,br&units=imperial
    
    Currently calling city number 3691:
    rawlins, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rawlins,us&units=imperial
    
    Currently calling city number 3692:
    massena, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=massena,us&units=imperial
    
    Currently calling city number 3693:
    haripur, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=haripur,pk&units=imperial
    
    Currently calling city number 3694:
    lesogorsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lesogorsk,ru&units=imperial
    
    Currently calling city number 3695:
    oloron-sainte-marie, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=oloron-sainte-marie,fr&units=imperial
    
    Currently calling city number 3696:
    gravatai, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gravatai,br&units=imperial
    
    Currently calling city number 3697:
    ila, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ila,ng&units=imperial
    
    Currently calling city number 3698:
    lander, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lander,us&units=imperial
    
    Currently calling city number 3699:
    waling, np
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=waling,np&units=imperial
    
    Currently calling city number 3700:
    dinguiraye, gn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dinguiraye,gn&units=imperial
    
    Currently calling city number 3701:
    vangaindrano, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vangaindrano,mg&units=imperial
    
    Currently calling city number 3702:
    zarate, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zarate,ar&units=imperial
    
    Currently calling city number 3703:
    videira, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=videira,br&units=imperial
    
    Currently calling city number 3704:
    bosconia, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bosconia,co&units=imperial
    
    Currently calling city number 3705:
    cartagena del chaira, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cartagena del chaira,co&units=imperial
    
    Currently calling city number 3706:
    drexel heights, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=drexel heights,us&units=imperial
    
    Currently calling city number 3707:
    seybaplaya, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=seybaplaya,mx&units=imperial
    
    Currently calling city number 3708:
    veshkayma, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=veshkayma,ru&units=imperial
    
    Currently calling city number 3709:
    borodino, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=borodino,ru&units=imperial
    
    Currently calling city number 3710:
    uthal, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=uthal,pk&units=imperial
    
    Currently calling city number 3711:
    orel, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=orel,ru&units=imperial
    
    Currently calling city number 3712:
    bazarnyye mataki, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bazarnyye mataki,ru&units=imperial
    
    Currently calling city number 3713:
    tikrit, iq
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tikrit,iq&units=imperial
    
    Currently calling city number 3714:
    chicontepec, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chicontepec,mx&units=imperial
    
    Currently calling city number 3715:
    ustka, pl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ustka,pl&units=imperial
    
    Currently calling city number 3716:
    tecoanapa, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tecoanapa,mx&units=imperial
    
    Currently calling city number 3717:
    sao sebastiao, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sao sebastiao,br&units=imperial
    
    Currently calling city number 3718:
    montanha, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=montanha,br&units=imperial
    
    Currently calling city number 3719:
    tres passos, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tres passos,br&units=imperial
    
    Currently calling city number 3720:
    winchester, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=winchester,us&units=imperial
    
    Currently calling city number 3721:
    forest acres, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=forest acres,us&units=imperial
    
    Currently calling city number 3722:
    lysva, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lysva,ru&units=imperial
    
    Currently calling city number 3723:
    urazovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=urazovo,ru&units=imperial
    
    Currently calling city number 3724:
    haywards heath, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=haywards heath,gb&units=imperial
    
    Currently calling city number 3725:
    riberalta, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=riberalta,bo&units=imperial
    
    Currently calling city number 3726:
    mumford, gh
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mumford,gh&units=imperial
    
    Currently calling city number 3727:
    panaba, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=panaba,mx&units=imperial
    
    Currently calling city number 3728:
    lapy, pl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lapy,pl&units=imperial
    
    Currently calling city number 3729:
    montepuez, mz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=montepuez,mz&units=imperial
    
    Currently calling city number 3730:
    ambovombe, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ambovombe,mg&units=imperial
    
    Currently calling city number 3731:
    francisco beltrao, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=francisco beltrao,br&units=imperial
    
    Currently calling city number 3732:
    lugovoy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lugovoy,ru&units=imperial
    
    Currently calling city number 3733:
    bageshwar, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bageshwar,in&units=imperial
    
    Currently calling city number 3734:
    erfurt, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=erfurt,de&units=imperial
    
    Currently calling city number 3735:
    vista hermosa, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vista hermosa,co&units=imperial
    
    Currently calling city number 3736:
    basavakalyan, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=basavakalyan,in&units=imperial
    
    Currently calling city number 3737:
    fort saint james, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fort saint james,ca&units=imperial
    
    Currently calling city number 3738:
    kirkland lake, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kirkland lake,ca&units=imperial
    
    Currently calling city number 3739:
    navirai, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=navirai,br&units=imperial
    
    Currently calling city number 3740:
    turgoyak, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=turgoyak,ru&units=imperial
    
    Currently calling city number 3741:
    lomza, pl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lomza,pl&units=imperial
    
    Currently calling city number 3742:
    miracema do tocantins, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=miracema do tocantins,br&units=imperial
    
    Currently calling city number 3743:
    seaham, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=seaham,gb&units=imperial
    
    Currently calling city number 3744:
    venustiano carranza, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=venustiano carranza,mx&units=imperial
    
    Currently calling city number 3745:
    barahona, do
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=barahona,do&units=imperial
    
    Currently calling city number 3746:
    ramshir, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ramshir,ir&units=imperial
    
    Currently calling city number 3747:
    caiaponia, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=caiaponia,br&units=imperial
    
    Currently calling city number 3748:
    oros, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=oros,br&units=imperial
    
    Currently calling city number 3749:
    arrecife, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=arrecife,es&units=imperial
    
    Currently calling city number 3750:
    oskarshamn, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=oskarshamn,se&units=imperial
    
    Currently calling city number 3751:
    klobuck, pl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=klobuck,pl&units=imperial
    
    Currently calling city number 3752:
    gagnoa, ci
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gagnoa,ci&units=imperial
    
    Currently calling city number 3753:
    qianguo, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=qianguo,cn&units=imperial
    
    Currently calling city number 3754:
    kangaatsiaq, gl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kangaatsiaq,gl&units=imperial
    
    Currently calling city number 3755:
    baft, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=baft,ir&units=imperial
    
    Currently calling city number 3756:
    sakhnovshchyna, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sakhnovshchyna,ua&units=imperial
    
    Currently calling city number 3757:
    canon city, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=canon city,us&units=imperial
    
    Currently calling city number 3758:
    bay city, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bay city,us&units=imperial
    
    Currently calling city number 3759:
    trelleborg, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=trelleborg,se&units=imperial
    
    Currently calling city number 3760:
    harpanahalli, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=harpanahalli,in&units=imperial
    
    Currently calling city number 3761:
    lulea, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lulea,se&units=imperial
    
    Currently calling city number 3762:
    guadalupe y calvo, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=guadalupe y calvo,mx&units=imperial
    
    Currently calling city number 3763:
    khonsa, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=khonsa,in&units=imperial
    
    Currently calling city number 3764:
    dire, ml
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dire,ml&units=imperial
    
    Currently calling city number 3765:
    wau, pg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wau,pg&units=imperial
    
    Currently calling city number 3766:
    ballitoville, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ballitoville,za&units=imperial
    
    Currently calling city number 3767:
    new plymouth, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=new plymouth,nz&units=imperial
    
    Currently calling city number 3768:
    moosomin, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=moosomin,ca&units=imperial
    
    Currently calling city number 3769:
    jinxiang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jinxiang,cn&units=imperial
    
    Currently calling city number 3770:
    chase, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chase,ca&units=imperial
    
    Currently calling city number 3771:
    parana, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=parana,ar&units=imperial
    
    Currently calling city number 3772:
    fort frances, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fort frances,ca&units=imperial
    
    Currently calling city number 3773:
    birnin kebbi, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=birnin kebbi,ng&units=imperial
    
    Currently calling city number 3774:
    rybinsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rybinsk,ru&units=imperial
    
    Currently calling city number 3775:
    bezhanitsy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bezhanitsy,ru&units=imperial
    
    Currently calling city number 3776:
    socorro, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=socorro,us&units=imperial
    
    Currently calling city number 3777:
    nkayi, cg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nkayi,cg&units=imperial
    
    Currently calling city number 3778:
    bikin, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bikin,ru&units=imperial
    
    Currently calling city number 3779:
    pindiga, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pindiga,ng&units=imperial
    
    Currently calling city number 3780:
    zhovkva, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zhovkva,ua&units=imperial
    
    Currently calling city number 3781:
    grakhovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=grakhovo,ru&units=imperial
    
    Currently calling city number 3782:
    tianmen, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tianmen,cn&units=imperial
    
    Currently calling city number 3783:
    kewanee, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kewanee,us&units=imperial
    
    Currently calling city number 3784:
    liuzhou, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=liuzhou,cn&units=imperial
    
    Currently calling city number 3785:
    effingham, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=effingham,us&units=imperial
    
    Currently calling city number 3786:
    cazones, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cazones,mx&units=imperial
    
    Currently calling city number 3787:
    belgrade, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=belgrade,us&units=imperial
    
    Currently calling city number 3788:
    makhachkala, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=makhachkala,ru&units=imperial
    
    Currently calling city number 3789:
    pocinhos, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pocinhos,br&units=imperial
    
    Currently calling city number 3790:
    mudbidri, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mudbidri,in&units=imperial
    
    Currently calling city number 3791:
    deniliquin, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=deniliquin,au&units=imperial
    
    Currently calling city number 3792:
    lewisporte, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lewisporte,ca&units=imperial
    
    Currently calling city number 3793:
    qarqin, af
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=qarqin,af&units=imperial
    
    Currently calling city number 3794:
    snasa, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=snasa,no&units=imperial
    
    Currently calling city number 3795:
    mingaora, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mingaora,pk&units=imperial
    
    Currently calling city number 3796:
    forest, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=forest,ca&units=imperial
    
    Currently calling city number 3797:
    kang, bw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kang,bw&units=imperial
    
    Currently calling city number 3798:
    enumclaw, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=enumclaw,us&units=imperial
    
    Currently calling city number 3799:
    muslyumovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=muslyumovo,ru&units=imperial
    
    Currently calling city number 3800:
    naliya, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=naliya,in&units=imperial
    
    Currently calling city number 3801:
    cananea, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cananea,mx&units=imperial
    
    Currently calling city number 3802:
    lodwar, ke
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lodwar,ke&units=imperial
    
    Currently calling city number 3803:
    dabakala, ci
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dabakala,ci&units=imperial
    
    Currently calling city number 3804:
    ipixuna, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ipixuna,br&units=imperial
    
    Currently calling city number 3805:
    baghmara, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=baghmara,in&units=imperial
    
    Currently calling city number 3806:
    yarada, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yarada,in&units=imperial
    
    Currently calling city number 3807:
    caboolture, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=caboolture,au&units=imperial
    
    Currently calling city number 3808:
    rojhan, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rojhan,pk&units=imperial
    
    Currently calling city number 3809:
    port-de-bouc, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=port-de-bouc,fr&units=imperial
    
    Currently calling city number 3810:
    avinurme, ee
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=avinurme,ee&units=imperial
    
    Currently calling city number 3811:
    woodward, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=woodward,us&units=imperial
    
    Currently calling city number 3812:
    azuaga, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=azuaga,es&units=imperial
    
    Currently calling city number 3813:
    krasnyy chikoy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=krasnyy chikoy,ru&units=imperial
    
    Currently calling city number 3814:
    saurimo, ao
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saurimo,ao&units=imperial
    
    Currently calling city number 3815:
    kalispell, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kalispell,us&units=imperial
    
    Currently calling city number 3816:
    betong, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=betong,th&units=imperial
    
    Currently calling city number 3817:
    nyamuswa, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nyamuswa,tz&units=imperial
    
    Currently calling city number 3818:
    mathathane, bw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mathathane,bw&units=imperial
    
    Currently calling city number 3819:
    shepetivka, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shepetivka,ua&units=imperial
    
    Currently calling city number 3820:
    abapo, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=abapo,bo&units=imperial
    
    Currently calling city number 3821:
    greenville, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=greenville,us&units=imperial
    
    Currently calling city number 3822:
    baijiantan, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=baijiantan,cn&units=imperial
    
    Currently calling city number 3823:
    firminy, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=firminy,fr&units=imperial
    
    Currently calling city number 3824:
    phonhong, la
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=phonhong,la&units=imperial
    
    Currently calling city number 3825:
    el cobre, cu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=el cobre,cu&units=imperial
    
    Currently calling city number 3826:
    zasechnoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zasechnoye,ru&units=imperial
    
    Currently calling city number 3827:
    siguiri, gn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=siguiri,gn&units=imperial
    
    Currently calling city number 3828:
    harlingen, nl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=harlingen,nl&units=imperial
    
    Currently calling city number 3829:
    neokhorion, gr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=neokhorion,gr&units=imperial
    
    Currently calling city number 3830:
    kamyshin, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kamyshin,ru&units=imperial
    
    Currently calling city number 3831:
    guerande, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=guerande,fr&units=imperial
    
    Currently calling city number 3832:
    wuwei, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wuwei,cn&units=imperial
    
    Currently calling city number 3833:
    kumluca, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kumluca,tr&units=imperial
    
    Currently calling city number 3834:
    uyskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=uyskoye,ru&units=imperial
    
    Currently calling city number 3835:
    wairoa, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wairoa,nz&units=imperial
    
    Currently calling city number 3836:
    massakory, td
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=massakory,td&units=imperial
    
    Currently calling city number 3837:
    kizukuri, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kizukuri,jp&units=imperial
    
    Currently calling city number 3838:
    atikokan, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=atikokan,ca&units=imperial
    
    Currently calling city number 3839:
    vygonichi, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vygonichi,ru&units=imperial
    
    Currently calling city number 3840:
    buloh kasap, my
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=buloh kasap,my&units=imperial
    
    Currently calling city number 3841:
    bolshoy lug, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bolshoy lug,ru&units=imperial
    
    Currently calling city number 3842:
    goba, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=goba,et&units=imperial
    
    Currently calling city number 3843:
    tete, mz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tete,mz&units=imperial
    
    Currently calling city number 3844:
    sao geraldo do araguaia, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sao geraldo do araguaia,br&units=imperial
    
    Currently calling city number 3845:
    dunmore east, ie
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dunmore east,ie&units=imperial
    
    Currently calling city number 3846:
    kerrobert, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kerrobert,ca&units=imperial
    
    Currently calling city number 3847:
    shambu, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shambu,et&units=imperial
    
    Currently calling city number 3848:
    ejido, ve
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ejido,ve&units=imperial
    
    Currently calling city number 3849:
    nkoteng, cm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nkoteng,cm&units=imperial
    
    Currently calling city number 3850:
    ballangen, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ballangen,no&units=imperial
    
    Currently calling city number 3851:
    karhal, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=karhal,in&units=imperial
    
    Currently calling city number 3852:
    carroll, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=carroll,us&units=imperial
    
    Currently calling city number 3853:
    new waterford, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=new waterford,ca&units=imperial
    
    Currently calling city number 3854:
    bom jesus, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bom jesus,br&units=imperial
    
    Currently calling city number 3855:
    gbarnga, lr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gbarnga,lr&units=imperial
    
    Currently calling city number 3856:
    savantvadi, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=savantvadi,in&units=imperial
    
    Currently calling city number 3857:
    cambara, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cambara,br&units=imperial
    
    Currently calling city number 3858:
    amasya, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=amasya,tr&units=imperial
    
    Currently calling city number 3859:
    pinawa, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pinawa,ca&units=imperial
    
    Currently calling city number 3860:
    oneonta, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=oneonta,us&units=imperial
    
    Currently calling city number 3861:
    shush, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shush,ir&units=imperial
    
    Currently calling city number 3862:
    tulungagung, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tulungagung,id&units=imperial
    
    Currently calling city number 3863:
    chihuahua, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chihuahua,mx&units=imperial
    
    Currently calling city number 3864:
    uyemskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=uyemskiy,ru&units=imperial
    
    Currently calling city number 3865:
    kyaikto, mm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kyaikto,mm&units=imperial
    
    Currently calling city number 3866:
    opelousas, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=opelousas,us&units=imperial
    
    Currently calling city number 3867:
    colombo, lk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=colombo,lk&units=imperial
    
    Currently calling city number 3868:
    ashtabula, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ashtabula,us&units=imperial
    
    Currently calling city number 3869:
    bobrovka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bobrovka,ru&units=imperial
    
    Currently calling city number 3870:
    tallahassee, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tallahassee,us&units=imperial
    
    Currently calling city number 3871:
    puro, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=puro,ph&units=imperial
    
    Currently calling city number 3872:
    rudbar, af
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rudbar,af&units=imperial
    
    Currently calling city number 3873:
    aanekoski, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aanekoski,fi&units=imperial
    
    Currently calling city number 3874:
    karachev, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=karachev,ru&units=imperial
    
    Currently calling city number 3875:
    boueni, yt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=boueni,yt&units=imperial
    
    Currently calling city number 3876:
    gusau, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gusau,ng&units=imperial
    
    Currently calling city number 3877:
    udomlya, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=udomlya,ru&units=imperial
    
    Currently calling city number 3878:
    spearfish, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=spearfish,us&units=imperial
    
    Currently calling city number 3879:
    leeton, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=leeton,au&units=imperial
    
    Currently calling city number 3880:
    lincoln, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lincoln,us&units=imperial
    
    Currently calling city number 3881:
    dehloran, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dehloran,ir&units=imperial
    
    Currently calling city number 3882:
    mugumu, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mugumu,tz&units=imperial
    
    Currently calling city number 3883:
    navrongo, gh
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=navrongo,gh&units=imperial
    
    Currently calling city number 3884:
    ofaqim, il
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ofaqim,il&units=imperial
    
    Currently calling city number 3885:
    leua, ao
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=leua,ao&units=imperial
    
    Currently calling city number 3886:
    kharovsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kharovsk,ru&units=imperial
    
    Currently calling city number 3887:
    storsteinnes, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=storsteinnes,no&units=imperial
    
    Currently calling city number 3888:
    sendafa, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sendafa,et&units=imperial
    
    Currently calling city number 3889:
    santa josefa, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santa josefa,ph&units=imperial
    
    Currently calling city number 3890:
    gouyave, gd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gouyave,gd&units=imperial
    
    Currently calling city number 3891:
    satun, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=satun,th&units=imperial
    
    Currently calling city number 3892:
    thomasville, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=thomasville,us&units=imperial
    
    Currently calling city number 3893:
    craigieburn, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=craigieburn,au&units=imperial
    
    Currently calling city number 3894:
    saint john, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saint john,ca&units=imperial
    
    Currently calling city number 3895:
    vostochnyy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vostochnyy,ru&units=imperial
    
    Currently calling city number 3896:
    denizli, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=denizli,tr&units=imperial
    
    Currently calling city number 3897:
    magaria, ne
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=magaria,ne&units=imperial
    
    Currently calling city number 3898:
    betare oya, cm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=betare oya,cm&units=imperial
    
    Currently calling city number 3899:
    kawardha, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kawardha,in&units=imperial
    
    Currently calling city number 3900:
    naron, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=naron,es&units=imperial
    
    Currently calling city number 3901:
    madona, lv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=madona,lv&units=imperial
    
    Currently calling city number 3902:
    morro bay, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=morro bay,us&units=imperial
    
    Currently calling city number 3903:
    mochicahui, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mochicahui,mx&units=imperial
    
    Currently calling city number 3904:
    tanhacu, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tanhacu,br&units=imperial
    
    Currently calling city number 3905:
    zhuzhou, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zhuzhou,cn&units=imperial
    
    Currently calling city number 3906:
    conceicao da barra, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=conceicao da barra,br&units=imperial
    
    Currently calling city number 3907:
    bhainsa, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bhainsa,in&units=imperial
    
    Currently calling city number 3908:
    jabinyanah, tn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jabinyanah,tn&units=imperial
    
    Currently calling city number 3909:
    qixingtai, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=qixingtai,cn&units=imperial
    
    Currently calling city number 3910:
    shakiso, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shakiso,et&units=imperial
    
    Currently calling city number 3911:
    araioses, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=araioses,br&units=imperial
    
    Currently calling city number 3912:
    rovereto, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rovereto,it&units=imperial
    
    Currently calling city number 3913:
    chambersburg, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chambersburg,us&units=imperial
    
    Currently calling city number 3914:
    koltubanovskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=koltubanovskiy,ru&units=imperial
    
    Currently calling city number 3915:
    hof, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hof,no&units=imperial
    
    Currently calling city number 3916:
    bezhta, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bezhta,ru&units=imperial
    
    Currently calling city number 3917:
    santiago de cao, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santiago de cao,pe&units=imperial
    
    Currently calling city number 3918:
    mglin, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mglin,ru&units=imperial
    
    Currently calling city number 3919:
    umea, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=umea,se&units=imperial
    
    Currently calling city number 3920:
    gambiran, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gambiran,id&units=imperial
    
    Currently calling city number 3921:
    sao jose da laje, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sao jose da laje,br&units=imperial
    
    Currently calling city number 3922:
    oruro, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=oruro,bo&units=imperial
    
    Currently calling city number 3923:
    redmond, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=redmond,us&units=imperial
    
    Currently calling city number 3924:
    dokka, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dokka,no&units=imperial
    
    Currently calling city number 3925:
    lasem, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lasem,id&units=imperial
    
    Currently calling city number 3926:
    sokoto, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sokoto,ng&units=imperial
    
    Currently calling city number 3927:
    braganca, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=braganca,br&units=imperial
    
    Currently calling city number 3928:
    carahue, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=carahue,cl&units=imperial
    
    Currently calling city number 3929:
    halberstadt, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=halberstadt,de&units=imperial
    
    Currently calling city number 3930:
    kimparana, ml
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kimparana,ml&units=imperial
    
    Currently calling city number 3931:
    goya, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=goya,ar&units=imperial
    
    Currently calling city number 3932:
    tatsuno, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tatsuno,jp&units=imperial
    
    Currently calling city number 3933:
    lappeenranta, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lappeenranta,fi&units=imperial
    
    Currently calling city number 3934:
    dandong, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dandong,cn&units=imperial
    
    Currently calling city number 3935:
    jacqueville, ci
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jacqueville,ci&units=imperial
    
    Currently calling city number 3936:
    yilan, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yilan,cn&units=imperial
    
    Currently calling city number 3937:
    lufkin, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lufkin,us&units=imperial
    
    Currently calling city number 3938:
    starobaltachevo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=starobaltachevo,ru&units=imperial
    
    Currently calling city number 3939:
    chimoio, mz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chimoio,mz&units=imperial
    
    Currently calling city number 3940:
    wichelen, be
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wichelen,be&units=imperial
    
    Currently calling city number 3941:
    verkhnyaya sinyachikha, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=verkhnyaya sinyachikha,ru&units=imperial
    
    Currently calling city number 3942:
    mae sai, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mae sai,th&units=imperial
    
    Currently calling city number 3943:
    alekseyevka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alekseyevka,ru&units=imperial
    
    Currently calling city number 3944:
    geisenheim, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=geisenheim,de&units=imperial
    
    Currently calling city number 3945:
    xam nua, la
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=xam nua,la&units=imperial
    
    Currently calling city number 3946:
    saint-lo, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saint-lo,fr&units=imperial
    
    Currently calling city number 3947:
    prado, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=prado,br&units=imperial
    
    Currently calling city number 3948:
    agua branca, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=agua branca,br&units=imperial
    
    Currently calling city number 3949:
    suleja, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=suleja,ng&units=imperial
    
    Currently calling city number 3950:
    harbour breton, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=harbour breton,ca&units=imperial
    
    Currently calling city number 3951:
    chingola, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chingola,zm&units=imperial
    
    Currently calling city number 3952:
    carupano, ve
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=carupano,ve&units=imperial
    
    Currently calling city number 3953:
    shirak, am
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shirak,am&units=imperial
    
    Currently calling city number 3954:
    arica, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=arica,cl&units=imperial
    
    Currently calling city number 3955:
    ouesso, cg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ouesso,cg&units=imperial
    
    Currently calling city number 3956:
    kutahya, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kutahya,tr&units=imperial
    
    Currently calling city number 3957:
    west fargo, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=west fargo,us&units=imperial
    
    Currently calling city number 3958:
    francistown, bw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=francistown,bw&units=imperial
    
    Currently calling city number 3959:
    casablanca, ma
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=casablanca,ma&units=imperial
    
    Currently calling city number 3960:
    barbosa ferraz, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=barbosa ferraz,br&units=imperial
    
    Currently calling city number 3961:
    mazatlan, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mazatlan,mx&units=imperial
    
    Currently calling city number 3962:
    siyabuswa, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=siyabuswa,za&units=imperial
    
    Currently calling city number 3963:
    niquero, cu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=niquero,cu&units=imperial
    
    Currently calling city number 3964:
    port antonio, jm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=port antonio,jm&units=imperial
    
    Currently calling city number 3965:
    rovaniemi, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rovaniemi,fi&units=imperial
    
    Currently calling city number 3966:
    stawell, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=stawell,au&units=imperial
    
    Currently calling city number 3967:
    sehithwa, bw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sehithwa,bw&units=imperial
    
    Currently calling city number 3968:
    finschhafen, pg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=finschhafen,pg&units=imperial
    
    Currently calling city number 3969:
    panguipulli, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=panguipulli,cl&units=imperial
    
    Currently calling city number 3970:
    abay, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=abay,kz&units=imperial
    
    Currently calling city number 3971:
    marsassoum, sn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=marsassoum,sn&units=imperial
    
    Currently calling city number 3972:
    shurugwi, zw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shurugwi,zw&units=imperial
    
    Currently calling city number 3973:
    honavar, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=honavar,in&units=imperial
    
    Currently calling city number 3974:
    szalkszentmarton, hu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=szalkszentmarton,hu&units=imperial
    
    Currently calling city number 3975:
    nkongsamba, cm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nkongsamba,cm&units=imperial
    
    Currently calling city number 3976:
    doba, td
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=doba,td&units=imperial
    
    Currently calling city number 3977:
    utinga, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=utinga,br&units=imperial
    
    Currently calling city number 3978:
    kampot, kh
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kampot,kh&units=imperial
    
    Currently calling city number 3979:
    ebolowa, cm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ebolowa,cm&units=imperial
    
    Currently calling city number 3980:
    nkhotakota, mw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nkhotakota,mw&units=imperial
    
    Currently calling city number 3981:
    san matias, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san matias,bo&units=imperial
    
    Currently calling city number 3982:
    susner, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=susner,in&units=imperial
    
    Currently calling city number 3983:
    middelburg, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=middelburg,za&units=imperial
    
    Currently calling city number 3984:
    krasnorechenskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=krasnorechenskiy,ru&units=imperial
    
    Currently calling city number 3985:
    arraias, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=arraias,br&units=imperial
    
    Currently calling city number 3986:
    loandjili, cg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=loandjili,cg&units=imperial
    
    Currently calling city number 3987:
    siocon, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=siocon,ph&units=imperial
    
    Currently calling city number 3988:
    leticia, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=leticia,co&units=imperial
    
    Currently calling city number 3989:
    medak, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=medak,in&units=imperial
    
    Currently calling city number 3990:
    east wenatchee bench, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=east wenatchee bench,us&units=imperial
    
    Currently calling city number 3991:
    charlottetown, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=charlottetown,ca&units=imperial
    
    Currently calling city number 3992:
    suileng, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=suileng,cn&units=imperial
    
    Currently calling city number 3993:
    kostomuksha, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kostomuksha,ru&units=imperial
    
    Currently calling city number 3994:
    mustasaari, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mustasaari,fi&units=imperial
    
    Currently calling city number 3995:
    iberia, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=iberia,pe&units=imperial
    
    Currently calling city number 3996:
    jashpurnagar, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jashpurnagar,in&units=imperial
    
    Currently calling city number 3997:
    alegrete, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alegrete,br&units=imperial
    
    Currently calling city number 3998:
    neman, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=neman,ru&units=imperial
    
    Currently calling city number 3999:
    ushtobe, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ushtobe,kz&units=imperial
    
    Currently calling city number 4000:
    chickasha, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chickasha,us&units=imperial
    
    Currently calling city number 4001:
    grafing, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=grafing,de&units=imperial
    
    Currently calling city number 4002:
    saskatoon, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saskatoon,ca&units=imperial
    
    Currently calling city number 4003:
    kibre mengist, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kibre mengist,et&units=imperial
    
    Currently calling city number 4004:
    caluquembe, ao
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=caluquembe,ao&units=imperial
    
    Currently calling city number 4005:
    bandrele, yt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bandrele,yt&units=imperial
    
    Currently calling city number 4006:
    guider, cm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=guider,cm&units=imperial
    
    Currently calling city number 4007:
    selaphum, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=selaphum,th&units=imperial
    
    Currently calling city number 4008:
    killybegs, ie
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=killybegs,ie&units=imperial
    
    Currently calling city number 4009:
    mwandiga, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mwandiga,tz&units=imperial
    
    Currently calling city number 4010:
    tenno, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tenno,jp&units=imperial
    
    Currently calling city number 4011:
    key west, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=key west,us&units=imperial
    
    Currently calling city number 4012:
    bestobe, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bestobe,kz&units=imperial
    
    Currently calling city number 4013:
    houston, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=houston,ca&units=imperial
    
    Currently calling city number 4014:
    tucupita, ve
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tucupita,ve&units=imperial
    
    Currently calling city number 4015:
    staropyshminsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=staropyshminsk,ru&units=imperial
    
    Currently calling city number 4016:
    ilinskoye-khovanskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ilinskoye-khovanskoye,ru&units=imperial
    
    Currently calling city number 4017:
    yarega, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yarega,ru&units=imperial
    
    Currently calling city number 4018:
    gwadar, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gwadar,pk&units=imperial
    
    Currently calling city number 4019:
    marmaris, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=marmaris,tr&units=imperial
    
    Currently calling city number 4020:
    panama city, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=panama city,us&units=imperial
    
    Currently calling city number 4021:
    koraput, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=koraput,in&units=imperial
    
    Currently calling city number 4022:
    biloela, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=biloela,au&units=imperial
    
    Currently calling city number 4023:
    serebryansk, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=serebryansk,kz&units=imperial
    
    Currently calling city number 4024:
    contamana, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=contamana,pe&units=imperial
    
    Currently calling city number 4025:
    bealanana, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bealanana,mg&units=imperial
    
    Currently calling city number 4026:
    magdagachi, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=magdagachi,ru&units=imperial
    
    Currently calling city number 4027:
    rancharia, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rancharia,br&units=imperial
    
    Currently calling city number 4028:
    mapiripan, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mapiripan,co&units=imperial
    
    Currently calling city number 4029:
    nawa, sy
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nawa,sy&units=imperial
    
    Currently calling city number 4030:
    ruswil, ch
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ruswil,ch&units=imperial
    
    Currently calling city number 4031:
    chaman, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chaman,pk&units=imperial
    
    Currently calling city number 4032:
    les cayes, ht
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=les cayes,ht&units=imperial
    
    Currently calling city number 4033:
    mitha tiwana, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mitha tiwana,pk&units=imperial
    
    Currently calling city number 4034:
    chany, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chany,ru&units=imperial
    
    Currently calling city number 4035:
    valvedditturai, lk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=valvedditturai,lk&units=imperial
    
    Currently calling city number 4036:
    loa janan, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=loa janan,id&units=imperial
    
    Currently calling city number 4037:
    cabinda, ao
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cabinda,ao&units=imperial
    
    Currently calling city number 4038:
    zyryanskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zyryanskoye,ru&units=imperial
    
    Currently calling city number 4039:
    kisanga, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kisanga,tz&units=imperial
    
    Currently calling city number 4040:
    makaha, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=makaha,us&units=imperial
    
    Currently calling city number 4041:
    kerrville, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kerrville,us&units=imperial
    
    Currently calling city number 4042:
    galveston, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=galveston,us&units=imperial
    
    Currently calling city number 4043:
    yashkul, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yashkul,ru&units=imperial
    
    Currently calling city number 4044:
    winston, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=winston,us&units=imperial
    
    Currently calling city number 4045:
    bitkine, td
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bitkine,td&units=imperial
    
    Currently calling city number 4046:
    bonfim, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bonfim,br&units=imperial
    
    Currently calling city number 4047:
    banjarmasin, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=banjarmasin,id&units=imperial
    
    Currently calling city number 4048:
    peyima, sl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=peyima,sl&units=imperial
    
    Currently calling city number 4049:
    jega, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jega,ng&units=imperial
    
    Currently calling city number 4050:
    goianesia, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=goianesia,br&units=imperial
    
    Currently calling city number 4051:
    salamanca, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=salamanca,es&units=imperial
    
    Currently calling city number 4052:
    ushumun, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ushumun,ru&units=imperial
    
    Currently calling city number 4053:
    sayat, tm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sayat,tm&units=imperial
    
    Currently calling city number 4054:
    jagtial, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jagtial,in&units=imperial
    
    Currently calling city number 4055:
    venado tuerto, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=venado tuerto,ar&units=imperial
    
    Currently calling city number 4056:
    devils lake, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=devils lake,us&units=imperial
    
    Currently calling city number 4057:
    la union, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=la union,mx&units=imperial
    
    Currently calling city number 4058:
    eldorado, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=eldorado,mx&units=imperial
    
    Currently calling city number 4059:
    kousseri, cm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kousseri,cm&units=imperial
    
    Currently calling city number 4060:
    bahia honda, cu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bahia honda,cu&units=imperial
    
    Currently calling city number 4061:
    wagga wagga, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wagga wagga,au&units=imperial
    
    Currently calling city number 4062:
    taber, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=taber,ca&units=imperial
    
    Currently calling city number 4063:
    barhi, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=barhi,in&units=imperial
    
    Currently calling city number 4064:
    bermeo, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bermeo,es&units=imperial
    
    Currently calling city number 4065:
    moyale, ke
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=moyale,ke&units=imperial
    
    Currently calling city number 4066:
    basi, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=basi,in&units=imperial
    
    Currently calling city number 4067:
    semirom, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=semirom,ir&units=imperial
    
    Currently calling city number 4068:
    foz, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=foz,es&units=imperial
    
    Currently calling city number 4069:
    masvingo, zw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=masvingo,zw&units=imperial
    
    Currently calling city number 4070:
    libano, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=libano,co&units=imperial
    
    Currently calling city number 4071:
    bichura, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bichura,ru&units=imperial
    
    Currently calling city number 4072:
    tula, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tula,mx&units=imperial
    
    Currently calling city number 4073:
    san miguel, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san miguel,pe&units=imperial
    
    Currently calling city number 4074:
    atocha, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=atocha,bo&units=imperial
    
    Currently calling city number 4075:
    rakaia, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rakaia,nz&units=imperial
    
    Currently calling city number 4076:
    druzhba, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=druzhba,ua&units=imperial
    
    Currently calling city number 4077:
    ciocanesti, ro
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ciocanesti,ro&units=imperial
    
    Currently calling city number 4078:
    taman, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=taman,id&units=imperial
    
    Currently calling city number 4079:
    coria, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=coria,es&units=imperial
    
    Currently calling city number 4080:
    alberton, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alberton,ca&units=imperial
    
    Currently calling city number 4081:
    youghal, ie
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=youghal,ie&units=imperial
    
    Currently calling city number 4082:
    crib point, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=crib point,au&units=imperial
    
    Currently calling city number 4083:
    tansen, np
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tansen,np&units=imperial
    
    Currently calling city number 4084:
    qingyuan, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=qingyuan,cn&units=imperial
    
    Currently calling city number 4085:
    ziarat, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ziarat,pk&units=imperial
    
    Currently calling city number 4086:
    macrohon, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=macrohon,ph&units=imperial
    
    Currently calling city number 4087:
    aranda de duero, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aranda de duero,es&units=imperial
    
    Currently calling city number 4088:
    los andes, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=los andes,cl&units=imperial
    
    Currently calling city number 4089:
    severomuysk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=severomuysk,ru&units=imperial
    
    Currently calling city number 4090:
    dasoguz, tm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dasoguz,tm&units=imperial
    
    Currently calling city number 4091:
    pitea, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pitea,se&units=imperial
    
    Currently calling city number 4092:
    limbe, cm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=limbe,cm&units=imperial
    
    Currently calling city number 4093:
    matelandia, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=matelandia,br&units=imperial
    
    Currently calling city number 4094:
    kasama, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kasama,zm&units=imperial
    
    Currently calling city number 4095:
    taganak, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=taganak,ph&units=imperial
    
    Currently calling city number 4096:
    los alamos, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=los alamos,us&units=imperial
    
    Currently calling city number 4097:
    berbera, so
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=berbera,so&units=imperial
    
    Currently calling city number 4098:
    butia, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=butia,br&units=imperial
    
    Currently calling city number 4099:
    helsinki, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=helsinki,fi&units=imperial
    
    Currently calling city number 4100:
    manakara, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=manakara,mg&units=imperial
    
    Currently calling city number 4101:
    rathdrum, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rathdrum,us&units=imperial
    
    Currently calling city number 4102:
    palmeiras de goias, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=palmeiras de goias,br&units=imperial
    
    Currently calling city number 4103:
    tashla, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tashla,ru&units=imperial
    
    Currently calling city number 4104:
    nong khai, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nong khai,th&units=imperial
    
    Currently calling city number 4105:
    oda, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=oda,jp&units=imperial
    
    Currently calling city number 4106:
    waupun, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=waupun,us&units=imperial
    
    Currently calling city number 4107:
    santa helena de goias, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santa helena de goias,br&units=imperial
    
    Currently calling city number 4108:
    ifakara, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ifakara,tz&units=imperial
    
    Currently calling city number 4109:
    la mana, ec
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=la mana,ec&units=imperial
    
    Currently calling city number 4110:
    pala, td
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pala,td&units=imperial
    
    Currently calling city number 4111:
    nassau, bs
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nassau,bs&units=imperial
    
    Currently calling city number 4112:
    stromness, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=stromness,gb&units=imperial
    
    Currently calling city number 4113:
    zabid, ye
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zabid,ye&units=imperial
    
    Currently calling city number 4114:
    torva, ee
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=torva,ee&units=imperial
    
    Currently calling city number 4115:
    gondal, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gondal,in&units=imperial
    
    Currently calling city number 4116:
    cotonou, bj
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cotonou,bj&units=imperial
    
    Currently calling city number 4117:
    norton shores, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=norton shores,us&units=imperial
    
    Currently calling city number 4118:
    suslovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=suslovo,ru&units=imperial
    
    Currently calling city number 4119:
    young, uy
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=young,uy&units=imperial
    
    Currently calling city number 4120:
    lewiston, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lewiston,us&units=imperial
    
    Currently calling city number 4121:
    dzilam gonzalez, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dzilam gonzalez,mx&units=imperial
    
    Currently calling city number 4122:
    nevers, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nevers,fr&units=imperial
    
    Currently calling city number 4123:
    ekhabi, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ekhabi,ru&units=imperial
    
    Currently calling city number 4124:
    dosso, ne
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dosso,ne&units=imperial
    
    Currently calling city number 4125:
    lang suan, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lang suan,th&units=imperial
    
    Currently calling city number 4126:
    joao pinheiro, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=joao pinheiro,br&units=imperial
    
    Currently calling city number 4127:
    grimshaw, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=grimshaw,ca&units=imperial
    
    Currently calling city number 4128:
    el tarra, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=el tarra,co&units=imperial
    
    Currently calling city number 4129:
    kyzyl-suu, kg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kyzyl-suu,kg&units=imperial
    
    Currently calling city number 4130:
    griffith, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=griffith,au&units=imperial
    
    Currently calling city number 4131:
    korhogo, ci
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=korhogo,ci&units=imperial
    
    Currently calling city number 4132:
    dafeng, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dafeng,cn&units=imperial
    
    Currently calling city number 4133:
    cua, ve
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cua,ve&units=imperial
    
    Currently calling city number 4134:
    ipatovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ipatovo,ru&units=imperial
    
    Currently calling city number 4135:
    vozdvizhenka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vozdvizhenka,ru&units=imperial
    
    Currently calling city number 4136:
    tourlaville, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tourlaville,fr&units=imperial
    
    Currently calling city number 4137:
    suar, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=suar,in&units=imperial
    
    Currently calling city number 4138:
    normandin, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=normandin,ca&units=imperial
    
    Currently calling city number 4139:
    braco do norte, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=braco do norte,br&units=imperial
    
    Currently calling city number 4140:
    qom, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=qom,ir&units=imperial
    
    Currently calling city number 4141:
    tonk, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tonk,in&units=imperial
    
    Currently calling city number 4142:
    khandagayty, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=khandagayty,ru&units=imperial
    
    Currently calling city number 4143:
    novouzensk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=novouzensk,ru&units=imperial
    
    Currently calling city number 4144:
    aberdeen, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aberdeen,us&units=imperial
    
    Currently calling city number 4145:
    kisiwani, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kisiwani,tz&units=imperial
    
    Currently calling city number 4146:
    oromocto, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=oromocto,ca&units=imperial
    
    Currently calling city number 4147:
    posse, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=posse,br&units=imperial
    
    Currently calling city number 4148:
    serov, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=serov,ru&units=imperial
    
    Currently calling city number 4149:
    langley park, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=langley park,us&units=imperial
    
    Currently calling city number 4150:
    achit, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=achit,ru&units=imperial
    
    Currently calling city number 4151:
    mishkino, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mishkino,ru&units=imperial
    
    Currently calling city number 4152:
    bhinmal, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bhinmal,in&units=imperial
    
    Currently calling city number 4153:
    jiamusi, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jiamusi,cn&units=imperial
    
    Currently calling city number 4154:
    hengyang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hengyang,cn&units=imperial
    
    Currently calling city number 4155:
    jaruco, cu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jaruco,cu&units=imperial
    
    Currently calling city number 4156:
    novaya igirma, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=novaya igirma,ru&units=imperial
    
    Currently calling city number 4157:
    anqing, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=anqing,cn&units=imperial
    
    Currently calling city number 4158:
    iaciara, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=iaciara,br&units=imperial
    
    Currently calling city number 4159:
    save, bj
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=save,bj&units=imperial
    
    Currently calling city number 4160:
    kaohsiung, tw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kaohsiung,tw&units=imperial
    
    Currently calling city number 4161:
    campo verde, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=campo verde,br&units=imperial
    
    Currently calling city number 4162:
    osuna, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=osuna,es&units=imperial
    
    Currently calling city number 4163:
    patan, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=patan,in&units=imperial
    
    Currently calling city number 4164:
    sao joao do paraiso, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sao joao do paraiso,br&units=imperial
    
    Currently calling city number 4165:
    huangpi, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=huangpi,cn&units=imperial
    
    Currently calling city number 4166:
    mogocha, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mogocha,ru&units=imperial
    
    Currently calling city number 4167:
    butterworth, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=butterworth,za&units=imperial
    
    Currently calling city number 4168:
    bozeman, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bozeman,us&units=imperial
    
    Currently calling city number 4169:
    kuvshinovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kuvshinovo,ru&units=imperial
    
    Currently calling city number 4170:
    yuksekova, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yuksekova,tr&units=imperial
    
    Currently calling city number 4171:
    qiongshan, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=qiongshan,cn&units=imperial
    
    Currently calling city number 4172:
    ligayan, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ligayan,ph&units=imperial
    
    Currently calling city number 4173:
    ellensburg, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ellensburg,us&units=imperial
    
    Currently calling city number 4174:
    sarlat-la-caneda, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sarlat-la-caneda,fr&units=imperial
    
    Currently calling city number 4175:
    brandon, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=brandon,ca&units=imperial
    
    Currently calling city number 4176:
    leczyca, pl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=leczyca,pl&units=imperial
    
    Currently calling city number 4177:
    zhaotong, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zhaotong,cn&units=imperial
    
    Currently calling city number 4178:
    port lavaca, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=port lavaca,us&units=imperial
    
    Currently calling city number 4179:
    thyolo, mw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=thyolo,mw&units=imperial
    
    Currently calling city number 4180:
    new smyrna beach, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=new smyrna beach,us&units=imperial
    
    Currently calling city number 4181:
    jinzhou, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jinzhou,cn&units=imperial
    
    Currently calling city number 4182:
    lagunas, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lagunas,pe&units=imperial
    
    Currently calling city number 4183:
    machico, pt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=machico,pt&units=imperial
    
    Currently calling city number 4184:
    beaune, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=beaune,fr&units=imperial
    
    Currently calling city number 4185:
    tumut, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tumut,au&units=imperial
    
    Currently calling city number 4186:
    selenginsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=selenginsk,ru&units=imperial
    
    Currently calling city number 4187:
    rabak, sd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rabak,sd&units=imperial
    
    Currently calling city number 4188:
    north augusta, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=north augusta,us&units=imperial
    
    Currently calling city number 4189:
    sao mateus, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sao mateus,br&units=imperial
    
    Currently calling city number 4190:
    zhucheng, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zhucheng,cn&units=imperial
    
    Currently calling city number 4191:
    mallaig, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mallaig,gb&units=imperial
    
    Currently calling city number 4192:
    am timan, td
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=am timan,td&units=imperial
    
    Currently calling city number 4193:
    biggar, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=biggar,ca&units=imperial
    
    Currently calling city number 4194:
    rafaela, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rafaela,ar&units=imperial
    
    Currently calling city number 4195:
    taiobeiras, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=taiobeiras,br&units=imperial
    
    Currently calling city number 4196:
    white rock, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=white rock,ca&units=imperial
    
    Currently calling city number 4197:
    arivonimamo, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=arivonimamo,mg&units=imperial
    
    Currently calling city number 4198:
    mercedes, uy
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mercedes,uy&units=imperial
    
    Currently calling city number 4199:
    nyrad, dk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nyrad,dk&units=imperial
    
    Currently calling city number 4200:
    dharur, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dharur,in&units=imperial
    
    Currently calling city number 4201:
    gorodovikovsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gorodovikovsk,ru&units=imperial
    
    Currently calling city number 4202:
    huallanca, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=huallanca,pe&units=imperial
    
    Currently calling city number 4203:
    obeliai, lt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=obeliai,lt&units=imperial
    
    Currently calling city number 4204:
    benghazi, ly
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=benghazi,ly&units=imperial
    
    Currently calling city number 4205:
    ganganagar, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ganganagar,in&units=imperial
    
    Currently calling city number 4206:
    kavalerovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kavalerovo,ru&units=imperial
    
    Currently calling city number 4207:
    prachuap khiri khan, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=prachuap khiri khan,th&units=imperial
    
    Currently calling city number 4208:
    songjianghe, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=songjianghe,cn&units=imperial
    
    Currently calling city number 4209:
    uyo, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=uyo,ng&units=imperial
    
    Currently calling city number 4210:
    danilovka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=danilovka,ru&units=imperial
    
    Currently calling city number 4211:
    berezovyy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=berezovyy,ru&units=imperial
    
    Currently calling city number 4212:
    viganello, ch
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=viganello,ch&units=imperial
    
    Currently calling city number 4213:
    stolac, ba
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=stolac,ba&units=imperial
    
    Currently calling city number 4214:
    juquitiba, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=juquitiba,br&units=imperial
    
    Currently calling city number 4215:
    beauceville, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=beauceville,ca&units=imperial
    
    Currently calling city number 4216:
    les escoumins, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=les escoumins,ca&units=imperial
    
    Currently calling city number 4217:
    wembley, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wembley,ca&units=imperial
    
    Currently calling city number 4218:
    whitley bay, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=whitley bay,gb&units=imperial
    
    Currently calling city number 4219:
    kragujevac, rs
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kragujevac,rs&units=imperial
    
    Currently calling city number 4220:
    yurla, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yurla,ru&units=imperial
    
    Currently calling city number 4221:
    myaundzha, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=myaundzha,ru&units=imperial
    
    Currently calling city number 4222:
    partizanskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=partizanskoye,ru&units=imperial
    
    Currently calling city number 4223:
    svit, sk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=svit,sk&units=imperial
    
    Currently calling city number 4224:
    mason city, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mason city,us&units=imperial
    
    Currently calling city number 4225:
    kathu, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kathu,th&units=imperial
    
    Currently calling city number 4226:
    xiaolingwei, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=xiaolingwei,cn&units=imperial
    
    Currently calling city number 4227:
    sabaudia, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sabaudia,it&units=imperial
    
    Currently calling city number 4228:
    bernalillo, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bernalillo,us&units=imperial
    
    Currently calling city number 4229:
    chaiyaphum, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chaiyaphum,th&units=imperial
    
    Currently calling city number 4230:
    kopychyntsi, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kopychyntsi,ua&units=imperial
    
    Currently calling city number 4231:
    vyazma, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vyazma,ru&units=imperial
    
    Currently calling city number 4232:
    corn island, ni
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=corn island,ni&units=imperial
    
    Currently calling city number 4233:
    kingori, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kingori,tz&units=imperial
    
    Currently calling city number 4234:
    chau doc, vn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chau doc,vn&units=imperial
    
    Currently calling city number 4235:
    nzerekore, gn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nzerekore,gn&units=imperial
    
    Currently calling city number 4236:
    badarganj, bd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=badarganj,bd&units=imperial
    
    Currently calling city number 4237:
    makokou, ga
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=makokou,ga&units=imperial
    
    Currently calling city number 4238:
    wittlich, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wittlich,de&units=imperial
    
    Currently calling city number 4239:
    tangshan, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tangshan,cn&units=imperial
    
    Currently calling city number 4240:
    mapiri, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mapiri,bo&units=imperial
    
    Currently calling city number 4241:
    bako, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bako,et&units=imperial
    
    Currently calling city number 4242:
    beterou, bj
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=beterou,bj&units=imperial
    
    Currently calling city number 4243:
    saint pete beach, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saint pete beach,us&units=imperial
    
    Currently calling city number 4244:
    mtwara, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mtwara,tz&units=imperial
    
    Currently calling city number 4245:
    sengiley, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sengiley,ru&units=imperial
    
    Currently calling city number 4246:
    vaasa, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vaasa,fi&units=imperial
    
    Currently calling city number 4247:
    bousso, td
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bousso,td&units=imperial
    
    Currently calling city number 4248:
    istok, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=istok,ru&units=imperial
    
    Currently calling city number 4249:
    tank, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tank,pk&units=imperial
    
    Currently calling city number 4250:
    buenos aires, cr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=buenos aires,cr&units=imperial
    
    Currently calling city number 4251:
    bydgoszcz, pl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bydgoszcz,pl&units=imperial
    
    Currently calling city number 4252:
    san benito abad, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san benito abad,co&units=imperial
    
    Currently calling city number 4253:
    barentu, er
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=barentu,er&units=imperial
    
    Currently calling city number 4254:
    kyra, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kyra,ru&units=imperial
    
    Currently calling city number 4255:
    laje, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=laje,br&units=imperial
    
    Currently calling city number 4256:
    tamandare, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tamandare,br&units=imperial
    
    Currently calling city number 4257:
    zanesville, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zanesville,us&units=imperial
    
    Currently calling city number 4258:
    fasa, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fasa,ir&units=imperial
    
    Currently calling city number 4259:
    leo, bf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=leo,bf&units=imperial
    
    Currently calling city number 4260:
    kommunisticheskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kommunisticheskiy,ru&units=imperial
    
    Currently calling city number 4261:
    mammaste, ee
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mammaste,ee&units=imperial
    
    Currently calling city number 4262:
    kirovsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kirovsk,ru&units=imperial
    
    Currently calling city number 4263:
    bratsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bratsk,ru&units=imperial
    
    Currently calling city number 4264:
    kango, ga
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kango,ga&units=imperial
    
    Currently calling city number 4265:
    coatesville, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=coatesville,us&units=imperial
    
    Currently calling city number 4266:
    villa bruzual, ve
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=villa bruzual,ve&units=imperial
    
    Currently calling city number 4267:
    burla, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=burla,ru&units=imperial
    
    Currently calling city number 4268:
    weyburn, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=weyburn,ca&units=imperial
    
    Currently calling city number 4269:
    wanlaweyn, so
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wanlaweyn,so&units=imperial
    
    Currently calling city number 4270:
    timra, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=timra,se&units=imperial
    
    Currently calling city number 4271:
    yongchang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yongchang,cn&units=imperial
    
    Currently calling city number 4272:
    oxford, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=oxford,nz&units=imperial
    
    Currently calling city number 4273:
    ivankiv, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ivankiv,ua&units=imperial
    
    Currently calling city number 4274:
    natitingou, bj
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=natitingou,bj&units=imperial
    
    Currently calling city number 4275:
    fianarantsoa, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fianarantsoa,mg&units=imperial
    
    Currently calling city number 4276:
    phalombe, mw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=phalombe,mw&units=imperial
    
    Currently calling city number 4277:
    kanevskaya, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kanevskaya,ru&units=imperial
    
    Currently calling city number 4278:
    agnibilekrou, ci
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=agnibilekrou,ci&units=imperial
    
    Currently calling city number 4279:
    seminole, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=seminole,us&units=imperial
    
    Currently calling city number 4280:
    rio cuarto, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rio cuarto,ar&units=imperial
    
    Currently calling city number 4281:
    spencer, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=spencer,us&units=imperial
    
    Currently calling city number 4282:
    elliot lake, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=elliot lake,ca&units=imperial
    
    Currently calling city number 4283:
    ejura, gh
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ejura,gh&units=imperial
    
    Currently calling city number 4284:
    fecamp, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fecamp,fr&units=imperial
    
    Currently calling city number 4285:
    sainte-adele, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sainte-adele,ca&units=imperial
    
    Currently calling city number 4286:
    darbhanga, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=darbhanga,in&units=imperial
    
    Currently calling city number 4287:
    ayr, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ayr,au&units=imperial
    
    Currently calling city number 4288:
    gushi, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gushi,cn&units=imperial
    
    Currently calling city number 4289:
    acayucan, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=acayucan,mx&units=imperial
    
    Currently calling city number 4290:
    sokol, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sokol,ru&units=imperial
    
    Currently calling city number 4291:
    chichibu, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chichibu,jp&units=imperial
    
    Currently calling city number 4292:
    balyaga, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=balyaga,ru&units=imperial
    
    Currently calling city number 4293:
    kartaly, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kartaly,ru&units=imperial
    
    Currently calling city number 4294:
    tshane, bw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tshane,bw&units=imperial
    
    Currently calling city number 4295:
    westwood, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=westwood,us&units=imperial
    
    Currently calling city number 4296:
    temyasovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=temyasovo,ru&units=imperial
    
    Currently calling city number 4297:
    south yuba city, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=south yuba city,us&units=imperial
    
    Currently calling city number 4298:
    ulu-telyak, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ulu-telyak,ru&units=imperial
    
    Currently calling city number 4299:
    thibodaux, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=thibodaux,us&units=imperial
    
    Currently calling city number 4300:
    huanren, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=huanren,cn&units=imperial
    
    Currently calling city number 4301:
    orangeville, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=orangeville,ca&units=imperial
    
    Currently calling city number 4302:
    gubkinskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gubkinskiy,ru&units=imperial
    
    Currently calling city number 4303:
    mapimi, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mapimi,mx&units=imperial
    
    Currently calling city number 4304:
    ha giang, vn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ha giang,vn&units=imperial
    
    Currently calling city number 4305:
    mugur-aksy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mugur-aksy,ru&units=imperial
    
    Currently calling city number 4306:
    loudi, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=loudi,cn&units=imperial
    
    Currently calling city number 4307:
    priiskovyy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=priiskovyy,ru&units=imperial
    
    Currently calling city number 4308:
    qitaihe, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=qitaihe,cn&units=imperial
    
    Currently calling city number 4309:
    resistencia, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=resistencia,ar&units=imperial
    
    Currently calling city number 4310:
    bakchar, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bakchar,ru&units=imperial
    
    Currently calling city number 4311:
    tres picos, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tres picos,mx&units=imperial
    
    Currently calling city number 4312:
    pascagoula, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pascagoula,us&units=imperial
    
    Currently calling city number 4313:
    vicksburg, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vicksburg,us&units=imperial
    
    Currently calling city number 4314:
    peque, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=peque,co&units=imperial
    
    Currently calling city number 4315:
    mto wa mbu, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mto wa mbu,tz&units=imperial
    
    Currently calling city number 4316:
    komatipoort, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=komatipoort,za&units=imperial
    
    Currently calling city number 4317:
    pleasanton, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pleasanton,us&units=imperial
    
    Currently calling city number 4318:
    sidhi, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sidhi,in&units=imperial
    
    Currently calling city number 4319:
    kholodnyy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kholodnyy,ru&units=imperial
    
    Currently calling city number 4320:
    nawa, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nawa,in&units=imperial
    
    Currently calling city number 4321:
    ziro, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ziro,in&units=imperial
    
    Currently calling city number 4322:
    razole, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=razole,in&units=imperial
    
    Currently calling city number 4323:
    van, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=van,tr&units=imperial
    
    Currently calling city number 4324:
    yokadouma, cm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yokadouma,cm&units=imperial
    
    Currently calling city number 4325:
    regen, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=regen,de&units=imperial
    
    Currently calling city number 4326:
    metro, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=metro,id&units=imperial
    
    Currently calling city number 4327:
    yauya, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yauya,pe&units=imperial
    
    Currently calling city number 4328:
    kayes, ml
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kayes,ml&units=imperial
    
    Currently calling city number 4329:
    diamantino, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=diamantino,br&units=imperial
    
    Currently calling city number 4330:
    yayva, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yayva,ru&units=imperial
    
    Currently calling city number 4331:
    rindal, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rindal,no&units=imperial
    
    Currently calling city number 4332:
    ustyuzhna, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ustyuzhna,ru&units=imperial
    
    Currently calling city number 4333:
    suruc, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=suruc,tr&units=imperial
    
    Currently calling city number 4334:
    tingo maria, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tingo maria,pe&units=imperial
    
    Currently calling city number 4335:
    velsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=velsk,ru&units=imperial
    
    Currently calling city number 4336:
    tello, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tello,co&units=imperial
    
    Currently calling city number 4337:
    cape coast, gh
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cape coast,gh&units=imperial
    
    Currently calling city number 4338:
    heyang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=heyang,cn&units=imperial
    
    Currently calling city number 4339:
    sabinov, sk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sabinov,sk&units=imperial
    
    Currently calling city number 4340:
    weiser, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=weiser,us&units=imperial
    
    Currently calling city number 4341:
    hansi, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hansi,in&units=imperial
    
    Currently calling city number 4342:
    terrasini, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=terrasini,it&units=imperial
    
    Currently calling city number 4343:
    devonport, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=devonport,au&units=imperial
    
    Currently calling city number 4344:
    calgary, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=calgary,ca&units=imperial
    
    Currently calling city number 4345:
    san luis, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san luis,mx&units=imperial
    
    Currently calling city number 4346:
    kannangad, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kannangad,in&units=imperial
    
    Currently calling city number 4347:
    praxedis guerrero, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=praxedis guerrero,mx&units=imperial
    
    Currently calling city number 4348:
    parfino, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=parfino,ru&units=imperial
    
    Currently calling city number 4349:
    barguzin, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=barguzin,ru&units=imperial
    
    Currently calling city number 4350:
    lamont, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lamont,ca&units=imperial
    
    Currently calling city number 4351:
    guanare, ve
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=guanare,ve&units=imperial
    
    Currently calling city number 4352:
    lubaczow, pl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lubaczow,pl&units=imperial
    
    Currently calling city number 4353:
    kingsville, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kingsville,us&units=imperial
    
    Currently calling city number 4354:
    alvinopolis, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alvinopolis,br&units=imperial
    
    Currently calling city number 4355:
    uyuni, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=uyuni,bo&units=imperial
    
    Currently calling city number 4356:
    statesville, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=statesville,us&units=imperial
    
    Currently calling city number 4357:
    weston, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=weston,us&units=imperial
    
    Currently calling city number 4358:
    alto araguaia, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alto araguaia,br&units=imperial
    
    Currently calling city number 4359:
    pushkinskiye gory, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pushkinskiye gory,ru&units=imperial
    
    Currently calling city number 4360:
    bang lamung, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bang lamung,th&units=imperial
    
    Currently calling city number 4361:
    cerrik, al
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cerrik,al&units=imperial
    
    Currently calling city number 4362:
    kihei, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kihei,us&units=imperial
    
    Currently calling city number 4363:
    esfahan, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=esfahan,ir&units=imperial
    
    Currently calling city number 4364:
    chachapoyas, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chachapoyas,pe&units=imperial
    
    Currently calling city number 4365:
    coracora, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=coracora,pe&units=imperial
    
    Currently calling city number 4366:
    phalodi, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=phalodi,in&units=imperial
    
    Currently calling city number 4367:
    gordeyevka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gordeyevka,ru&units=imperial
    
    Currently calling city number 4368:
    bantry, ie
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bantry,ie&units=imperial
    
    Currently calling city number 4369:
    elban, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=elban,ru&units=imperial
    
    Currently calling city number 4370:
    jaguarari, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jaguarari,br&units=imperial
    
    Currently calling city number 4371:
    campina grande do sul, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=campina grande do sul,br&units=imperial
    
    Currently calling city number 4372:
    prachatice, cz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=prachatice,cz&units=imperial
    
    Currently calling city number 4373:
    basoko, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=basoko,cd&units=imperial
    
    Currently calling city number 4374:
    adiake, ci
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=adiake,ci&units=imperial
    
    Currently calling city number 4375:
    ellsworth, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ellsworth,us&units=imperial
    
    Currently calling city number 4376:
    sao jose de ribamar, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sao jose de ribamar,br&units=imperial
    
    Currently calling city number 4377:
    ingersoll, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ingersoll,ca&units=imperial
    
    Currently calling city number 4378:
    alanya, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alanya,tr&units=imperial
    
    Currently calling city number 4379:
    castlemaine, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=castlemaine,au&units=imperial
    
    Currently calling city number 4380:
    paoua, cf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=paoua,cf&units=imperial
    
    Currently calling city number 4381:
    paharpur, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=paharpur,pk&units=imperial
    
    Currently calling city number 4382:
    srednebelaya, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=srednebelaya,ru&units=imperial
    
    Currently calling city number 4383:
    peace river, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=peace river,ca&units=imperial
    
    Currently calling city number 4384:
    ouro fino, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ouro fino,br&units=imperial
    
    Currently calling city number 4385:
    atakpame, tg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=atakpame,tg&units=imperial
    
    Currently calling city number 4386:
    cuenca, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cuenca,es&units=imperial
    
    Currently calling city number 4387:
    eura, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=eura,fi&units=imperial
    
    Currently calling city number 4388:
    amazar, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=amazar,ru&units=imperial
    
    Currently calling city number 4389:
    wahpeton, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wahpeton,us&units=imperial
    
    Currently calling city number 4390:
    katy, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=katy,us&units=imperial
    
    Currently calling city number 4391:
    arantangi, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=arantangi,in&units=imperial
    
    Currently calling city number 4392:
    glubokiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=glubokiy,ru&units=imperial
    
    Currently calling city number 4393:
    druskininkai, lt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=druskininkai,lt&units=imperial
    
    Currently calling city number 4394:
    ibate, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ibate,br&units=imperial
    
    Currently calling city number 4395:
    waraseoni, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=waraseoni,in&units=imperial
    
    Currently calling city number 4396:
    begun, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=begun,in&units=imperial
    
    Currently calling city number 4397:
    businga, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=businga,cd&units=imperial
    
    Currently calling city number 4398:
    payyannur, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=payyannur,in&units=imperial
    
    Currently calling city number 4399:
    tlalnepantla, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tlalnepantla,mx&units=imperial
    
    Currently calling city number 4400:
    logumkloster, dk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=logumkloster,dk&units=imperial
    
    Currently calling city number 4401:
    havelock, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=havelock,nz&units=imperial
    
    Currently calling city number 4402:
    chaumont, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chaumont,fr&units=imperial
    
    Currently calling city number 4403:
    landskrona, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=landskrona,se&units=imperial
    
    Currently calling city number 4404:
    kyshtovka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kyshtovka,ru&units=imperial
    
    Currently calling city number 4405:
    eirunepe, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=eirunepe,br&units=imperial
    
    Currently calling city number 4406:
    tarancon, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tarancon,es&units=imperial
    
    Currently calling city number 4407:
    oliva, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=oliva,es&units=imperial
    
    Currently calling city number 4408:
    ladwa, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ladwa,in&units=imperial
    
    Currently calling city number 4409:
    aleppo, sy
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aleppo,sy&units=imperial
    
    Currently calling city number 4410:
    hobbs, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hobbs,us&units=imperial
    
    Currently calling city number 4411:
    karlshamn, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=karlshamn,se&units=imperial
    
    Currently calling city number 4412:
    golspie, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=golspie,gb&units=imperial
    
    Currently calling city number 4413:
    mombetsu, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mombetsu,jp&units=imperial
    
    Currently calling city number 4414:
    baiyin, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=baiyin,cn&units=imperial
    
    Currently calling city number 4415:
    ust-ordynskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ust-ordynskiy,ru&units=imperial
    
    Currently calling city number 4416:
    mlonggo, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mlonggo,id&units=imperial
    
    Currently calling city number 4417:
    nam som, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nam som,th&units=imperial
    
    Currently calling city number 4418:
    huangmei, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=huangmei,cn&units=imperial
    
    Currently calling city number 4419:
    glamoc, ba
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=glamoc,ba&units=imperial
    
    Currently calling city number 4420:
    espanola, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=espanola,ca&units=imperial
    
    Currently calling city number 4421:
    duekoue, ci
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=duekoue,ci&units=imperial
    
    Currently calling city number 4422:
    agdam, az
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=agdam,az&units=imperial
    
    Currently calling city number 4423:
    dabola, gn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dabola,gn&units=imperial
    
    Currently calling city number 4424:
    russkiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=russkiy,ru&units=imperial
    
    Currently calling city number 4425:
    shirokiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shirokiy,ru&units=imperial
    
    Currently calling city number 4426:
    iraucuba, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=iraucuba,br&units=imperial
    
    Currently calling city number 4427:
    rupert, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rupert,us&units=imperial
    
    Currently calling city number 4428:
    warren, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=warren,us&units=imperial
    
    Currently calling city number 4429:
    pedasi, pa
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pedasi,pa&units=imperial
    
    Currently calling city number 4430:
    chisec, gt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chisec,gt&units=imperial
    
    Currently calling city number 4431:
    muncar, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=muncar,id&units=imperial
    
    Currently calling city number 4432:
    matelica, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=matelica,it&units=imperial
    
    Currently calling city number 4433:
    zelenoborskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zelenoborskiy,ru&units=imperial
    
    Currently calling city number 4434:
    subate, lv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=subate,lv&units=imperial
    
    Currently calling city number 4435:
    riwaka, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=riwaka,nz&units=imperial
    
    Currently calling city number 4436:
    torzhok, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=torzhok,ru&units=imperial
    
    Currently calling city number 4437:
    wote, ke
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wote,ke&units=imperial
    
    Currently calling city number 4438:
    lugoba, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lugoba,tz&units=imperial
    
    Currently calling city number 4439:
    nuevo laredo, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nuevo laredo,mx&units=imperial
    
    Currently calling city number 4440:
    front royal, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=front royal,us&units=imperial
    
    Currently calling city number 4441:
    ormond beach, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ormond beach,us&units=imperial
    
    Currently calling city number 4442:
    anzio, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=anzio,it&units=imperial
    
    Currently calling city number 4443:
    epe, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=epe,ng&units=imperial
    
    Currently calling city number 4444:
    barroquinha, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=barroquinha,br&units=imperial
    
    Currently calling city number 4445:
    russkiy aktash, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=russkiy aktash,ru&units=imperial
    
    Currently calling city number 4446:
    gondanglegi, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gondanglegi,id&units=imperial
    
    Currently calling city number 4447:
    mount darwin, zw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mount darwin,zw&units=imperial
    
    Currently calling city number 4448:
    klaebu, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=klaebu,no&units=imperial
    
    Currently calling city number 4449:
    rokytne, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rokytne,ua&units=imperial
    
    Currently calling city number 4450:
    valkeala, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=valkeala,fi&units=imperial
    
    Currently calling city number 4451:
    dharchula, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dharchula,in&units=imperial
    
    Currently calling city number 4452:
    vredendal, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vredendal,za&units=imperial
    
    Currently calling city number 4453:
    kerikeri, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kerikeri,nz&units=imperial
    
    Currently calling city number 4454:
    tobol, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tobol,kz&units=imperial
    
    Currently calling city number 4455:
    port shepstone, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=port shepstone,za&units=imperial
    
    Currently calling city number 4456:
    upig, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=upig,ph&units=imperial
    
    Currently calling city number 4457:
    otavi, na
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=otavi,na&units=imperial
    
    Currently calling city number 4458:
    venado, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=venado,mx&units=imperial
    
    Currently calling city number 4459:
    avenal, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=avenal,us&units=imperial
    
    Currently calling city number 4460:
    saint-marc, ht
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saint-marc,ht&units=imperial
    
    Currently calling city number 4461:
    pesaro, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pesaro,it&units=imperial
    
    Currently calling city number 4462:
    coroico, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=coroico,bo&units=imperial
    
    Currently calling city number 4463:
    pointe michel, dm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pointe michel,dm&units=imperial
    
    Currently calling city number 4464:
    paros, gr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=paros,gr&units=imperial
    
    Currently calling city number 4465:
    gayeri, bf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gayeri,bf&units=imperial
    
    Currently calling city number 4466:
    lebedinyy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lebedinyy,ru&units=imperial
    
    Currently calling city number 4467:
    roblin, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=roblin,ca&units=imperial
    
    Currently calling city number 4468:
    cordoba, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cordoba,es&units=imperial
    
    Currently calling city number 4469:
    puteyets, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=puteyets,ru&units=imperial
    
    Currently calling city number 4470:
    joshimath, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=joshimath,in&units=imperial
    
    Currently calling city number 4471:
    serra, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=serra,br&units=imperial
    
    Currently calling city number 4472:
    skagen, dk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=skagen,dk&units=imperial
    
    Currently calling city number 4473:
    basudebpur, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=basudebpur,in&units=imperial
    
    Currently calling city number 4474:
    darovskoy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=darovskoy,ru&units=imperial
    
    Currently calling city number 4475:
    varangaon, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=varangaon,in&units=imperial
    
    Currently calling city number 4476:
    jiaozuo, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jiaozuo,cn&units=imperial
    
    Currently calling city number 4477:
    mumbwa, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mumbwa,zm&units=imperial
    
    Currently calling city number 4478:
    decatur, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=decatur,us&units=imperial
    
    Currently calling city number 4479:
    albury, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=albury,au&units=imperial
    
    Currently calling city number 4480:
    alice town, bs
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alice town,bs&units=imperial
    
    Currently calling city number 4481:
    santiago, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santiago,br&units=imperial
    
    Currently calling city number 4482:
    escanaba, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=escanaba,us&units=imperial
    
    Currently calling city number 4483:
    petropavl, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=petropavl,kz&units=imperial
    
    Currently calling city number 4484:
    ghanzi, bw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ghanzi,bw&units=imperial
    
    Currently calling city number 4485:
    port alberni, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=port alberni,ca&units=imperial
    
    Currently calling city number 4486:
    erdenet, mn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=erdenet,mn&units=imperial
    
    Currently calling city number 4487:
    megion, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=megion,ru&units=imperial
    
    Currently calling city number 4488:
    arcachon, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=arcachon,fr&units=imperial
    
    Currently calling city number 4489:
    jever, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jever,de&units=imperial
    
    Currently calling city number 4490:
    valleyview, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=valleyview,ca&units=imperial
    
    Currently calling city number 4491:
    canavieiras, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=canavieiras,br&units=imperial
    
    Currently calling city number 4492:
    yichang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yichang,cn&units=imperial
    
    Currently calling city number 4493:
    benjamin hill, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=benjamin hill,mx&units=imperial
    
    Currently calling city number 4494:
    diffa, ne
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=diffa,ne&units=imperial
    
    Currently calling city number 4495:
    nardaran, az
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nardaran,az&units=imperial
    
    Currently calling city number 4496:
    canico, pt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=canico,pt&units=imperial
    
    Currently calling city number 4497:
    ajaccio, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ajaccio,fr&units=imperial
    
    Currently calling city number 4498:
    enterprise, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=enterprise,us&units=imperial
    
    Currently calling city number 4499:
    floro, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=floro,no&units=imperial
    
    Currently calling city number 4500:
    brunsbuttel, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=brunsbuttel,de&units=imperial
    
    Currently calling city number 4501:
    chateaubelair, vc
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chateaubelair,vc&units=imperial
    
    Currently calling city number 4502:
    divnogorsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=divnogorsk,ru&units=imperial
    
    Currently calling city number 4503:
    rio branco, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rio branco,br&units=imperial
    
    Currently calling city number 4504:
    khromtau, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=khromtau,kz&units=imperial
    
    Currently calling city number 4505:
    raahe, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=raahe,fi&units=imperial
    
    Currently calling city number 4506:
    imphal, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=imphal,in&units=imperial
    
    Currently calling city number 4507:
    solenzo, bf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=solenzo,bf&units=imperial
    
    Currently calling city number 4508:
    kungalv, se
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kungalv,se&units=imperial
    
    Currently calling city number 4509:
    cuenca, ec
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cuenca,ec&units=imperial
    
    Currently calling city number 4510:
    formosa, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=formosa,br&units=imperial
    
    Currently calling city number 4511:
    kamarion, gr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kamarion,gr&units=imperial
    
    Currently calling city number 4512:
    sigulda, lv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sigulda,lv&units=imperial
    
    Currently calling city number 4513:
    los zacatones, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=los zacatones,mx&units=imperial
    
    Currently calling city number 4514:
    mweka, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mweka,cd&units=imperial
    
    Currently calling city number 4515:
    capitan bado, py
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=capitan bado,py&units=imperial
    
    Currently calling city number 4516:
    joson, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=joson,ph&units=imperial
    
    Currently calling city number 4517:
    khanu woralaksaburi, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=khanu woralaksaburi,th&units=imperial
    
    Currently calling city number 4518:
    kem, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kem,ru&units=imperial
    
    Currently calling city number 4519:
    nata, bw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nata,bw&units=imperial
    
    Currently calling city number 4520:
    zvenigovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zvenigovo,ru&units=imperial
    
    Currently calling city number 4521:
    menomonie, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=menomonie,us&units=imperial
    
    Currently calling city number 4522:
    mudon, mm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mudon,mm&units=imperial
    
    Currently calling city number 4523:
    kasempa, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kasempa,zm&units=imperial
    
    Currently calling city number 4524:
    pandamatenga, bw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pandamatenga,bw&units=imperial
    
    Currently calling city number 4525:
    morropon, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=morropon,pe&units=imperial
    
    Currently calling city number 4526:
    jining, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jining,cn&units=imperial
    
    Currently calling city number 4527:
    muroran, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=muroran,jp&units=imperial
    
    Currently calling city number 4528:
    wahiawa, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wahiawa,us&units=imperial
    
    Currently calling city number 4529:
    smolenka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=smolenka,ru&units=imperial
    
    Currently calling city number 4530:
    la florida, hn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=la florida,hn&units=imperial
    
    Currently calling city number 4531:
    ferme-neuve, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ferme-neuve,ca&units=imperial
    
    Currently calling city number 4532:
    eyemouth, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=eyemouth,gb&units=imperial
    
    Currently calling city number 4533:
    pawai, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pawai,in&units=imperial
    
    Currently calling city number 4534:
    san gabriel, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san gabriel,mx&units=imperial
    
    Currently calling city number 4535:
    contai, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=contai,in&units=imperial
    
    Currently calling city number 4536:
    fez, ma
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fez,ma&units=imperial
    
    Currently calling city number 4537:
    puerto carreno, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=puerto carreno,co&units=imperial
    
    Currently calling city number 4538:
    bayburt, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bayburt,tr&units=imperial
    
    Currently calling city number 4539:
    mangit, uz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mangit,uz&units=imperial
    
    Currently calling city number 4540:
    sakata, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sakata,jp&units=imperial
    
    Currently calling city number 4541:
    khatima, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=khatima,in&units=imperial
    
    Currently calling city number 4542:
    el porvenir, hn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=el porvenir,hn&units=imperial
    
    Currently calling city number 4543:
    la gomera, gt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=la gomera,gt&units=imperial
    
    Currently calling city number 4544:
    cheremukhovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cheremukhovo,ru&units=imperial
    
    Currently calling city number 4545:
    pedernales, ec
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pedernales,ec&units=imperial
    
    Currently calling city number 4546:
    easton, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=easton,us&units=imperial
    
    Currently calling city number 4547:
    ontario, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ontario,us&units=imperial
    
    Currently calling city number 4548:
    letlhakane, bw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=letlhakane,bw&units=imperial
    
    Currently calling city number 4549:
    sarahan, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sarahan,in&units=imperial
    
    Currently calling city number 4550:
    sao borja, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sao borja,br&units=imperial
    
    Currently calling city number 4551:
    matata, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=matata,nz&units=imperial
    
    Currently calling city number 4552:
    amalner, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=amalner,in&units=imperial
    
    Currently calling city number 4553:
    binga, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=binga,cd&units=imperial
    
    Currently calling city number 4554:
    grand-lahou, ci
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=grand-lahou,ci&units=imperial
    
    Currently calling city number 4555:
    endicott, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=endicott,us&units=imperial
    
    Currently calling city number 4556:
    berga, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=berga,es&units=imperial
    
    Currently calling city number 4557:
    mayya, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mayya,ru&units=imperial
    
    Currently calling city number 4558:
    pingshan, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pingshan,cn&units=imperial
    
    Currently calling city number 4559:
    sagna, ro
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sagna,ro&units=imperial
    
    Currently calling city number 4560:
    etchojoa, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=etchojoa,mx&units=imperial
    
    Currently calling city number 4561:
    san miguel, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san miguel,bo&units=imperial
    
    Currently calling city number 4562:
    cradock, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cradock,za&units=imperial
    
    Currently calling city number 4563:
    southbridge, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=southbridge,us&units=imperial
    
    Currently calling city number 4564:
    veinticinco de mayo, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=veinticinco de mayo,ar&units=imperial
    
    Currently calling city number 4565:
    la peca, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=la peca,pe&units=imperial
    
    Currently calling city number 4566:
    horsham, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=horsham,au&units=imperial
    
    Currently calling city number 4567:
    umarizal, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=umarizal,br&units=imperial
    
    Currently calling city number 4568:
    chlum u trebone, cz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chlum u trebone,cz&units=imperial
    
    Currently calling city number 4569:
    chenghai, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chenghai,cn&units=imperial
    
    Currently calling city number 4570:
    somotillo, ni
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=somotillo,ni&units=imperial
    
    Currently calling city number 4571:
    pueblo nuevo, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pueblo nuevo,mx&units=imperial
    
    Currently calling city number 4572:
    akhtanizovskaya, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=akhtanizovskaya,ru&units=imperial
    
    Currently calling city number 4573:
    shahr-e babak, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shahr-e babak,ir&units=imperial
    
    Currently calling city number 4574:
    esmeralda, cu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=esmeralda,cu&units=imperial
    
    Currently calling city number 4575:
    yongan, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yongan,cn&units=imperial
    
    Currently calling city number 4576:
    paragominas, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=paragominas,br&units=imperial
    
    Currently calling city number 4577:
    paso de los toros, uy
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=paso de los toros,uy&units=imperial
    
    Currently calling city number 4578:
    afikpo, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=afikpo,ng&units=imperial
    
    Currently calling city number 4579:
    marathon, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=marathon,ca&units=imperial
    
    Currently calling city number 4580:
    butte, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=butte,us&units=imperial
    
    Currently calling city number 4581:
    ramotswa, bw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ramotswa,bw&units=imperial
    
    Currently calling city number 4582:
    fort pierce, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fort pierce,us&units=imperial
    
    Currently calling city number 4583:
    butajira, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=butajira,et&units=imperial
    
    Currently calling city number 4584:
    verkhoturye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=verkhoturye,ru&units=imperial
    
    Currently calling city number 4585:
    shebunino, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shebunino,ru&units=imperial
    
    Currently calling city number 4586:
    victoria point, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=victoria point,au&units=imperial
    
    Currently calling city number 4587:
    lake city, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lake city,us&units=imperial
    
    Currently calling city number 4588:
    nauta, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nauta,pe&units=imperial
    
    Currently calling city number 4589:
    provost, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=provost,ca&units=imperial
    
    Currently calling city number 4590:
    trinidad, cu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=trinidad,cu&units=imperial
    
    Currently calling city number 4591:
    ust-kan, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ust-kan,ru&units=imperial
    
    Currently calling city number 4592:
    peresichna, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=peresichna,ua&units=imperial
    
    Currently calling city number 4593:
    jibert, ro
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jibert,ro&units=imperial
    
    Currently calling city number 4594:
    san lawrenz, mt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san lawrenz,mt&units=imperial
    
    Currently calling city number 4595:
    sebastian, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sebastian,us&units=imperial
    
    Currently calling city number 4596:
    cobija, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cobija,bo&units=imperial
    
    Currently calling city number 4597:
    copaceni, ro
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=copaceni,ro&units=imperial
    
    Currently calling city number 4598:
    siirt, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=siirt,tr&units=imperial
    
    Currently calling city number 4599:
    buritis, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=buritis,br&units=imperial
    
    Currently calling city number 4600:
    nongpoh, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nongpoh,in&units=imperial
    
    Currently calling city number 4601:
    tlazazalca, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tlazazalca,mx&units=imperial
    
    Currently calling city number 4602:
    booue, ga
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=booue,ga&units=imperial
    
    Currently calling city number 4603:
    guantanamo, cu
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=guantanamo,cu&units=imperial
    
    Currently calling city number 4604:
    arak, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=arak,ir&units=imperial
    
    Currently calling city number 4605:
    barcelona, ve
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=barcelona,ve&units=imperial
    
    Currently calling city number 4606:
    amudat, ug
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=amudat,ug&units=imperial
    
    Currently calling city number 4607:
    sonari, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sonari,in&units=imperial
    
    Currently calling city number 4608:
    hengshui, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hengshui,cn&units=imperial
    
    Currently calling city number 4609:
    kachiry, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kachiry,kz&units=imperial
    
    Currently calling city number 4610:
    pelym, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pelym,ru&units=imperial
    
    Currently calling city number 4611:
    conchas, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=conchas,br&units=imperial
    
    Currently calling city number 4612:
    shitkino, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shitkino,ru&units=imperial
    
    Currently calling city number 4613:
    cap-haitien, ht
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cap-haitien,ht&units=imperial
    
    Currently calling city number 4614:
    bennington, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bennington,us&units=imperial
    
    Currently calling city number 4615:
    malinyi, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=malinyi,tz&units=imperial
    
    Currently calling city number 4616:
    ambikapur, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ambikapur,in&units=imperial
    
    Currently calling city number 4617:
    bossangoa, cf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bossangoa,cf&units=imperial
    
    Currently calling city number 4618:
    ellwangen, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ellwangen,de&units=imperial
    
    Currently calling city number 4619:
    kolda, sn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kolda,sn&units=imperial
    
    Currently calling city number 4620:
    cehegin, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cehegin,es&units=imperial
    
    Currently calling city number 4621:
    fort wellington, gy
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fort wellington,gy&units=imperial
    
    Currently calling city number 4622:
    mudgee, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mudgee,au&units=imperial
    
    Currently calling city number 4623:
    winneba, gh
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=winneba,gh&units=imperial
    
    Currently calling city number 4624:
    durango, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=durango,us&units=imperial
    
    Currently calling city number 4625:
    guamo, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=guamo,co&units=imperial
    
    Currently calling city number 4626:
    baoying, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=baoying,cn&units=imperial
    
    Currently calling city number 4627:
    barillas, gt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=barillas,gt&units=imperial
    
    Currently calling city number 4628:
    fairview, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fairview,ca&units=imperial
    
    Currently calling city number 4629:
    konstancin-jeziorna, pl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=konstancin-jeziorna,pl&units=imperial
    
    Currently calling city number 4630:
    poway, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=poway,us&units=imperial
    
    Currently calling city number 4631:
    tacuarembo, uy
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tacuarembo,uy&units=imperial
    
    Currently calling city number 4632:
    novyy nekouz, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=novyy nekouz,ru&units=imperial
    
    Currently calling city number 4633:
    alampur, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alampur,in&units=imperial
    
    Currently calling city number 4634:
    puerto penasco, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=puerto penasco,mx&units=imperial
    
    Currently calling city number 4635:
    catalao, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=catalao,br&units=imperial
    
    Currently calling city number 4636:
    barahan, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=barahan,ph&units=imperial
    
    Currently calling city number 4637:
    suratgarh, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=suratgarh,in&units=imperial
    
    Currently calling city number 4638:
    holice, cz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=holice,cz&units=imperial
    
    Currently calling city number 4639:
    khuzdar, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=khuzdar,pk&units=imperial
    
    Currently calling city number 4640:
    brenham, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=brenham,us&units=imperial
    
    Currently calling city number 4641:
    nizhnyaya salda, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nizhnyaya salda,ru&units=imperial
    
    Currently calling city number 4642:
    humboldt, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=humboldt,us&units=imperial
    
    Currently calling city number 4643:
    sainte-rose, re
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sainte-rose,re&units=imperial
    
    Currently calling city number 4644:
    san felipe, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san felipe,ph&units=imperial
    
    Currently calling city number 4645:
    ulagan, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ulagan,ru&units=imperial
    
    Currently calling city number 4646:
    kholm, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kholm,ru&units=imperial
    
    Currently calling city number 4647:
    texarkana, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=texarkana,us&units=imperial
    
    Currently calling city number 4648:
    koeru, ee
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=koeru,ee&units=imperial
    
    Currently calling city number 4649:
    orocue, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=orocue,co&units=imperial
    
    Currently calling city number 4650:
    sapa, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sapa,ph&units=imperial
    
    Currently calling city number 4651:
    kayankulam, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kayankulam,in&units=imperial
    
    Currently calling city number 4652:
    rajampet, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rajampet,in&units=imperial
    
    Currently calling city number 4653:
    laela, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=laela,tz&units=imperial
    
    Currently calling city number 4654:
    syedove, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=syedove,ua&units=imperial
    
    Currently calling city number 4655:
    lebanon, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lebanon,us&units=imperial
    
    Currently calling city number 4656:
    barkhan, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=barkhan,pk&units=imperial
    
    Currently calling city number 4657:
    namie, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=namie,jp&units=imperial
    
    Currently calling city number 4658:
    san juan de uraba, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san juan de uraba,co&units=imperial
    
    Currently calling city number 4659:
    solone, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=solone,ua&units=imperial
    
    Currently calling city number 4660:
    xiaoshi, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=xiaoshi,cn&units=imperial
    
    Currently calling city number 4661:
    shahreza, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shahreza,ir&units=imperial
    
    Currently calling city number 4662:
    sjovegan, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sjovegan,no&units=imperial
    
    Currently calling city number 4663:
    bellary, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bellary,in&units=imperial
    
    Currently calling city number 4664:
    richmond, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=richmond,za&units=imperial
    
    Currently calling city number 4665:
    shushtar, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shushtar,ir&units=imperial
    
    Currently calling city number 4666:
    lithgow, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lithgow,au&units=imperial
    
    Currently calling city number 4667:
    herrin, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=herrin,us&units=imperial
    
    Currently calling city number 4668:
    lauria, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lauria,it&units=imperial
    
    Currently calling city number 4669:
    adeje, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=adeje,es&units=imperial
    
    Currently calling city number 4670:
    salumbar, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=salumbar,in&units=imperial
    
    Currently calling city number 4671:
    icod de los vinos, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=icod de los vinos,es&units=imperial
    
    Currently calling city number 4672:
    plaster rock, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=plaster rock,ca&units=imperial
    
    Currently calling city number 4673:
    singaparna, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=singaparna,id&units=imperial
    
    Currently calling city number 4674:
    pyaozerskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pyaozerskiy,ru&units=imperial
    
    Currently calling city number 4675:
    nhamunda, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nhamunda,br&units=imperial
    
    Currently calling city number 4676:
    jucurutu, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=jucurutu,br&units=imperial
    
    Currently calling city number 4677:
    fort atkinson, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fort atkinson,us&units=imperial
    
    Currently calling city number 4678:
    xicotencatl, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=xicotencatl,mx&units=imperial
    
    Currently calling city number 4679:
    wloszczowa, pl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wloszczowa,pl&units=imperial
    
    Currently calling city number 4680:
    alexandria, eg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alexandria,eg&units=imperial
    
    Currently calling city number 4681:
    khandbari, np
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=khandbari,np&units=imperial
    
    Currently calling city number 4682:
    la libertad, sv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=la libertad,sv&units=imperial
    
    Currently calling city number 4683:
    makkaveyevo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=makkaveyevo,ru&units=imperial
    
    Currently calling city number 4684:
    afsin, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=afsin,tr&units=imperial
    
    Currently calling city number 4685:
    karaj, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=karaj,ir&units=imperial
    
    Currently calling city number 4686:
    bada, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bada,ru&units=imperial
    
    Currently calling city number 4687:
    turochak, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=turochak,ru&units=imperial
    
    Currently calling city number 4688:
    delijan, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=delijan,ir&units=imperial
    
    Currently calling city number 4689:
    frunze, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=frunze,ua&units=imperial
    
    Currently calling city number 4690:
    bushehr, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bushehr,ir&units=imperial
    
    Currently calling city number 4691:
    sennoy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sennoy,ru&units=imperial
    
    Currently calling city number 4692:
    lind, dk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lind,dk&units=imperial
    
    Currently calling city number 4693:
    longyan, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=longyan,cn&units=imperial
    
    Currently calling city number 4694:
    fryazino, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fryazino,ru&units=imperial
    
    Currently calling city number 4695:
    zirandaro, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zirandaro,mx&units=imperial
    
    Currently calling city number 4696:
    balaka, mw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=balaka,mw&units=imperial
    
    Currently calling city number 4697:
    santa maria, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santa maria,us&units=imperial
    
    Currently calling city number 4698:
    linhai, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=linhai,cn&units=imperial
    
    Currently calling city number 4699:
    piet retief, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=piet retief,za&units=imperial
    
    Currently calling city number 4700:
    santa lucia, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santa lucia,es&units=imperial
    
    Currently calling city number 4701:
    aracati, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aracati,br&units=imperial
    
    Currently calling city number 4702:
    lohja, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lohja,fi&units=imperial
    
    Currently calling city number 4703:
    bogande, bf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bogande,bf&units=imperial
    
    Currently calling city number 4704:
    vanderhoof, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vanderhoof,ca&units=imperial
    
    Currently calling city number 4705:
    roslavl, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=roslavl,ru&units=imperial
    
    Currently calling city number 4706:
    ilebo, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ilebo,cd&units=imperial
    
    Currently calling city number 4707:
    mulchen, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mulchen,cl&units=imperial
    
    Currently calling city number 4708:
    yondo, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yondo,co&units=imperial
    
    Currently calling city number 4709:
    galbshtadt, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=galbshtadt,ru&units=imperial
    
    Currently calling city number 4710:
    ploemeur, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ploemeur,fr&units=imperial
    
    Currently calling city number 4711:
    puchezh, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=puchezh,ru&units=imperial
    
    Currently calling city number 4712:
    swinford, ie
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=swinford,ie&units=imperial
    
    Currently calling city number 4713:
    barra do bugres, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=barra do bugres,br&units=imperial
    
    Currently calling city number 4714:
    moissac, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=moissac,fr&units=imperial
    
    Currently calling city number 4715:
    batie, bf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=batie,bf&units=imperial
    
    Currently calling city number 4716:
    bokhan, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bokhan,ru&units=imperial
    
    Currently calling city number 4717:
    dunoon, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dunoon,gb&units=imperial
    
    Currently calling city number 4718:
    hangu, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hangu,cn&units=imperial
    
    Currently calling city number 4719:
    hermosillo, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hermosillo,mx&units=imperial
    
    Currently calling city number 4720:
    semporna, my
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=semporna,my&units=imperial
    
    Currently calling city number 4721:
    egersund, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=egersund,no&units=imperial
    
    Currently calling city number 4722:
    san isidro, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san isidro,ph&units=imperial
    
    Currently calling city number 4723:
    erie, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=erie,us&units=imperial
    
    Currently calling city number 4724:
    kajiado, ke
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kajiado,ke&units=imperial
    
    Currently calling city number 4725:
    quincy, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=quincy,us&units=imperial
    
    Currently calling city number 4726:
    canutama, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=canutama,br&units=imperial
    
    Currently calling city number 4727:
    amarpur, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=amarpur,in&units=imperial
    
    Currently calling city number 4728:
    shubarkuduk, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shubarkuduk,kz&units=imperial
    
    Currently calling city number 4729:
    honolulu, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=honolulu,us&units=imperial
    
    Currently calling city number 4730:
    escarcega, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=escarcega,mx&units=imperial
    
    Currently calling city number 4731:
    uarini, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=uarini,br&units=imperial
    
    Currently calling city number 4732:
    chandur, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chandur,in&units=imperial
    
    Currently calling city number 4733:
    brynmawr, gb
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=brynmawr,gb&units=imperial
    
    Currently calling city number 4734:
    ust-barguzin, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ust-barguzin,ru&units=imperial
    
    Currently calling city number 4735:
    arkansas city, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=arkansas city,us&units=imperial
    
    Currently calling city number 4736:
    iacu, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=iacu,br&units=imperial
    
    Currently calling city number 4737:
    spornoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=spornoye,ru&units=imperial
    
    Currently calling city number 4738:
    kwekwe, zw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kwekwe,zw&units=imperial
    
    Currently calling city number 4739:
    frutal, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=frutal,br&units=imperial
    
    Currently calling city number 4740:
    aksarayskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aksarayskiy,ru&units=imperial
    
    Currently calling city number 4741:
    codo, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=codo,br&units=imperial
    
    Currently calling city number 4742:
    velingara, sn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=velingara,sn&units=imperial
    
    Currently calling city number 4743:
    medicine hat, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=medicine hat,ca&units=imperial
    
    Currently calling city number 4744:
    takaka, nz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=takaka,nz&units=imperial
    
    Currently calling city number 4745:
    nenjiang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nenjiang,cn&units=imperial
    
    Currently calling city number 4746:
    gannan, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gannan,cn&units=imperial
    
    Currently calling city number 4747:
    port arthur, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=port arthur,us&units=imperial
    
    Currently calling city number 4748:
    zelenoborsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zelenoborsk,ru&units=imperial
    
    Currently calling city number 4749:
    ardalstangen, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ardalstangen,no&units=imperial
    
    Currently calling city number 4750:
    vichuga, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vichuga,ru&units=imperial
    
    Currently calling city number 4751:
    nyimba, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nyimba,zm&units=imperial
    
    Currently calling city number 4752:
    cambrils, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cambrils,es&units=imperial
    
    Currently calling city number 4753:
    el rosario, sv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=el rosario,sv&units=imperial
    
    Currently calling city number 4754:
    bad wurzach, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bad wurzach,de&units=imperial
    
    Currently calling city number 4755:
    rudnogorsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rudnogorsk,ru&units=imperial
    
    Currently calling city number 4756:
    sherpur, bd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sherpur,bd&units=imperial
    
    Currently calling city number 4757:
    silifke, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=silifke,tr&units=imperial
    
    Currently calling city number 4758:
    bogorodskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bogorodskoye,ru&units=imperial
    
    Currently calling city number 4759:
    sitrah, bh
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sitrah,bh&units=imperial
    
    Currently calling city number 4760:
    mamou, gn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mamou,gn&units=imperial
    
    Currently calling city number 4761:
    spas, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=spas,ua&units=imperial
    
    Currently calling city number 4762:
    landsberg, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=landsberg,de&units=imperial
    
    Currently calling city number 4763:
    mandalay, mm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mandalay,mm&units=imperial
    
    Currently calling city number 4764:
    maku, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=maku,ir&units=imperial
    
    Currently calling city number 4765:
    talwara, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=talwara,in&units=imperial
    
    Currently calling city number 4766:
    cenade, ro
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cenade,ro&units=imperial
    
    Currently calling city number 4767:
    nieves, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nieves,mx&units=imperial
    
    Currently calling city number 4768:
    poirino, it
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=poirino,it&units=imperial
    
    Currently calling city number 4769:
    yoichi, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yoichi,jp&units=imperial
    
    Currently calling city number 4770:
    kilis, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kilis,tr&units=imperial
    
    Currently calling city number 4771:
    delvine, al
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=delvine,al&units=imperial
    
    Currently calling city number 4772:
    powell, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=powell,us&units=imperial
    
    Currently calling city number 4773:
    lebrija, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lebrija,es&units=imperial
    
    Currently calling city number 4774:
    kargil, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kargil,in&units=imperial
    
    Currently calling city number 4775:
    poddorye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=poddorye,ru&units=imperial
    
    Currently calling city number 4776:
    oskemen, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=oskemen,kz&units=imperial
    
    Currently calling city number 4777:
    imuris, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=imuris,mx&units=imperial
    
    Currently calling city number 4778:
    santa catarina de tepehuanes, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santa catarina de tepehuanes,mx&units=imperial
    
    Currently calling city number 4779:
    vohibinany, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=vohibinany,mg&units=imperial
    
    Currently calling city number 4780:
    lam luk ka, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lam luk ka,th&units=imperial
    
    Currently calling city number 4781:
    umm bab, qa
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=umm bab,qa&units=imperial
    
    Currently calling city number 4782:
    ulverstone, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ulverstone,au&units=imperial
    
    Currently calling city number 4783:
    niagara falls, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=niagara falls,us&units=imperial
    
    Currently calling city number 4784:
    eston, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=eston,ca&units=imperial
    
    Currently calling city number 4785:
    onokhoy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=onokhoy,ru&units=imperial
    
    Currently calling city number 4786:
    mago, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mago,ru&units=imperial
    
    Currently calling city number 4787:
    linden, gy
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=linden,gy&units=imperial
    
    Currently calling city number 4788:
    alta gracia, ar
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alta gracia,ar&units=imperial
    
    Currently calling city number 4789:
    uporovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=uporovo,ru&units=imperial
    
    Currently calling city number 4790:
    nedjo, et
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nedjo,et&units=imperial
    
    Currently calling city number 4791:
    orange walk, bz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=orange walk,bz&units=imperial
    
    Currently calling city number 4792:
    yakima, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yakima,us&units=imperial
    
    Currently calling city number 4793:
    ensenada, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ensenada,mx&units=imperial
    
    Currently calling city number 4794:
    kaspiysk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kaspiysk,ru&units=imperial
    
    Currently calling city number 4795:
    portsmouth, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=portsmouth,us&units=imperial
    
    Currently calling city number 4796:
    malatya, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=malatya,tr&units=imperial
    
    Currently calling city number 4797:
    hillsboro, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hillsboro,us&units=imperial
    
    Currently calling city number 4798:
    kolyvan, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kolyvan,ru&units=imperial
    
    Currently calling city number 4799:
    yendi, gh
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yendi,gh&units=imperial
    
    Currently calling city number 4800:
    caraquet, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=caraquet,ca&units=imperial
    
    Currently calling city number 4801:
    villa del rosario, ve
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=villa del rosario,ve&units=imperial
    
    Currently calling city number 4802:
    amarante do maranhao, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=amarante do maranhao,br&units=imperial
    
    Currently calling city number 4803:
    toucheng, tw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=toucheng,tw&units=imperial
    
    Currently calling city number 4804:
    astaneh-ye ashrafiyeh, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=astaneh-ye ashrafiyeh,ir&units=imperial
    
    Currently calling city number 4805:
    nueva helvecia, uy
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nueva helvecia,uy&units=imperial
    
    Currently calling city number 4806:
    rapar, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rapar,in&units=imperial
    
    Currently calling city number 4807:
    setermoen, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=setermoen,no&units=imperial
    
    Currently calling city number 4808:
    rock springs, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rock springs,us&units=imperial
    
    Currently calling city number 4809:
    mabay, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mabay,ph&units=imperial
    
    Currently calling city number 4810:
    bindura, zw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bindura,zw&units=imperial
    
    Currently calling city number 4811:
    tenenkou, ml
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tenenkou,ml&units=imperial
    
    Currently calling city number 4812:
    shaowu, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shaowu,cn&units=imperial
    
    Currently calling city number 4813:
    rong kwang, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rong kwang,th&units=imperial
    
    Currently calling city number 4814:
    scottsbluff, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=scottsbluff,us&units=imperial
    
    Currently calling city number 4815:
    dapaong, tg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dapaong,tg&units=imperial
    
    Currently calling city number 4816:
    machinga, mw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=machinga,mw&units=imperial
    
    Currently calling city number 4817:
    saryozek, kz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saryozek,kz&units=imperial
    
    Currently calling city number 4818:
    bloemfontein, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bloemfontein,za&units=imperial
    
    Currently calling city number 4819:
    kisarawe, tz
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kisarawe,tz&units=imperial
    
    Currently calling city number 4820:
    kalundborg, dk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kalundborg,dk&units=imperial
    
    Currently calling city number 4821:
    lake havasu city, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lake havasu city,us&units=imperial
    
    Currently calling city number 4822:
    palmeirais, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=palmeirais,br&units=imperial
    
    Currently calling city number 4823:
    ijsselstein, nl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ijsselstein,nl&units=imperial
    
    Currently calling city number 4824:
    mazyr, by
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mazyr,by&units=imperial
    
    Currently calling city number 4825:
    guaraniacu, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=guaraniacu,br&units=imperial
    
    Currently calling city number 4826:
    makaryev, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=makaryev,ru&units=imperial
    
    Currently calling city number 4827:
    iznoski, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=iznoski,ru&units=imperial
    
    Currently calling city number 4828:
    ilam, np
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ilam,np&units=imperial
    
    Currently calling city number 4829:
    kuznechnoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kuznechnoye,ru&units=imperial
    
    Currently calling city number 4830:
    hoa binh, vn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hoa binh,vn&units=imperial
    
    Currently calling city number 4831:
    uvira, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=uvira,cd&units=imperial
    
    Currently calling city number 4832:
    bossier city, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bossier city,us&units=imperial
    
    Currently calling city number 4833:
    hamada, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hamada,jp&units=imperial
    
    Currently calling city number 4834:
    punta umbria, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=punta umbria,es&units=imperial
    
    Currently calling city number 4835:
    bhinga, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bhinga,in&units=imperial
    
    Currently calling city number 4836:
    lengshuijiang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lengshuijiang,cn&units=imperial
    
    Currently calling city number 4837:
    lima, py
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lima,py&units=imperial
    
    Currently calling city number 4838:
    tukan, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tukan,ru&units=imperial
    
    Currently calling city number 4839:
    la reforma, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=la reforma,mx&units=imperial
    
    Currently calling city number 4840:
    cayambe, ec
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=cayambe,ec&units=imperial
    
    Currently calling city number 4841:
    luba, gq
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=luba,gq&units=imperial
    
    Currently calling city number 4842:
    quchan, ir
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=quchan,ir&units=imperial
    
    Currently calling city number 4843:
    meaux, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=meaux,fr&units=imperial
    
    Currently calling city number 4844:
    solhan, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=solhan,tr&units=imperial
    
    Currently calling city number 4845:
    harmanli, bg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=harmanli,bg&units=imperial
    
    Currently calling city number 4846:
    savinka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=savinka,ru&units=imperial
    
    Currently calling city number 4847:
    shobara, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=shobara,jp&units=imperial
    
    Currently calling city number 4848:
    saeby, dk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saeby,dk&units=imperial
    
    Currently calling city number 4849:
    galyugayevskaya, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=galyugayevskaya,ru&units=imperial
    
    Currently calling city number 4850:
    la colorada, pa
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=la colorada,pa&units=imperial
    
    Currently calling city number 4851:
    terrak, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=terrak,no&units=imperial
    
    Currently calling city number 4852:
    santa cruz de tenerife, es
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=santa cruz de tenerife,es&units=imperial
    
    Currently calling city number 4853:
    achim, de
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=achim,de&units=imperial
    
    Currently calling city number 4854:
    kalga, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kalga,ru&units=imperial
    
    Currently calling city number 4855:
    zadar, hr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zadar,hr&units=imperial
    
    Currently calling city number 4856:
    okhtyrka, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=okhtyrka,ua&units=imperial
    
    Currently calling city number 4857:
    coffs harbour, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=coffs harbour,au&units=imperial
    
    Currently calling city number 4858:
    tramandai, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tramandai,br&units=imperial
    
    Currently calling city number 4859:
    bela, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bela,pk&units=imperial
    
    Currently calling city number 4860:
    kinango, ke
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kinango,ke&units=imperial
    
    Currently calling city number 4861:
    arrifes, pt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=arrifes,pt&units=imperial
    
    Currently calling city number 4862:
    nagai, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nagai,jp&units=imperial
    
    Currently calling city number 4863:
    ofunato, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ofunato,jp&units=imperial
    
    Currently calling city number 4864:
    gunnedah, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gunnedah,au&units=imperial
    
    Currently calling city number 4865:
    gresham, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gresham,us&units=imperial
    
    Currently calling city number 4866:
    swan river, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=swan river,ca&units=imperial
    
    Currently calling city number 4867:
    angol, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=angol,cl&units=imperial
    
    Currently calling city number 4868:
    moura, pt
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=moura,pt&units=imperial
    
    Currently calling city number 4869:
    anuradhapura, lk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=anuradhapura,lk&units=imperial
    
    Currently calling city number 4870:
    valparaiso, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=valparaiso,us&units=imperial
    
    Currently calling city number 4871:
    camalu, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=camalu,mx&units=imperial
    
    Currently calling city number 4872:
    nikolsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nikolsk,ru&units=imperial
    
    Currently calling city number 4873:
    culebra, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=culebra,us&units=imperial
    
    Currently calling city number 4874:
    okmulgee, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=okmulgee,us&units=imperial
    
    Currently calling city number 4875:
    harrisonburg, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=harrisonburg,us&units=imperial
    
    Currently calling city number 4876:
    ovre ardal, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ovre ardal,no&units=imperial
    
    Currently calling city number 4877:
    trojes, hn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=trojes,hn&units=imperial
    
    Currently calling city number 4878:
    koulikoro, ml
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=koulikoro,ml&units=imperial
    
    Currently calling city number 4879:
    kouroussa, gn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kouroussa,gn&units=imperial
    
    Currently calling city number 4880:
    maues, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=maues,br&units=imperial
    
    Currently calling city number 4881:
    male, mv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=male,mv&units=imperial
    
    Currently calling city number 4882:
    dauriya, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dauriya,ru&units=imperial
    
    Currently calling city number 4883:
    colomi, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=colomi,bo&units=imperial
    
    Currently calling city number 4884:
    tarabuco, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tarabuco,bo&units=imperial
    
    Currently calling city number 4885:
    rosetown, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rosetown,ca&units=imperial
    
    Currently calling city number 4886:
    lewistown, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lewistown,us&units=imperial
    
    Currently calling city number 4887:
    benalla, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=benalla,au&units=imperial
    
    Currently calling city number 4888:
    yershov, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yershov,ru&units=imperial
    
    Currently calling city number 4889:
    slonim, by
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=slonim,by&units=imperial
    
    Currently calling city number 4890:
    doha, qa
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=doha,qa&units=imperial
    
    Currently calling city number 4891:
    moundsville, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=moundsville,us&units=imperial
    
    Currently calling city number 4892:
    sosva, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sosva,ru&units=imperial
    
    Currently calling city number 4893:
    agra, gr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=agra,gr&units=imperial
    
    Currently calling city number 4894:
    varkaus, fi
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=varkaus,fi&units=imperial
    
    Currently calling city number 4895:
    ocho rios, jm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ocho rios,jm&units=imperial
    
    Currently calling city number 4896:
    tsybuliv, ua
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tsybuliv,ua&units=imperial
    
    Currently calling city number 4897:
    nioki, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nioki,cd&units=imperial
    
    Currently calling city number 4898:
    ornes, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ornes,no&units=imperial
    
    Currently calling city number 4899:
    bucyrus, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bucyrus,us&units=imperial
    
    Currently calling city number 4900:
    hope, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hope,us&units=imperial
    
    Currently calling city number 4901:
    dhuburi, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dhuburi,in&units=imperial
    
    Currently calling city number 4902:
    zilupe, lv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zilupe,lv&units=imperial
    
    Currently calling city number 4903:
    san rafael, bo
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=san rafael,bo&units=imperial
    
    Currently calling city number 4904:
    alabaster, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=alabaster,us&units=imperial
    
    Currently calling city number 4905:
    manitowoc, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=manitowoc,us&units=imperial
    
    Currently calling city number 4906:
    mchinji, mw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mchinji,mw&units=imperial
    
    Currently calling city number 4907:
    anuchino, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=anuchino,ru&units=imperial
    
    Currently calling city number 4908:
    saint-louis, sn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saint-louis,sn&units=imperial
    
    Currently calling city number 4909:
    ushibuka, jp
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ushibuka,jp&units=imperial
    
    Currently calling city number 4910:
    sosenskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sosenskiy,ru&units=imperial
    
    Currently calling city number 4911:
    glens falls, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=glens falls,us&units=imperial
    
    Currently calling city number 4912:
    arawa, pg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=arawa,pg&units=imperial
    
    Currently calling city number 4913:
    sarreguemines, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sarreguemines,fr&units=imperial
    
    Currently calling city number 4914:
    ostrogozhsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ostrogozhsk,ru&units=imperial
    
    Currently calling city number 4915:
    superior, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=superior,us&units=imperial
    
    Currently calling city number 4916:
    amnat charoen, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=amnat charoen,th&units=imperial
    
    Currently calling city number 4917:
    hosanagara, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hosanagara,in&units=imperial
    
    Currently calling city number 4918:
    rengo, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=rengo,cl&units=imperial
    
    Currently calling city number 4919:
    zhob, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=zhob,pk&units=imperial
    
    Currently calling city number 4920:
    milos, gr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=milos,gr&units=imperial
    
    Currently calling city number 4921:
    colchester, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=colchester,ca&units=imperial
    
    Currently calling city number 4922:
    katra, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=katra,in&units=imperial
    
    Currently calling city number 4923:
    kashary, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kashary,ru&units=imperial
    
    Currently calling city number 4924:
    gejiu, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gejiu,cn&units=imperial
    
    Currently calling city number 4925:
    otradnoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=otradnoye,ru&units=imperial
    
    Currently calling city number 4926:
    migori, ke
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=migori,ke&units=imperial
    
    Currently calling city number 4927:
    nanga eboko, cm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=nanga eboko,cm&units=imperial
    
    Currently calling city number 4928:
    namwala, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=namwala,zm&units=imperial
    
    Currently calling city number 4929:
    odesskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=odesskoye,ru&units=imperial
    
    Currently calling city number 4930:
    viljoenskroon, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=viljoenskroon,za&units=imperial
    
    Currently calling city number 4931:
    ouro preto do oeste, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ouro preto do oeste,br&units=imperial
    
    Currently calling city number 4932:
    linjiang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=linjiang,cn&units=imperial
    
    Currently calling city number 4933:
    betwagan, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=betwagan,ph&units=imperial
    
    Currently calling city number 4934:
    priyutovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=priyutovo,ru&units=imperial
    
    Currently calling city number 4935:
    malacacheta, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=malacacheta,br&units=imperial
    
    Currently calling city number 4936:
    arvin, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=arvin,us&units=imperial
    
    Currently calling city number 4937:
    lakhisarai, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lakhisarai,in&units=imperial
    
    Currently calling city number 4938:
    bafra, tr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bafra,tr&units=imperial
    
    Currently calling city number 4939:
    sonipat, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sonipat,in&units=imperial
    
    Currently calling city number 4940:
    effium, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=effium,ng&units=imperial
    
    Currently calling city number 4941:
    hacienda heights, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=hacienda heights,us&units=imperial
    
    Currently calling city number 4942:
    ilinge, za
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ilinge,za&units=imperial
    
    Currently calling city number 4943:
    baherden, tm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=baherden,tm&units=imperial
    
    Currently calling city number 4944:
    krasnyy oktyabr, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=krasnyy oktyabr,ru&units=imperial
    
    Currently calling city number 4945:
    dumas, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dumas,us&units=imperial
    
    Currently calling city number 4946:
    damaturu, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=damaturu,ng&units=imperial
    
    Currently calling city number 4947:
    calceta, ec
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=calceta,ec&units=imperial
    
    Currently calling city number 4948:
    maua, ke
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=maua,ke&units=imperial
    
    Currently calling city number 4949:
    gatbo, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=gatbo,ph&units=imperial
    
    Currently calling city number 4950:
    katsina, ng
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=katsina,ng&units=imperial
    
    Currently calling city number 4951:
    mongoumba, cf
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mongoumba,cf&units=imperial
    
    Currently calling city number 4952:
    calama, cl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=calama,cl&units=imperial
    
    Currently calling city number 4953:
    lakselv, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=lakselv,no&units=imperial
    
    Currently calling city number 4954:
    aykino, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=aykino,ru&units=imperial
    
    Currently calling city number 4955:
    saint-paul, re
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=saint-paul,re&units=imperial
    
    Currently calling city number 4956:
    strelka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=strelka,ru&units=imperial
    
    Currently calling city number 4957:
    chipinge, zw
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chipinge,zw&units=imperial
    
    Currently calling city number 4958:
    virden, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=virden,ca&units=imperial
    
    Currently calling city number 4959:
    bialogard, pl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bialogard,pl&units=imperial
    
    Currently calling city number 4960:
    estacion coahuila, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=estacion coahuila,mx&units=imperial
    
    Currently calling city number 4961:
    williams lake, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=williams lake,ca&units=imperial
    
    Currently calling city number 4962:
    assomada, cv
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=assomada,cv&units=imperial
    
    Currently calling city number 4963:
    tupancireta, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tupancireta,br&units=imperial
    
    Currently calling city number 4964:
    bangkalan, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bangkalan,id&units=imperial
    
    Currently calling city number 4965:
    yhu, py
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=yhu,py&units=imperial
    
    Currently calling city number 4966:
    sann, pk
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sann,pk&units=imperial
    
    Currently calling city number 4967:
    parana, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=parana,br&units=imperial
    
    Currently calling city number 4968:
    tamsweg, at
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=tamsweg,at&units=imperial
    
    Currently calling city number 4969:
    suceveni, ro
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=suceveni,ro&units=imperial
    
    Currently calling city number 4970:
    guerrero, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=guerrero,mx&units=imperial
    
    Currently calling city number 4971:
    wodonga, au
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=wodonga,au&units=imperial
    
    Currently calling city number 4972:
    linqing, cn
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=linqing,cn&units=imperial
    
    Currently calling city number 4973:
    makushino, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=makushino,ru&units=imperial
    
    Currently calling city number 4974:
    palmas, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=palmas,br&units=imperial
    
    Currently calling city number 4975:
    las choapas, mx
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=las choapas,mx&units=imperial
    
    Currently calling city number 4976:
    mbandaka, cd
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mbandaka,cd&units=imperial
    
    Currently calling city number 4977:
    two hills, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=two hills,ca&units=imperial
    
    Currently calling city number 4978:
    berkak, no
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=berkak,no&units=imperial
    
    Currently calling city number 4979:
    mokhotlong, ls
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mokhotlong,ls&units=imperial
    
    Currently calling city number 4980:
    ambalavao, mg
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=ambalavao,mg&units=imperial
    
    Currently calling city number 4981:
    kawambwa, zm
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kawambwa,zm&units=imperial
    
    Currently calling city number 4982:
    dok kham tai, th
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=dok kham tai,th&units=imperial
    
    Currently calling city number 4983:
    boquira, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=boquira,br&units=imperial
    
    Currently calling city number 4984:
    truth or consequences, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=truth or consequences,us&units=imperial
    
    Currently calling city number 4985:
    mandali, iq
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=mandali,iq&units=imperial
    
    Currently calling city number 4986:
    kimberley, ca
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=kimberley,ca&units=imperial
    
    Currently calling city number 4987:
    orshanka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=orshanka,ru&units=imperial
    
    Currently calling city number 4988:
    bambui, br
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=bambui,br&units=imperial
    
    Currently calling city number 4989:
    suwalki, pl
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=suwalki,pl&units=imperial
    
    Currently calling city number 4990:
    orcopampa, pe
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=orcopampa,pe&units=imperial
    
    Currently calling city number 4991:
    linao, ph
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=linao,ph&units=imperial
    
    Currently calling city number 4992:
    pampa, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pampa,us&units=imperial
    
    Currently calling city number 4993:
    fitionesti, ro
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=fitionesti,ro&units=imperial
    
    Currently calling city number 4994:
    acacias, co
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=acacias,co&units=imperial
    
    Currently calling city number 4995:
    betlitsa, ru
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=betlitsa,ru&units=imperial
    
    Currently calling city number 4996:
    abbeville, fr
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=abbeville,fr&units=imperial
    
    Currently calling city number 4997:
    pendleton, us
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=pendleton,us&units=imperial
    
    Currently calling city number 4998:
    sembakung, id
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=sembakung,id&units=imperial
    
    Currently calling city number 4999:
    chhindwara, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=chhindwara,in&units=imperial
    
    Currently calling city number 5000:
    manavalakurichi, in
    http://api.openweathermap.org/data/2.5/weather?appid=********************************&q=manavalakurichi,in&units=imperial
    
    


```python
# Just to see how many times the loop was abandoned due to my if statement or try/except statement
dupes_dropped = total_loops - non_dupe_drop_loop - 5000
print(f'Number of random coords generated: {total_loops}')
print(f'Number of dupe coords dropped: {dupes_dropped}')
print(f'Number of unique coords that don\'t have entries in OWM: {non_dupe_drop_loop}')

```

    Number of random coords generated: 52845
    Number of dupe coords dropped: 47257
    Number of unique coords that don't have entries in OWM: 588
    


```python
# Build data frame with collected data
df = pd.DataFrame({'City' : city,
                   'Cloudiness' : cloudiness,
                   'Country' : country,
                   'Date' : date,
                   'Humidity' : humidity,
                   'Lat' : latitude,
                   'Lng' : longitude,
                   'Max Temp' : temp,
                   'Wind Speed' : windspeed
                   })

print(len(df['City']))
df.head()
```

    5000
    




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
      <th>Cloudiness</th>
      <th>Country</th>
      <th>Date</th>
      <th>Humidity</th>
      <th>Lat</th>
      <th>Lng</th>
      <th>Max Temp</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>teya</td>
      <td>80</td>
      <td>ru</td>
      <td>2018-04-02 22:58:03</td>
      <td>75</td>
      <td>60.37</td>
      <td>92.63</td>
      <td>20.38</td>
      <td>5.64</td>
    </tr>
    <tr>
      <th>1</th>
      <td>vaini</td>
      <td>88</td>
      <td>to</td>
      <td>2018-04-02 22:57:55</td>
      <td>100</td>
      <td>-21.20</td>
      <td>-175.20</td>
      <td>79.87</td>
      <td>27.11</td>
    </tr>
    <tr>
      <th>2</th>
      <td>albany</td>
      <td>0</td>
      <td>au</td>
      <td>2018-04-02 22:58:04</td>
      <td>59</td>
      <td>-35.02</td>
      <td>117.88</td>
      <td>81.31</td>
      <td>9.89</td>
    </tr>
    <tr>
      <th>3</th>
      <td>barrow</td>
      <td>90</td>
      <td>us</td>
      <td>2018-04-02 22:35:00</td>
      <td>93</td>
      <td>39.51</td>
      <td>-90.40</td>
      <td>39.20</td>
      <td>8.05</td>
    </tr>
    <tr>
      <th>4</th>
      <td>faanui</td>
      <td>44</td>
      <td>pf</td>
      <td>2018-04-02 22:57:56</td>
      <td>100</td>
      <td>-16.48</td>
      <td>-151.75</td>
      <td>82.39</td>
      <td>11.79</td>
    </tr>
  </tbody>
</table>
</div>



<h1>Latitude vs Temperature Plot</h1>


```python
current_date =  df['Date'][0][:10]

df.plot.scatter('Lat', 'Max Temp', s=60, edgecolor='black', alpha=.70,
                figsize=(12, 8), fontsize=16, grid=True, 
                xlim=(-100, 100), ylim=(-50, 150))

plt.ylabel('Max Temperature (F)', fontsize=20)
plt.xlabel('Latitude', fontsize=20)
plt.title(f'City Latitude vs. Max Temperature on {current_date}', fontsize=22)

```




    Text(0.5,1,'City Latitude vs. Max Temperature on 2018-04-02')




![png](output_7_1.png)


<h1>Latitude vs Humidity Plot</h1>


```python
df.plot.scatter('Lat', 'Humidity', s=60, edgecolor='black', alpha=.70,
                figsize=(12, 8), fontsize=16, grid=True,
                xlim=(-100, 100), ylim=(-10, 110))

plt.ylabel('Humidity (%)', fontsize=20)
plt.xlabel('Latitude', fontsize=20)
plt.title(f'City Latitude vs. Humidity on {current_date}', fontsize=22)

```




    Text(0.5,1,'City Latitude vs. Humidity on 2018-04-02')




![png](output_9_1.png)


<h1>Latitude vs Cloudiness Plot</h1>


```python
df.plot.scatter('Lat', 'Cloudiness', s=60, edgecolor='black', alpha=.70,
                figsize=(12, 8), fontsize=16, grid=True,
                xlim=(-100, 100), ylim=(-10, 110))

plt.ylabel('Cloud Coverage (%)', fontsize=20)
plt.xlabel('Latitude', fontsize=20)
plt.title(f'City Latitude vs. Cloud Coverage on {current_date}', fontsize=22)

```




    Text(0.5,1,'City Latitude vs. Cloud Coverage on 2018-04-02')




![png](output_11_1.png)


<h1>Latitude vs Wind Speed Plot</h1>


```python
df.plot.scatter('Lat', 'Wind Speed', s=60, edgecolor='black', alpha=.70,
                figsize=(12, 8), fontsize=16, grid=True,
                xlim=(-100, 100), ylim=(-5, 50))

plt.ylabel('Wind Speed (mph)', fontsize=20)
plt.xlabel('Latitude', fontsize=20)
plt.title(f'City Latitude vs. Wind Speed on {current_date}', fontsize=22)


```




    Text(0.5,1,'City Latitude vs. Wind Speed on 2018-04-02')




![png](output_13_1.png)



```python
df.to_csv('WeatherPy_df.csv', encoding='utf-8')
```

<h1> End of homework, the rest is just extra stuff I did out of curiosity. </h1>


```python
current_date =  df['Date'][0][:10]

df.plot.scatter('Lng', 'Lat', s=60, edgecolor='black', alpha=0.50, c='Max Temp', colormap = 'jet',
                figsize=(20, 10), fontsize=16, grid=True, 
                xlim=(-200, 200), ylim=(-100, 100))

plt.ylabel('Latitude', fontsize=20)
plt.xlabel('Longitude', fontsize=20)
plt.title('Temp Map', fontsize=22)
```




    Text(0.5,1,'Temp Map')




![png](output_16_1.png)



```python
current_date =  df['Date'][0][:10]

df.plot.scatter('Lng', 'Lat', s=60, edgecolor='black', alpha=0.50, c='Humidity', colormap = 'rainbow',
                figsize=(20, 10), fontsize=16, grid=True, 
                xlim=(-200, 200), ylim=(-100, 100))

plt.ylabel('Latitude', fontsize=20)
plt.xlabel('Longitude', fontsize=20)
plt.title('Humidity Map', fontsize=22)
```




    Text(0.5,1,'Humidity Map')




![png](output_17_1.png)



```python
current_date =  df['Date'][0][:10]

df.plot.scatter('Lng', 'Lat', s=60, edgecolor='black', alpha=0.50, c='Cloudiness', colormap = 'cool',
                figsize=(20, 10), fontsize=16, grid=True, 
                xlim=(-200, 200), ylim=(-100, 100))

plt.ylabel('Latitude', fontsize=20)
plt.xlabel('Longitude', fontsize=20)
plt.title('Cloud Map', fontsize=22)
```




    Text(0.5,1,'Cloud Map')




![png](output_18_1.png)

