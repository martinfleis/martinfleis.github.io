---
title: "Introducing Dask-GeoPandas for scalable spatial analysis in Python"
date: "2022-03-31"
tags:
  - "geography"
  - "python"
---

Using Python for data science is usually a great experience, but if you've ever worked with [pandas](http://pandas.pydata.org) or [GeoPandas](https://geopandas.org/), you may have noticed that they use only a single core of your processor. Especially on larger machines, that is a bit of a sad situation.

Developers came up with many solutions to scale pandas, but the one that seems to take the lead is Dask. Dask (specifically `dask.dataframe` as Dask can do much more) [creates a partitioned data frame](https://docs.dask.org/en/latest/dataframe.html), where each partition is a single `pandas.DataFrame`. Each of them can be then processed in parallel and combined when necessary. On top of that, the whole pipeline can be scaled to a cluster of machines and can deal with out-of-core computation, i.e. with datasets that do not fit the memory.

Today, we announce the release of Dask-GeoPandas 0.1.0, a new Python package that extends `dask.dataframe` in the same way GeoPandas extends pandas, bringing the support for geospatial data to Dask. That means geometry columns and spatial operations but also spatial partitioning, ensuring that geometries that are close in space are within the same partition, necessary for efficient spatial indexing.

The project has been in development for quite some time. [The original exploration](https://github.com/mrocklin/dask-geopandas) of bridging Dask and GeoPandas started almost 5 years ago by Matt Rocklin, the author of Dask. Later, in 2020, Julia Signell revised the idea and created the foundations of the current project. Since then, GeoPandas maintainers have taken over and led the recent development.

What is awesome about Dask-GeoPandas? First, you can do your spatial analysis in parallel, making sure all available resources are used (no more sad idle cores!), turning your workflow into faster and more efficient ones. You can also use Dask-GeoPandas to process data that do not fit your machine's memory as Dask comes with a support of out-of-core computation. Finally, you can distribute the work across many machines in a cluster. And all that with almost the same familiar GeoPandas APIs.

The latest evolution of underlying libraries powering GeoPandas ensures that it is efficient in terms of utilisation of resources but also performant within each partition. For example, unlike GeoPandas, where the use of [`pygeos`](https://pygeos.readthedocs.io/en/stable/), a new vectorised interface to GEOS is [optional](https://geopandas.org/en/stable/getting_started/install.html#using-the-optional-pygeos-dependency), Dask-GeoPandas requires it. Similarly, it depends on [`pyogrio`](https://pyogrio.readthedocs.io/en/latest/), a vectorised interface to GDAL, to read geospatial file formats.

At this moment, Dask-GeoPandas can do a lot of what GeoPandas can, with some limitations. When your code involves individual geometries, without assessing a relationship between them (like computing a centroid or area), you should be able to use it directly. When you need to work out some relationships, you can try (still a bit limited) `sjoin` or make use of spatial partitions and spatial indexing.

But not everything is ready. For example, overlapping computation needed for use cases like accessibility or K-nearest neighbour analyses is not yet implemented, PostGIS IO is not done, and some overlay operations are implemented only partially (`sjoin`) or not at all (`overlay`, `sjoin_nearest`). But the 0.1.0 release is just a start.

You can try it yourself, installing via conda (or mamba) or from PyPI (but see the [instructions](https://dask-geopandas.readthedocs.io/en/latest/installation.html), GeoPandas can be tricky to install using pip).

```python
mamba install dask-geopandas
```

```
pip install dask-geopandas
```

The best starting point to learn how Dask-GeoPandas works is [the documentation](https://dask-geopandas.readthedocs.io/), but this is the gist:

```python
import geopandas
import dask_geopandas

df = geopandas.read_file(
    geopandas.datasets.get_path("naturalearth_lowres")
)
dask_df = dask_geopandas.from_geopandas(df, npartitions=8)

dask_df.geometry.area.compute()
```

The code creates a `dask_geopandas.GeoDataFrame` with 8 partitions because I have 8 cores and compute each polygon's area in parallel, giving almost 8x speedup compared to the vanilla GeoPandas.

You can also check my [latest post](https://martinfleischmann.net/dask-geopandas-vs-postgis-vs-gpu-performance-and-spatial-joins/) comparing Dask-GeoPandas performance on a large spatial join with PostGIS and cuSpatial (GPU) implementations.

If you want to help, have questions or ideas, you are always welcome. Just head over to [Github](https://github.com/geopandas/dask-geopandas) or [Gitter](https://gitter.im/geopandas/geopandas) and say hi!
