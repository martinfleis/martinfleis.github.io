---
title: "GeoPandas 1.0 is coming. What will change?"
date: "2024-04-13"
tags:
  - "community"
  - "python"
  - "geopandas"
---

The GeoPandas team is racing towards the 1.0 release, nearly 10 years after 0.1 made it to PyPI. As with any major release, it brings some changes. This post highlights those I feel are the most important and invites you to test the `1.0.0-alpha1` and later `rc` versions before we let the stable version fly to the world.

## Please test!

As of today, GeoPandas 1.0 is out as a pre-release `1.0.0-alpha1`. If you read it during April or May 2024, please install the pre-release and test your code against it. If anything looks odd, please [let us know](https://github.com/geopandas/geopandas/issues/new/choose)!

How to install it? Either from PyPI with the `--pre` flag (add `-U` if you want to update an existing version):

```sh
pip install geopandas --pre -U
```

Or from Github, if you want the latest, greatest version:

```sh
pip install git+https://github.com/geopandas/geopandas.git
```

Both will contain the changes outlined below.

## So, what will change?

### Geometry engines

GeoPandas has supported either Shapely < 2, PyGEOS or Shapely > 2 as a geometry engine for quite some time. You could have installed any of them, and it just worked (with some minor caveats). That ends with 1.0, which strictly requires Shapely 2.0 or newer. If your code depends on old Shapely, for whichever reason, or PyGEOS, it is time to upgrade. If you were relying on PyGEOS, we have a [migration guide](https://geopandas.org/en/stable/docs/user_guide/pygeos_to_shapely.html) for you.

Note that if you are using GeoPandas methods to perform geometry operations, you should not notice any difference compared to GeoPandas 0.14 if you had Shapely 2 installed alongside. If you had Shapely < 2 installed, everything would be much faster.

### I/O engines

The land of geospatial I/O (reading and writing files) is dominated by [GDAL](https://gdal.org), and GeoPandas does not deviate from that. Since the early days, our I/O was based on GDAL exposed through the [Fiona](https://fiona.readthedocs.io) package. A few versions ago, we introduced an optional replacement of Fiona called [Pyogrio](https://pyogrio.readthedocs.io), which is built specifically for the type of use case GeoPandas has and, as a result, is quite a bit faster than Fiona[^1]. And now, we have made Pyogrio the default. When installing GeoPandas, it no longer pulls Fiona, so if you depend on some of its functionality directly, you will need to install it manually. If you were used to using Fiona to list layers in a GeoPackage or another file format that supports multiple layers, you can now use a built-in function based on Pyogrio instead:

```py
# list layers of a GeoPackage
geopandas.list_layers("middle_earth.gpkg")
```

We tried to ensure that the behaviour of the Pyogrio engine matches the one of Fiona as much as we could, but there may be differences. If you notice some that look strange, please [let us know](https://github.com/geopandas/pyogrio/issues). And if you prefer to use Fiona for some reason, you can always specify the `engine="fiona"` keyword in `geopandas.read_file` and `GeoDataFrame.to_file` or change the default globally using:

```py
geopandas.options.io_engine = "fiona"
```

See the [migration guide](https://geopandas.org/en/latest/docs/user_guide/fiona_to_pyogrio.html) for some details.

### Datasets are gone

The `geopandas.datasets` module used to contain example data from [Natural Earth](https://www.naturalearthdata.com) and the New York Boroughs map. However, the module has been deprecated, and we have removed it in 1.0. I know that many of you used the `"naturalearth_lowres"` dataset either in your work or teaching materials. While the removal will likely cause some friction, it is better for the health of the GeoPandas project (and mine) not to deal with contested political boundaries.

If you are looking for some real-world data to showcase whatever you like to talk about, check the [geodatasets](https://geodatasets.readthedocs.io/en/stable/) package. It even has the good old `"nybb"` dataset:

```py
from geodatasets import get_path

ny_boroughs = geopandas.read_file(get_path("nybb"))
```

### Deprecations

Many deprecations we warn about since 0.14 or earlier are now enforced. See the list taken directly from the [changelog](https://github.com/geopandas/geopandas/blob/main/CHANGELOG.md):

- Removed deprecated functions `geopandas.io.read_file`, `geopandas.io.to_file` and `geopandas.io.sql.read_postgis`.`geopandas.read_file`, `geopandas.read_postgis` and the GeoDataFrame/GeoSeries `to_file(..)` method should be used instead.
- Removed deprecated `GeometryArray.data` property, `np.asarray(..)` or the `to_numpy()` method should be used instead.
- Removed deprecated `sindex.query_bulk` method, using `sindex.query` instead.
- Removed deprecated `sjoin` parameter `op`, `predicate` should be supplied instead.
- Removed deprecated GeoSeries/ GeoDataFrame methods `__xor__`, `__or__`, `__and__` and `__sub__`. Instead use methods `symmetric_difference`, `union`, `intersection` and `difference` respectively.
- Removed deprecated plotting functions `plot_polygon_collection`, `plot_linestring_collection` and `plot_point_collection`, use the GeoSeries/GeoDataFrame `.plot` method directly instead.
- Removed deprecated GeoSeries/GeoDataFrame `.plot` parameters `axes` and `colormap`, instead use `ax` and `cmap`respectively.

## New stuff

We also have a bunch of new functionality coming but not all will be available in `1.0.0-alpha1` pre-release. I will try to cover it once we release the stable 1.0 in another post.

[^1]: See [slides](https://jorisvandenbossche.github.io/talks/2022_GeoPython_geopandas/#32) on Pyogrio from Joris van den Bossche's talk at GeoPython 2022 for some comparison. Since then, certain operations are even faster with Pyogrio.
