# instructions
## follow the following instructions
----------
# `NEW! April 4, 2024`
##      `TODO:`
-  # **ATTENTION!!!** 
-  You are getting `Status: Infeasible` when you run the code below, check why?!!!, we should get `Status: optimal` instead. You should get one location for each sensor at each hour! Check notebook for more details!!!
- explain in details, every single piece of code you applied here from pulp in the context of your data, what does it mean exactly, what does each dictionary, array mean for your data?!
- explain in details, every single piece of code you applied here from pulp in the context of your data, what does it mean exactly, what does each dictionary, array mean for your data?!
- store the data subsets in a folder in your repository on a .csv format!
- take the optimal locations of sensors in each hour, then draw a trajectory of all the hours of the day where each sensor should move. Since you have three sensors, this will result in three trajectories on the map
- you need to calculate the coordinates (longitude, latitude) for each optimal location of the sensors in each hour, then convert the list into .csv file, then for each sensor, construct a linestring which represents the trajectory of that sensor, then store all those trajectories in a geojson file of linestrings, then display on an interactive map (showing the sensors moving in different directions from hour to next hour)!
----------------
# `Required OPTIMIZATION ==> IMPORTANT!`

# TODO `next`:
1. [ ] run the example clustering code (DBSCAN), attached in the new folder "DBSCAN_Clustering" inside 'starting_code' folder
2. [ ] read more about how DBSCAN works in scikit-learn
    > [DBSCAN scikit-learn](https://scikit-learn.org/stable/modules/generated/sklearn.cluster.DBSCAN.html)
3. [ ] you need to use ```Silhouette Coefficient ```for evaluation
    > ***"If the ground truth labels are not known, evaluation can only be performed using the model results itself. In that case, the Silhouette Coefficient comes in handy."***
    an example is here:
    [Demo of DBSCAN clustering algorithm](https://scikit-learn.org/stable/auto_examples/cluster/plot_dbscan.html#sphx-glr-auto-examples-cluster-plot-dbscan-py)
- Then you need to `adapt` the stock version of the DBSCAN so that it operates a bit differently, specifically you need to do the following:
    - plain scikit-learn DBSCAN distance calculation relies on using  ```haversine``` as a distance metric to calculate haversine distances (i.e., geometrical distances) between coordinates (longitudes/latitudes pairs)
  > the attention that should be given in this case is that you did not capture any statistics regarding the distribution of the pm25 values (our target variable), you could for example capture the histograms of those values.
  Read more about possible values of ```pm2.5``` in this reference [PM2.5 particles in the air](https://www.epa.vic.gov.au/for-community/environmental-information/air-quality/pm25-particles-in-the-air). This means that you need to create a histogram showing the density of each bracket, your binning strategy should rely on the community definition of ranges of values. For example, binning example is the follwoing: Less than 25, 25–50, 50–100, 100–300, More than 300. Simialr to what appears in the following figure[in notebook].
  
    - Also, draw histograms showing the same binning and density of pm2.5 values in each neighborhood in your data. By the way, how many neighborhoods you have in your data?!
  > this is important as it will inform us about the fact wether nearby locations are having similar pm25 values. Why do we need to do this, because it is only in that case we consider those as a cluster, since they are geographiclly nearby, and also having simialr feature values (pm25 in this case). So what you need to do next is the following:
  - .. ```Extract and normalize several features```, similar to what has been done in the following tutorial, read specifically [Extending DBSCAN beyond just spatial coordinates](https://musa-550-fall-2020.github.io/slides/lecture-11A.html), thereafter ```Run DBSCAN to extract high-density clusters``` passing as an argument to the DBSCAN the new ```scaled features```, probably something like ```cores, labels = dbscan(features, eps=eps, min_samples=min_samples)``` . notice passing features (including pm25, longitude, latitude) instead of simply the coordinates.
  - having done this novel distance calculation (based on geometrical distance and pm25 values distance), calculate again the ```silhouette_score```, and check wether you obtain a higher accuracy (higher silhouette_score values) or not!

  > **N.B.** we are able to do this because of the definition of ```metric``` in DBSCAN which says **metric: The metric to use when calculating distance between instances in** a ```feature array``` so, it  a distance between several features is possible, given a ```feature array```, so put your scaled features in a feature array.
- You need to apply this customized DBSCAN version to each time window depending on the time-discritization that you have selected (e.g., hours, minutes, part of day including morning, afternoon and evening). Now imagine that you have obtained a list of best clusters using adapted-DBSCAN for a specific time(for example an hour), then this is the initial list of potential locations of sensors that you should feed to the Integer Linear Programming (ILP) algorithm. For example, you can take the centers (cores) of those clsuters as the potential sensor deployment positions. Then you pass those to the following part of the previous code as a `# Decision variables`, `    num_sites = len(cluster_centers)`, then this will change the following ` sensors = LpVariable.dicts("Sensor", range(num_sites), cat=LpBinary)`, which means `range(num_sites)` is reduced to equal only number of clusters, and the problem complexity is then reduced!
- formulate your optimization problem similar to the reference paper, and similar to the following example [A Transportation Problem](https://coin-or.github.io/pulp/CaseStudies/a_transportation_problem.html).
So, the following steps:
    - **Identify the Decision Variables**: 
        - use binary decision variables Xp to specify if a sensor should be placed at a position p or not. Remeber those are the cluster centers that you have obtained using DBSCAN!
        - costp the cost of deploying a sensor at a position p. Now, how to calculate this cost for each sensor?! Since you have decided that clusters are the potential positions, then you calculate the total `area` of each cluster and that would, then you use normalization to declare cost, bigger area coverage means higher cost!
    - **Formulate the Objective Function**: It should minimize the overall deployment cost as formulated in the objective function. Your objective function should be similar to `Minimise the deployment Costs = costp the cost of deploying a sensor at a position p * binary place sensor in p1 or not + … + costp the cost of deploying a sensor at a position pj * binary place sensor in pj or not`, which is equivalent to, `ILP = min SIGMA
    
    ```math
    Min  \sum_{p ∈ P} cost_p x_p  
    ```
    > `You should modify the code so that it accomodates those changes!`

------------------------------
# `things to consider later on`
  - one importance configurable parameter in this case is the distance metric.
  > you can use instead 
    - most importantly the following : ```From scikit-learn: [‘cityblock’, ‘euclidean’, ‘l1’, ‘l2’, ‘manhattan’].```
    - and probably aome of the following ```From scipy.spatial.distance: [‘braycurtis’, ‘canberra’, ‘chebyshev’, ‘dice’, ‘hamming’, ‘jaccard’, ‘kulsinski’, ‘rogerstanimoto’, ‘russellrao’,, ‘sokalmichener’, ‘sokalsneath’, ```

  - then, having permutate this configurable parameter, capture the ```silhouette_score``` and compare, then draw an x-y figure such as the following [figure attached in notebook], (```you can do those graphs in MS excel after capturing the numbers```):
  - Provide a detailed explanation of the values you obtained for ```Silhouette Coefficient```
    - take some insights from [scikit-learn docs](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.silhouette_score.html#sklearn.metrics.silhouette_score)
    - That is to say, >
    > "The best value is 1 and the worst value is -1. Values near 0 indicate overlapping clusters. Negative values generally indicate that a sample has been assigned to the wrong cluster, as a different cluster is more similar."
  - you need to do more analytics and Exploratory Spatio-Temporal Data Analytics (ESTDA,  read specifically [Extending DBSCAN beyond just spatial coordinates](https://musa-550-fall-2020.github.io/slides/lecture-11A.html), thereafter, for example ```Identify the 5 largest clusters``` , ```Get mean statistics for the top 5 largest clusters```, ```Visualize the top 5 largest clusters```, ```Visualizing one cluster at a time```.
- add other metrics from the [sklearn.metrics](https://scikit-learn.org/stable/modules/clustering.html#clustering-performance-evaluation), for example the following:
  > - ```Davies-Bouldin Index```, **Zero is the lowest possible score. Values closer to zero indicate a better partition.**
  for your experiment, we have obtained a number on par with 2.78!, which is very high! so probably your clustering scheme misses something! try different combinations of configurations (sampling scheme, distance methods, distance based on combination of features such as geographical long/lat and pm25 values), read my previous comments to get insights.
    - ```Calinski-Harabasz Index```. You have computed that already, but what is your explanation and reasoning of the results obtained!
----------------------


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