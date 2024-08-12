# PRODIGY_DS_05
import os
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
import folium
from folium.plugins import HeatMap
import kaggle

dataset_name = 'mysarahmadbhat/nyc-traffic-accidents'
download_path = 'C:/Users/LENOVO/Downloads/NYC Accidents 2020.zip'
extract_path = 'C:/Users/LENOVO/Downloads/'
csv_file_name = 'NYC Accidents 2020.csv'  
csv_file_path = os.path.join(extract_path, csv_file_name)

if not os.path.exists(csv_file_path):
    print("Downloading dataset...")
    kaggle.api.dataset_download_files(dataset_name, path=extract_path, unzip=True)
else:
    print("Dataset already downloaded.")

df = pd.read_csv(csv_file_path)
df['DATE'] = pd.to_datetime(df['CRASH DATE'] + ' ' + df['CRASH TIME'])

df['Hour'] = df['DATE'].dt.hour
df['DayOfWeek'] = df['DATE'].dt.dayofweek
df['Month'] = df['DATE'].dt.month
df['Year'] = df['DATE'].dt.year

# Accidents by time of day
plt.figure(figsize=(12, 6))
sns.countplot(x='Hour', data=df)
plt.title('Accidents by Hour of Day')
plt.xlabel('Hour of Day')
plt.ylabel('Count')
plt.show()

# Accidents by day of the week
plt.figure(figsize=(12, 6))
sns.countplot(x='DayOfWeek', data=df)
plt.title('Accidents by Day of the Week')
plt.xlabel('Day of the Week')
plt.ylabel('Count')
plt.xticks(ticks=np.arange(7), labels=['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday'])
plt.show()

# Accidents by month
plt.figure(figsize=(12, 6))
sns.countplot(x='Month', data=df)
plt.title('Accidents by Month')
plt.xlabel('Month')
plt.ylabel('Count')
plt.xticks(ticks=np.arange(1, 13), labels=['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'])
plt.show()

# Accidents by contributing factor
plt.figure(figsize=(12, 6))
contributing_factors = df['CONTRIBUTING FACTOR VEHICLE 1'].value_counts().nlargest(10)
sns.barplot(x=contributing_factors.index, y=contributing_factors.values)
plt.title('Top 10 Contributing Factors for Accidents')
plt.xlabel('Contributing Factor')
plt.ylabel('Count')
plt.xticks(rotation=45)
plt.show()

# Visualization of accident hotspots
map_center = [df['LATITUDE'].mean(), df['LONGITUDE'].mean()]
m = folium.Map(location=map_center, zoom_start=10)

# HeatMap
heat_data = [[row['LATITUDE'], row['LONGITUDE']] for index, row in df.iterrows() if not pd.isnull(row['LATITUDE']) and not pd.isnull(row['LONGITUDE'])]
HeatMap(heat_data).add_to(m)
m.save('nyc_accident_hotspots.html')

import webbrowser
webbrowser.open('nyc_accident_hotspots.html')



