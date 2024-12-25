# TD3
Fonctions de SQL Spatial

This repository contains the steps for **Using Spatial SQL Functions** with PostGIS to analyze geospatial data. This exercise focuses on identifying parcels within a 1 km radius of a fire point and calculating related spatial queries.

## Overview

The tutorial includes:

1. **Importing Shapefiles into PostGIS**
2. **Querying Parcel Data**
3. **Identifying Fire Points**
4. **Analyzing Risk Zones**
5. **Calculating Parcel Statistics**

## Steps

### 1. Import Shapefile into PostGIS
- Download the shapefile for parcels in Florida.
- Open **PostGIS Shapefile Import/Export Manager** (search for it in your system).
- Select the shapefile and import it into a PostgreSQL table.

### 2. Query Parcel Data
- Verify the data by running the following queries in PgAdmin:
  ```sql
  SELECT COUNT(*) FROM parcels;
  ```
  ```sql
  SELECT * FROM parcels LIMIT 5;
  ```

### 3. Identify the Fire Point
- Use the following query to calculate the centroid of a specific parcel (e.g., `gid = 462273`):
  ```sql
  SELECT ST_Centroid(geom) AS fire_point
  FROM parcels
  WHERE gid = 462273;
  ```

### 4. Analyze Risk Zones
- In QGIS:
  1. Duplicate the `parcels` layer and rename it to `fire-risk`.
  2. Right-click on the `fire-risk` layer and select `Filter`.
  3. Use the following filter expression to select parcels within 1 km of the fire point:
     ```sql
     ST_DWithin(geom, (SELECT ST_Centroid(geom) FROM "public"."parcels" WHERE gid = 462273), 1000)
     ```
  4. Adjust the symbology to highlight risk zones.

- To include another fire point (e.g., `gid = 460957`), modify the filter to include both:
  ```sql
  ST_DWithin(geom, (SELECT ST_Centroid(geom) FROM "public"."parcels" WHERE gid IN (462273, 460957)), 1000)
  ```

### 5. Calculate Parcel Statistics
- Run spatial SQL queries to answer the following questions:
  1. **How many parcels are within 1 km of the two fire points?**
     ```sql
     SELECT COUNT(*)
     FROM parcels
     WHERE ST_DWithin(geom, (SELECT ST_Centroid(geom) FROM "public"."parcels" WHERE gid IN (462273, 460957)), 1000);
     ```
  2. **What is the total area of parcels near the fire points?**
     ```sql
     SELECT SUM(ST_Area(geom))
     FROM parcels
     WHERE ST_DWithin(geom, (SELECT ST_Centroid(geom) FROM "public"."parcels" WHERE gid IN (462273, 460957)), 1000);
     ```

## Notes
- Refer to the [PostGIS documentation](https://postgis.net/docs/reference.html) for additional spatial functions.
- Ensure the correct SRID (`4326`) is used for geometry columns.

## License
This repository is licensed under the MIT License. See `LICENSE` for details.
