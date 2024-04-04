# instructions
## follow the following instructions
----------
NEW! April 4, 2024

TODO:
-  **ATTENTION!!!** You are getting `Status: Infeasible` when you run the code below, check why?!!!, we should get `Status: optimal` instead. You should get one location for each sensor at each hour!
- explain in details, every single piece of code you applied here from pulp in the context of your data, what does it mean exactly, what does each dictionary, array mean for your data?!
- explain in details, every single piece of code you applied here from pulp in the context of your data, what does it mean exactly, what does each dictionary, array mean for your data?!
    - store the data subsets in a folder in your repository on a .csv format!

  - take the optimal locations of sensors in each hour, then draw a trajectory of all the hours of the day where each sensor should move. Since you have three sensors, this will result in three trajectories on the map
    - you need to calculate the coordinates (longitude, latitude) for each optimal location of the sensors in each hour, then convert the list into .csv file, then for each sensor, construct a linestring which represents the trajectory of that sensor, then store all those trajectories in a geojson file of linestrings, then display on an interactive map (showing the sensors moving in different directions from hour to next hour)!

----------------

1. [ ] run the example starting code and familiarize yourself with some geosaptial processing techniques, including:
    - sampling
    - spatial join
    - geo-visualization

2. [ ] reference papers include:
    > [1. WSN AQ monitoring](https://inria.hal.science/hal-01392863) 

3. [ ] start by performing Exploratory Data Analytics (EDA) for the data
    > for example
        - historgrams to study the distribution of data (you have three sensors)
        - ```Kernel Density (univariate, aspatial)```
        - get insights from the following:
    - [02_geovisualization](https://darribas.org/gds_scipy16/ipynb_md/02_geovisualization.html)
    - [Exploratory Spatial Data Analysis (ESDA)](https://darribas.org/gds_scipy16/ipynb_md/04_esda.html)
    - [NYC Data](https://github.com/PacktPublishing/Geospatial-Data-Science-Quick-Start-Guide/blob/master/Chapter02/NYC%20Data.ipynb)
    - [Performing Spatial operations like a Pro](https://github.com/PacktPublishing/Geospatial-Data-Science-Quick-Start-Guide/blob/master/Chapter03/Chapter3.ipynb) such as ```Spatial join```

4. [ ] imagine dividing your data into snapshots (time-based, being it week month day depending on our EDA)
    - Run the ILP (Integer Linear Programming) for each snapshot individually, this will result in optimal location(s) at this specific time interval (depends on our division, day month etc.,) of each sensor so that it covers most of the space we have
    - We store those in a file (csv, geojson) containing for each row: sensorID, time (day, month), longitude, latitude, 
    - From this we can draw trajectory of ```best deployment locations``` of each sensor over time 