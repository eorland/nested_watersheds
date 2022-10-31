## Easy catchment delineation in Python

Wrapper for the awesome [pysheds](https://github.com/mdbartos/pysheds) library for quick nested catchment delineation. Using a DEM as input, the functions provided in this repo will return two ```GeoDataFrames```: one detailing the stream network and another descibing the corresponding catchment for each segment. Major thanks to Matt Bartos for his work on ```pysheds``` to make this possible.

Example: 
```
import make_catchments
import folium

basins, branches = make_catchments.generate_catchments('path/to/dem.tif', acc_thresh=5000,so_filter=4)
```
Output: 
```
Reading DEM...
Making flow accumulaton map...
Generating stream network...
Calculating pour points...
Generating 561 catchments...
Total runtime is 1.66 minutes
```
```
# Visualize output
gdf = basins.copy()
# separate by stream order
ones = gdf[gdf['Order']==1]
twos = gdf[gdf['Order']==2]
threes = gdf[gdf['Order']==3] # skip fours

# Map 'em!
cols = ['Index','Length','Relief','Order','Slope','AreaSqKm','LocalPP_X','LocalPP_Y','Final_Chain_Val','BasinGeo']
cols_branches = ['Index','Length','Relief','Order','Slope','geometry','LocalPP_X','LocalPP_Y','Final_Chain_Val']

m = branches[cols_branches].explore(color='black')
ones[cols].explore(m=m,color='blue',tiles='Stamen Terrain')
twos[cols].explore(m=m,color='purple',tiles='Stamen Terrain')
threes[cols].explore(m=m,color='yellow',tiles='Stamen Terrain')
folium.LayerControl().add_to(m) 
m
```

![sample_output](https://user-images.githubusercontent.com/51799071/199057768-1a167084-cecc-4795-a2c2-7df79637e0db.PNG)

## A few other notes
The sample DEM provided in this repo is at 10m resolution. Function runtime will vary based on DEM size, resolution, and the thresholds used. Expect much longer runtimes if using a large DEM. To make the start up process easier, a `.yml` file is included. Main dependencies are:  `pysheds`,`rasterio`, and `geopandas`.  
