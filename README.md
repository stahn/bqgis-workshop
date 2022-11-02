# Stacje pogodowe 50km od punktu

```
  #standardSQL
  WITH params AS (
    SELECT ST_GeogPoint(-122.021,37.406) AS center, 50 AS maxdist_km ),
  distance_from_center AS (
    SELECT
      id,
      name,
      ST_GeogPoint(longitude, latitude) AS loc,
      ST_Distance(ST_GeogPoint(longitude, latitude), params.center) AS dist_meters
    FROM
      `bigquery-public-data.ghcn_d.ghcnd_stations`, params
    WHERE ST_DWithin(ST_GeogPoint(longitude, latitude), params.center, params.maxdist_km*1000)

  )
  SELECT * from distance_from_center
```
