# Przykład 1: Stacje pogodowe 50km od punktu

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

# Przykład 2: Analiza przestępczości w Chicago

punktowo na mapie:
```
SELECT
 primary_type,
 ST_GEOGPOINT(longitude,latitude) AS point
FROM
 `bigquery-public-data.chicago_crime.crime`
WHERE
 longitude IS NOT NULL
```

obszary i granice dzielnic (kody pocztowe) w Chicago:
```
SELECT
 zip_code,
 county,
 city,
 zip_code_geom
FROM
 `bigquery-public-data.geo_us_boundaries.zip_codes`
WHERE
 city="Chicago city"
```

Spatial join:
```
SELECT
 crimes.primary_type as ctype,
 ST_GEOGPOINT(longitude,latitude) AS point,
 zip.zip_code as zipc
FROM
 `bigquery-public-data.chicago_crime.crime` as crimes
 JOIN `bigquery-public-data.geo_us_boundaries.zip_codes` as zip
ON
 ST_Within(ST_GEOGPOINT(longitude,latitude),zip.zip_code_geom)
```

Analityka - ilość przestępstw per kod pocztowy:
```
WITH
  SJOIN AS (
  SELECT
    COUNT(crimes.primary_type) AS counts,
    -- ST_GEOGPOINT(longitude,latitude) AS point,
    zip.zip_code AS zipcode
  FROM
    `bigquery-public-data.chicago_crime.crime` AS crimes
  JOIN
    `bigquery-public-data.geo_us_boundaries.zip_codes` AS zip
  ON
    ST_WITHIN(ST_GEOGPOINT(longitude,latitude),zip.zip_code_geom)
  GROUP BY
    zip.zip_code)
SELECT
  zip_code,
  counts,
  zip_code_geom
FROM
  SJOIN AS SJOINED
JOIN
  `bigquery-public-data.geo_us_boundaries.zip_codes` AS boundaries
ON
  sjoined.zipcode = boundaries.zip_code
```

# Przykład 3: Wizualizacja w Looker Studio
Looker Studio -> BQ -> Custom Query:
```
select
    ct.state_fips_code,
    ct.county_fips_code,
    c.county_name,
    ct.tract_ce,
    ct.geo_id,
    ct.tract_name,
    ct.lsad_name,
    ct.internal_point_lat,
    ct.internal_point_lon,
    ct.internal_point_geo,
    ct.tract_geom,
    acs.total_pop,
    acs.households,
    acs.male_pop,
    acs.female_pop,
    acs.median_age,
    acs.median_income,
    acs.income_per_capita,
    acs.gini_index,
    acs.owner_occupied_housing_units_median_value,
    acs.median_rent,
    acs.percent_income_spent_on_rent,
from `bigquery-public-data.geo_census_tracts.census_tracts_washington` ct
left join `bigquery-public-data.geo_us_boundaries.counties` c
    on (ct.state_fips_code || ct.county_fips_code) = c.geo_id
left join `bigquery-public-data.census_bureau_acs.censustract_2018_5yr` acs
    on ct.geo_id = acs.geo_id
```
