---
title: "Scaling up vector analysis with Dask-GeoPandas"
date: "2022-06-22"
tags:
  - "community"
  - "python"
  - "talks"
  - "teaching"
  - "geopandas"
---
The workshop organised during the GeoPython 2022 together with Joris van den Bossche introduces the Dask-GeoPandas library and walks you through its key components, allowing you to take a GeoPandas workflow and run it in parallel, out-of-core and even distributed on a remote cluster.

Workshop resources are available on [Github](https://github.com/martinfleis/dask-geopandas-tutorial).

## Annotation

The geospatial Python ecosystem provides a nice set of tools for working with vector data, including Shapely for geometry operations and GeoPandas to work with tabular data (and many other packages for IO, visualization, domain specific processing, …). One of the limitations of those core tools is a sub-optimal performance and limited scaling possibilities.

The PyData ecosystem is increasingly embracing Dask as a tool of choice when the scale of the task goes beyond capabilities of Pandas or Numpy. Over the last years, effort has been put in improving the performance through vectorized interfaces to GEOS, the underlying C library of Shapely. In turn, that enables releasing the GIL and makes the Dask - GeoPandas combination more interesting.

Since GeoPandas is an extension to the pandas DataFrame, the same way how Dask scales pandas can be applied on GeoPandas as well. Initial effort to build a bridge between Dask and GeoPandas is currently taking the shape of the dask-geopandas library.

This workshop provides an introduction to dask-geopandas and walks you through the key aspects of the library. We will touch the specificity of vector geospatial data when it comes to parallelisation, cover use cases where dask-geopandas provides major benefits as well as those, where it currently struggles. You will learn how to turn your GeoPandas workflow to Dask-GeoPandas one and what are the rules of thumb when doing so.
