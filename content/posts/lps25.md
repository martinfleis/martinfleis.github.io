---
title: "A deep dive into vector data cubes in Python"
date: "2025-06-23"
tags:
  - "community"
  - "python"
  - "talks"
  - "teaching"
---

Yesterday we kickstarted the ESA's Living Planet Symposium
with the a workshop on vector data cubes using Xvec package
built on top of Xarray, Shapely and GeoPandas. If you did
not manage to be there but are still interested, the
workshop material is available below. The workshop covered
 the concept of VDC
using both coordinate geometry and variable geometry with
applications (and a combination of the two) using real-world
data and use cases.

- [Workshop materials](https://github.com/martinfleis/lps25)

## Annotation

Data cubes, as structures for representing multi-dimensional data, are typically used for raster data. When we deal with vector data, thinking of points, polygons and the like, we tend to represent it as tabular data. However, when the nature of the vector dataset is multi-dimensional, this is not sufficient. This tutorial will provide a deep dive into the concept of vector data cubes, an extension of the generic data cube model to support vector geometries either as coordinates along one or more dimensions or as variables within the cube itself. You will learn how to create such objects in Python using Xarray and Xvec. The tutorial is organised along a series of applied use cases that show different applications of vector data cubes. Starting with the one resulting from zonal statistics applied to a multidimensional raster cube, we will cover spatial subsetting, plotting, CRS transformations, constructive operations and I/O. Using the cube that captures origin-destination data, we will explore the application of multiple dimensions supported by vector geometry. Finally, the use case of time-evolving geometry will demonstrate the vector data cube composed of geometries both as coordinates along the dimensions and as variables in the cube. This includes an introduction to the concept of summary geometry supported by spatial masking and the more complex case of zonal statistics based on time-evolving geometry.
