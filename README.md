# Robert Zupan Capstone Project
The purpose of this repository is to display the code and results of my capstone project while at The Data Incubator.

## Background and Motivation
The United States has had infrastructure problems for well over 20 years. Specfically, the American Society of Civil Engineers (ASCE) periodically releases an infrastructure report card ([link to report](https://infrastructurereportcard.org/)) and the current overall grade is a C-. More specifically, in 2017 the United States' roads received a D rating and require an estimated $4.59 trillion to improve over the next ten years. There are a multitude of ways to reduce this cost and efficiently tackle these infrastructure problems, but a promising approach is to utilize data science. The goal of my capstone project is to utilize data science and machine learning methods to identify car crash "hot spots" (i.e., locations that have a statistically higher number of crashes) and their potential causes. This work can reduce investigatory costs and aid in the distribution of funds. A secondary goal is to provide these findings for use in map services (Google Maps, Bing, MapQuest, etc.) as well as self-driving/smart car databases in order to potentially reroute traffic during dangerous periods.

## The Data
The data used for this project is available as a Kaggle database ([link to database](https://www.kaggle.com/sobhanmoosavi/us-accidents)). This data includes crashes pulled from both Bing and MapQuest APIs. It includes information such as: severity of the crash, start time of crash, latitude/longitude of the crash location, weather conditions, etc. I will be specifically utilizing the latitude/longitude coordiantes, start time, and weather conditions.

## Data Wrangling
### Initial Investigation
The first step in the data wrangling process was to collapse the data down to a simple list of unique latitude and longitude pairs and the count of crashes at each location to get an idea if this database has any potential hotspots. This was the primary goal in the `Zupan_Capstone_Statistics.ipynb` notebook. It was a relatively simple process that resulted in a complexity of O(n). The results were stored as a dictionary where the key was the latitude/longitude pair and the value was the number of crashes at that location.

### Latitude/Longitude Aggregation
The next step was to collapse the data even further by combining latitude/longitude pairs that were close enough to be considered the same location. This was done in the `LatLonCombine.ipynb` notebook. The notebook includes a function that calculates the distance between latitude/longitude pairs (which utilizes the Haversine formula). If a latitude and longitude pair was calculated to be within 30 meters of another it was removed and added to the other's total number of crashes. This was done with nested for loops so it resulted in a complexity of O(n<sup>2</sup>). This could have been made more efficient in a couple of ways. Primarily, the cost of the Haversine formula was quite expensive. Considering I am dealing with very small distances this notebook could be made much more efficient by using something along the lines of a Manhatten distance (which I do in a later notebook). Additonally, within this notebook I identify outliers by considering any latitude/longitude pair that has a number of crashes greater than the mean number of crashes plus three standard deviations. Both the final set of aggregated latitude/longitude pairs and the outliers were stored as dictionaries with keys as the latitude/longitude pairs and the values as the number of crashes.

### Data Trimming and Grouping
The final step in handling the data was to first trim the data (remove information that wasn't useful to my analysis) and then group the data based on the outliers. The trimming of the data was essentially just removing columns that didn't have information pertinent to my analysis. This was done in the `CapstoneTrim.ipynb` notebook. It utilizes some basic pandas commands to convert the csv file to a DataFrame then drops the rows that aren't important. This data was stored as a DataFrame. The outlier grouping was performed in the `Capstone_Extract.ipynb` notebook. This notebook took the shortcut of using a Manhatten distance to determine which latitude/longitude pairs were close to an outlier's location since the Haversine formula was so expensive. Specifically, some basics pandas math was used to determine if each location was within an acceptable Manhatten distance and then extracted if so. Each of the outliers now have a DataFrame that represents the full information for each of their crashes. These DataFrames will be used in the proceeding machine learning notebooks.

## Visualization and Web App
With the outlier data a visualization of the crash hot spots can now be made. This was carried out in the `CapstoneViz.ipynb` notebook. Specifically, folium was used to create an interactive OpenStreetMap, branca was utilized to include a colormap/legend, along with markers for each of the outlier locations. Both the size and color of the markers indicate the number of crashes. Two visual cues were used due to the individual cues haveing issues. The result can be found within the notebook itself, or within the web app developed through Heroku. The github for the web app can be found [here](https://github.com/rzupan/CapstoneMap.git) and the web app itself can be found [here](https://rzupan-capstone.herokuapp.com/). Note the web app takes some time to load after entering your location, this is due to not being able to directly store the map and having to plot it each request. I currently have it catching typos but if a request takes more than 30 seconds it will time the app out and I don't currently have a fix for this.
