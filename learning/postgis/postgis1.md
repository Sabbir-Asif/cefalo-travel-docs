# Introduction to PostGIS: Concepts, Examples, and Practical Usage

PostGIS is a powerful spatial database extender for PostgreSQL. It adds support for geographic objects, allowing location queries to be run in SQL. This blog post provides a comprehensive introduction to PostGIS, covering the fundamental concepts and subtle details necessary to integrate it into your application, particularly for a TypeScript + Node.js backend using tools like Express and Knex.

## Why Use PostGIS?

Most applications with real-world data need to deal with geographical information at some point. Examples include:

* Finding the nearest store to a customer.
* Calculating distances between two geographic points.
* Querying objects within a geographic boundary.

Without PostGIS, handling these kinds of tasks requires external libraries or services (e.g., Google Maps API), which may incur cost, latency, and complexity. PostGIS brings spatial capability directly into your PostgreSQL database, enabling efficient and accurate spatial queries using SQL.

## Core Concepts of PostGIS

To understand how to use PostGIS, it's essential to learn the following core concepts:

### 1. Geometry vs. Geography Types

**Geometry** types are based on a flat, Cartesian coordinate system. They are accurate for small areas but do not take the earth's curvature into account.

**Geography** types model the earth as a sphere, which allows for accurate distance and area calculations over long distances. Most web mapping and real-world geographic apps should use geography.

In PostGIS, both are available. Geography is recommended when dealing with latitude and longitude.

```sql
-- Geometry example
SELECT ST_Distance(
  ST_GeomFromText('POINT(0 0)'),
  ST_GeomFromText('POINT(3 4)')
); -- returns 5

-- Geography example
SELECT ST_Distance(
  geography(ST_MakePoint(-122.5, 37.7)),
  geography(ST_MakePoint(-122.3, 37.8))
); -- returns distance in meters
```

### 2. SRID (Spatial Reference System Identifier)

SRID defines the coordinate system and projection. The most common SRID is **4326**, which uses the WGS84 coordinate system (latitude and longitude).

When creating geography points, always use `ST_SetSRID` to ensure the data uses the correct reference system.

```sql
SELECT ST_SetSRID(ST_MakePoint(-122.5, 37.7), 4326);
```

### 3. Points and Other Geometry Types

PostGIS supports many geometry types:

* **POINT**: a single location.
* **LINESTRING**: a path (e.g., a road or route).
* **POLYGON**: an area (e.g., a city boundary).
* **MULTIPOINT**, **MULTILINESTRING**, **MULTIPOLYGON**: collections of the above.

For most use cases (e.g., storing locations), the **POINT** type is sufficient.

```sql
-- Create a point
SELECT ST_MakePoint(longitude, latitude);
```

When using geography, you typically wrap the point:

```sql
SELECT geography(ST_SetSRID(ST_MakePoint(long, lat), 4326));
```

## Installing and Enabling PostGIS

### PostgreSQL Extension

To use PostGIS, you must enable the extension in your database:

```sql
CREATE EXTENSION postgis;
```

This makes spatial functions and types available.

## Practical Use Case: Transport Table

Let’s say we have a `transports` table with routes between two points. Each route has a starting and destination point.

```sql
CREATE TABLE transports (
  id SERIAL PRIMARY KEY,
  name TEXT NOT NULL,
  type TEXT CHECK (type IN ('bus', 'train', 'flight')),
  startingLocationName TEXT NOT NULL,
  startingPoint GEOGRAPHY(POINT, 4326) NOT NULL,
  destinationLocationName TEXT NOT NULL,
  destinationPoint GEOGRAPHY(POINT, 4326) NOT NULL,
  departure_time TIMESTAMP NOT NULL,
  fare FLOAT NOT NULL
);
```

### Inserting Data

To insert data, use `ST_MakePoint` and convert it into `GEOGRAPHY` with `ST_SetSRID`:

```sql
INSERT INTO transports (
  name, type, startingLocationName, startingPoint,
  destinationLocationName, destinationPoint, departure_time, fare
) VALUES (
  'Intercity Express', 'train', 'City A',
  ST_SetSRID(ST_MakePoint(90.4125, 23.8103), 4326)::geography,
  'City B', ST_SetSRID(ST_MakePoint(91.4125, 24.8103), 4326)::geography,
  '2025-06-01 09:00:00', 1200.00
);
```

Notice how the coordinates are provided as `(longitude, latitude)`.

### Querying Distance

Let’s say we want to find all transports starting within 10 kilometers of a point.

```sql
SELECT * FROM transports
WHERE ST_DWithin(
  startingPoint,
  ST_SetSRID(ST_MakePoint(90.4000, 23.8000), 4326)::geography,
  10000
);
```

Here, `ST_DWithin` checks whether two geography objects are within a given distance (in meters).

## Using PostGIS with Knex

In Knex.js, you can insert spatial data using `db.raw` to pass raw SQL for creating `geography` points:

```ts
await knex('transports').insert({
  name: 'Test Flight',
  type: 'flight',
  startingLocationName: 'Airport A',
  startingPoint: knex.raw(
    'ST_SetSRID(ST_MakePoint(?, ?), 4326)::geography',
    [90.4125, 23.8103]
  ),
  destinationLocationName: 'Airport B',
  destinationPoint: knex.raw(
    'ST_SetSRID(ST_MakePoint(?, ?), 4326)::geography',
    [91.4125, 24.8103]
  ),
  departure_time: new Date(),
  fare: 3500.0,
});
```

## Summary

PostGIS extends PostgreSQL into a spatial database, enabling native handling of geographic data such as points, distances, and spatial relationships. Understanding key concepts like geometry vs. geography, SRIDs, and functions like `ST_MakePoint`, `ST_SetSRID`, and `ST_DWithin` is essential to using PostGIS effectively.

By integrating PostGIS into your backend application, you gain:

* Precise location storage and querying
* Elimination of external geospatial services
* High performance for spatial queries directly in SQL

This makes PostGIS an invaluable tool for building modern location-aware backend services.
