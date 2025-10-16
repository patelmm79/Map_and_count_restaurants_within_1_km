# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This project uses Python geospatial libraries to analyze and visualize restaurants within a specified radius. The primary use case demonstrates counting pizzerias within a 1 km radius of a point in NYC using OpenStreetMap data.

## Core Architecture

### Notebooks

- **Map_locations_in_radius.ipynb**: Main implementation notebook that demonstrates the full workflow for spatial analysis
- **main.ipynb**: Contains analysis of travel/advertising dataset (appears to be from a different project/assessment)

### Geospatial Analysis Workflow

The project follows this pipeline (implemented in Map_locations_in_radius.ipynb):

1. **Parameter Input**: Define starting coordinates (lat/lon), radius in km, and cuisine type
2. **Polygon Generation**: Use `geog` package to create a circle polygon around the center point with specified radius
3. **Data Retrieval**: Query OpenStreetMap via Overpass API to fetch restaurant data within a bounding box
4. **Spatial Join**: Use GeoPandas `sjoin` with `op='within'` to determine which points fall within the circle polygon
5. **Visualization**: Display results on interactive Folium map with popup information

### Key Dependencies

- **geopandas**: GeoDataFrame operations and spatial joins
- **geog**: Geodesic functions for creating circle polygons from center points
- **shapely.geometry**: Geometric object creation (Point, Polygon)
- **overpass**: Python wrapper for OpenStreetMap Overpass API
- **folium**: Interactive map visualization with popups and marker clusters
- **pandas/numpy**: Data manipulation

## Working with the Code

### Running the Analysis

Open and run `Map_locations_in_radius.ipynb` in Jupyter. Modify these input parameters in cell 5:

```python
radius = 1  # radius in KM
starting_lat = 40.679044
starting_lon = -73.971655
cuisine = 'pizza'
```

### Coordinate System

The project uses EPSG:4326 (WGS84) coordinate reference system for all geospatial operations.

### Extending to Other Amenities

To query different types of locations, modify the Overpass API query (cell 11). The current query uses `node["amenity"="restaurant"]["cuisine"="pizza"]`. You can change:
- `amenity` parameter (e.g., "cafe", "bar", "fast_food")
- `cuisine` parameter (e.g., "italian", "chinese", "burger")

### Known Limitations

- **Data Quality**: OpenStreetMap data may be incomplete; some restaurants may not be captured
- **Polygon Shape**: Uses circular radius rather than walking/driving time isochrones, which may not reflect realistic accessibility

## Development Workflow

This is a Jupyter notebook-based analysis project. Execute cells sequentially to reproduce the analysis. The notebooks contain embedded visualizations that display within the notebook output.

### Modifying Visualizations

- Zoom level is calculated dynamically: `zoom_level = (radius * -1) + 16`
- Maps use CartoDB Positron base tiles
- Popup tables are styled with custom HTML/CSS in cell 16

## Spatial Join Logic

The core analytical step uses GeoPandas spatial join:
```python
pointInPolys = gpd.sjoin(gs, polygon_frame, op='within')
```

This operation filters restaurant points that fall within the circle polygon, accounting for the difference between the initial bounding box query (which returns more results) and the actual circular area of interest.
