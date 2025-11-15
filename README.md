# Naples-Bus-Line151-Analysis
import pandas as pd
from pathlib import Path

Path = r'C:\Users\Ayyan LapTop\OneDrive - Università di Napoli Federico II\Desktop\MSC TEAMS UNINA\SEMESTERS\semester 03\Intelligent Transportation System\ITS PROJECTS\ANM_EAV_GTFS_FILES\ANM_GTFS DATA\\'

#load gtfs table ko
stops = pd.read_csv(Path + "stops.txt")
routes = pd.read_csv(Path + "routes.txt")
trips = pd.read_csv(Path + "trips.txt")
stop_times = pd.read_csv(Path + "stop_times.txt")

#Convert times to datetime: (GTFS time is HH:MM:SS (can go beyond 24:00:00!).)
stop_times['arrival_time'] = pd.to_timedelta(stop_times['arrival_time'])
stop_times['departure_time'] = pd.to_timedelta(stop_times['departure_time'])
stop_times.head()

#route define kiya bus ka, humna konsi bus ka dekanaha
routes_151 = routes[routes['route_short_name'] == '151']
routes_151 #151 ki route id 3941 ha: route_id	agency_id	route_short_name	route_long_name	route_type	route_url	route_color	route_text_color

#TRIPS define
#route id milna ka bad humna trips ka filter lagana ha, This will show all the trip_id that belong to bus 151.
route_id_151 = routes_151['route_id'].iloc[0]
trips_151 = trips[trips['route_id'] == route_id_151]
trips_151.head()

#STOP TIME;
#give you information about trip_id	arrival_time	departure_time	stop_id	stop_sequence	stop_headsign	pickup_type	drop_off_type
#ab ain trips id ko use kerka hum stop time dekha ga
stop_times_151 = stop_times[stop_times['trip_id'].isin(trips_151['trip_id'])] # first stop time means all trips and then second keep only 151 trips. isin() is a filtering method: “Keep only rows where this value exists in this list/series.”
stop_times_151.head()

#SEQUENCE OF STOPS WITH NAMES
#340_CodUdp:D472_29493922
one_trip = trips_151['trip_id'].iloc[0]
#MERGE ALL THE THINGS IN ONE LIKE stop id, name, lat, log,arrival time departure time etc
route151_sequence = stop_times_151.merge(
    stops[['stop_id', 'stop_name', 'stop_lat', 'stop_lon']], 
    on='stop_id', 
    how='left')
route151_sequence
import folium 
from IPython.display import display
m = folium.Map(location=[40.85, 14.27], zoom_start=12)
coords = route151_sequence[['stop_lat','stop_lon']].values.tolist()
folium.PolyLine(coords).add_to(m)
display(m)
