# A Practical Tutorial on Using PostGIS with PostgreSQL

This tutorial will walk you through how to install, configure, and use PostGIS — the spatial extension for PostgreSQL. By the end of this guide, you'll understand how to store, query, and manipulate spatial (geographic) data using SQL.

---

## Prerequisites

* PostgreSQL installed (version 12+ recommended)
* Basic understanding of SQL
* Access to a terminal or SQL client (like `psql`, PgAdmin, or DBeaver)

---

## Step 1: Install PostGIS

### Ubuntu/Debian

```bash
sudo apt install postgis postgresql-15-postgis-3
```

### macOS (using Homebrew)

```bash
brew install postgis
```

### Windows

Use the [PostgreSQL installer](https://www.postgresql.org/download/windows/) and select PostGIS during installation.

---

## Step 2: Enable PostGIS in Your Database

Connect to your database using `psql` or a SQL client:

```sql
CREATE DATABASE geo_example;
\c geo_example
CREATE EXTENSION postgis;
```

You can check the installed functions with:

```sql
SELECT PostGIS_full_version();
```

---

## Step 3: Create a Spatial Table

Let’s create a simple table that stores city data with geographic points.

```sql
CREATE TABLE cities (
  id SERIAL PRIMARY KEY,
  name TEXT NOT NULL,
  location GEOGRAPHY(Point, 4326) NOT NULL
);
```

### Explanation:

* `GEOGRAPHY(Point, 4326)` tells PostGIS to use spherical coordinates.
* `4326` is the SRID for WGS 84 — the most commonly used GPS coordinate system.

---

## Step 4: Insert Spatial Data

```sql
INSERT INTO cities (name, location)
VALUES
  ('San Francisco', ST_SetSRID(ST_MakePoint(-122.4194, 37.7749), 4326)::geography),
  ('New York', ST_SetSRID(ST_MakePoint(-74.0060, 40.7128), 4326)::geography),
  ('Los Angeles', ST_SetSRID(ST_MakePoint(-118.2437, 34.0522), 4326)::geography);
```

---

## Step 5: Query by Distance

### Find all cities within 4000 km of San Francisco:

```sql
SELECT name, ST_Distance(location, sanfran.location) AS distance_km
FROM cities,
  (SELECT location FROM cities WHERE name = 'San Francisco') AS sanfran
WHERE ST_DWithin(location, sanfran.location, 4000000);  -- distance in meters
```

### Compute Distance Between Two Cities:

```sql
SELECT
  a.name AS city_a,
  b.name AS city_b,
  ST_Distance(a.location, b.location) / 1000 AS distance_km
FROM cities a
JOIN cities b ON a.name = 'San Francisco' AND b.name = 'New York';
```

---

## Step 6: Indexing for Performance

Spatial queries can be slow without indexing. Use a **GIST** index:

```sql
CREATE INDEX cities_location_idx ON cities USING GIST(location);
```

This allows fast proximity searches and spatial joins.

---

## Step 7: Visualizing Data

While PostGIS itself is backend-focused, you can visualize spatial data using:

* **QGIS**: Desktop GIS tool to connect to your PostGIS DB and visualize layers.
* **GeoJSON + Leaflet**: Convert PostGIS data to GeoJSON and render maps in web apps.

```sql
SELECT name, ST_AsGeoJSON(location) FROM cities;
```

---

## Bonus: Store Routes or Areas

Use `LINESTRING` or `POLYGON` types.

```sql
CREATE TABLE routes (
  id SERIAL PRIMARY KEY,
  name TEXT,
  path GEOGRAPHY(LINESTRING, 4326)
);
```

Insert example:

```sql
INSERT INTO routes (name, path)
VALUES (
  'Flight Path A',
  ST_SetSRID(ST_MakeLine(
    ARRAY[
      ST_MakePoint(-122.4194, 37.7749),
      ST_MakePoint(-74.0060, 40.7128)
    ]), 4326)::geography
);
```

---

## Summary

You’ve now:

* Installed PostGIS
* Created spatial tables
* Inserted and queried geographic data
* Learned about performance via indexing
* Exported to GeoJSON for mapping

PostGIS unlocks the power of spatial queries right inside your PostgreSQL database. With just a few SQL functions, you can add location-based intelligence to your app without relying on third-party APIs.

Next steps:

* Explore more spatial functions: `ST_Intersects`, `ST_Contains`, `ST_Buffer`, etc.
* Try integrating with frontend maps (Leaflet, Mapbox)
* Build spatial REST APIs using Node.js and Express
