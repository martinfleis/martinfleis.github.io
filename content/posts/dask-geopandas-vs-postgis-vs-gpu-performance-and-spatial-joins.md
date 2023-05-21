---
title: "Dask-GeoPandas vs PostGIS vs GPU: Performance and Spatial Joins"
date: "2022-03-10"
---

Paul Ramsey saw a spatial join done [using a GPU](https://medium.com/swlh/how-to-perform-fast-and-powerful-geospatial-data-analysis-with-gpu-48f16a168b10) and tried to [do the same with PostGIS](https://blog.crunchydata.com/blog/performance-and-spatial-joins), checking how fast that is compared to the GPU-based RAPIDS.AI solution. Since Paul used parallelisation in PostGIS, I got curious how fast [Dask-GeoPandas](https://github.com/geopandas/dask-geopandas) is on the same task.

So, I gave it a go.

```python
import download
import geopandas
import dask_geopandas
import dask.dataframe
from dask.distributed import Client, LocalCluster
```

Let's download the data using Paul's query, to ensure we work with the same CSV.

```bash
curl "https://phl.carto.com/api/v2/sql?filename=parking_violations&format=csv&skipfields=cartodb_id,the_geom,the_geom_webmercator&q=SELECT%20*%20FROM%20parking_violations%20WHERE%20issue_datetime%20%3E=%20%272012-01-01%27%20AND%20issue_datetime%20%3C%20%272017-12-31%27" > phl_parking.csv
```

And then download and unzip the neighbourhoods shapefile.

```python
download.download(
    "https://github.com/azavea/geo-data/raw/master/Neighborhoods_Philadelphia/Neighborhoods_Philadelphia.zip",
    "Neighborhoods_Philadelphia", 
    kind="zip"
)
```

Paul used a machine with 8 cores. Since I use a machine with 16 cores, I'll create a local cluster limited to 8 workers. That should be as close to Paul's machine as I can get without using some virtual one. Keep in mind that this distorts the benchmark as we use different processors with different performances. But the point here is to get a sense of how fast can Dask-based solution be compared to PostGIS and the original GPU code.

```python
client = Client(
    LocalCluster(
        n_workers=8, 
        threads_per_worker=1
    )
)
```

With Dask, we create the whole pipeline to create a task graph and then run it all, so we won't have the timings for individual steps, just the total one.

Read parking data CSV into a partitioned data frame (25MB per partition).

```python
ddf = dask.dataframe.read_csv(
    "phl_parking.csv", 
    blocksize=25e6, 
    assume_missing=True
)
```

Create point geometry and assign it to the data frame, creating `dask_geopandas.GeoDataFrame`.

```python
ddf = ddf.set_geometry(
    dask_geopandas.points_from_xy(
        ddf, 
        x="lon", 
        y="lat", 
        crs=4326
    )
)
```

Read neighbourhood polygons and reproject to EPSG:4326 (same as parking data).

```python
neigh = geopandas.read_file(
    "Neighborhoods_Philadelphia"
).to_crs(4326)
```

Create the spatial join.

```python
joined = dask_geopandas.sjoin(ddf, neigh, predicate="within")
```

Finally, let's compute the result.

```python
%%time
r = joined.compute()
```

Time on a local cluster with 8 workers and 1 thread per worker to pretend it is an 8-core CPU:

```
CPU times: user 9.34 s, sys: 2.09 s, total: 11.4 s
Wall time: 21.3 s
```

The complete pipeline took 21.3 seconds, including sending all data to a single process, in the end, to create a single partition joined GeoDataFrame. Usually, that is unnecessary as you work with the data directly in Dask. It does take a few seconds guessing from the Dask Dashboard.

Let's compare it to the PostGIS solution:

- Reading in the 9M records from CSV takes about **29 seconds**
- Making a second copy while creating a geometry column takes about **24 seconds**
- The final query running with 4 workers takes **24 seconds**

That gives us a total of **77 seconds** compared to **21 seconds** using Dask-GeoPandas. It's still slower than **13 seconds** using RAPIDS.AI although that covers only the join itself, not reading and creating geometry, so my sense is that it will be almost equal. One aspect that makes the difference between Dask and PostGIS is that our pipeline is parallelised at every step - reading the CSV, creating points, generating spatial index (that is done under the hood in `sjoin`), the actual join.

While Paul was using the 8-core machine, PostGIS actually utilised only 4 cores (I am not sure why). Let's try to run our code limited to 4 workers as well.

```
CPU times: user 9.53 s, sys: 2 s, total: 11.5 s
Wall time: 28.4 s
```

**28 seconds** is a bit slower than before, but still quite fast!

When comparing PostGIS and GPU solutions, Paul says

> Basically, it is very hard to beat a bespoke performance solution with a general-purpose tool. Yet, PostgreSQL/PostGIS comes within "good enough" range of a high end GPU solution, so that counts as a "win" to me.

At the moment, Dask-GeoPandas is somewhere between PostGIS and bespoke solutions. It does not offer as many functions as PostGIS, but it is designed as a general-purpose tool. So I would say that we are all winners here.

The notebook is available [here](https://gist.github.com/martinfleis/8200105480d777daa7bbf70492a8fbe3).

EDIT (Mar 24, 2022): See also Dewey Dunnington's [follow-up expanding the comparison to R.](https://dewey.dunnington.ca/post/2022/profiling-point-in-polygon-joins-in-r/)
