---
title: "Getting the most out of GeoPandas 1.0"
date: "2024-05-27"
tags:
  - "community"
  - "python"
  - "talks"
  - "teaching"
  - "geopandas"
---

After 10 years since the first release, GeoPandas reached version 1.0. This workshop will showcase how to get the most out of the recent enhancements and develop a code ready for 2024 and beyond.

Workshop resources are available on [Github](https://github.com/martinfleis/geopandas-one-workshop).

## Annotation

GeoPandas is one of the core components of the GeoPython ecosystem, providing the critical infrastructure to work with vector spatial data, usually reserved for the context of GIS, in Python. Although central to many applications and supporting many users, GeoPandas was technically not considered stable (even though it was practically for a long time). With GeoPandas 1.0, this is no longer the case, and the project has reached maturity and stability we can build upon in years to come.

The efforts towards the 1.0 release were split into two main components - performance and features. While the performance gains coming from the complete integration of Shapely 2.0 used for geometry operations and pyogrio used to read and write data to disk should work out of the box, new features deserve a bit of attention. This workshop will take a subset of new functionality that has made it to the GeoPandas within the last year and show what is possible, what are the recommended patterns, and how to combine different components into an efficient workflow. Participants will learn how to make use of newly exposed shapely functionality and how it differs from the shapely implementation, how to use sampling methods to create dot density maps or to take advantage of efficient serialisation of geometries via GeoArrow.