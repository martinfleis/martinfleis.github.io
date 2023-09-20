---
title: "A note on Spatial Data Science across Languages, vol.1"
date: "2023-09-20"
tags:
  - "geography"
  - "python"
  - "R"
  - "Julia"
  - "spatial data science"
  - "SDSL"
---

I am sitting on a train back to Prague after two days of discussing tooling for spatial
data science available in the Python, R and Julia ecosystems, with occasional excursions
to the worlds of Rust, JavaScript or ESRI. I am coming back from the
[Spatial Data Science across Languages](https://r-spatial.org/sdsl/) (SDSL) workshop and
I'd like to share a few thoughts[^1] while they're fresh.

[^1]: I don't mean to write a comprehensive report. That may or may not come later.

## Different maturity of ecosystems

As a Python developer, I must admit that what the R-Spatial community managed to create is impressive and is in some aspects further that where we are. The most notable is the [support of
spherical geometries](https://r-spatial.org/r/2020/06/17/s2.html). Where `geopandas` warns that a computation of area or a distance in geographic (longitude, latitude) coordinates is incorrect (more on this behavior below), `{sf}` returns a correct value. The same support is on the `geopandas` roadmap but it will not be done by tomorrow.[^2] Same could be said about the extent of support of spatial statistics but that is the same as with any statistics. R was built for it.

[^2]: I you'd like to follow the development, it happens in the [`spherely` project](https://github.com/benbovy/spherely).

Julia, on the other hand, is a much younger language and a smaller
community, with a lot of implementations missing or being very crude, but that gives them a chance to avoid mistakes that were
made in R or Python before. I hope we shared enough of those in past two days :).

## Is Arrow the future?

One of the recurring topics was the Apache Arrow project and the [GeoArrow](https://github.com/geoarrow/geoarrow) specification for storing vector geometries in Arrow. It seems to me that, at least for certain use cases (typically larger datasets), Arrow may play an important role in the ecosystem. Check for example [this blog post](https://kylebarron.dev/blog/geoarrow-and-geoparquet-in-deck-gl) by Kyle Barron on usage of GeoArrow in deck.gl or [this one](https://dewey.dunnington.ca/post/2022/building-bridges-arrow-parquet-and-geospatial-computing/) by Dewey Dunnington on building bridges using Arrow. Everything about (Geo)Arrow is fresh, there's not a lot of implementation beyond proofs of a concept, but the project is surely worth following.

## Freedom vs. security

Here's my hot take. R-Spatial and (Geo)Python communities, while sharing fundamental values of [FOSS](https://en.wikipedia.org/wiki/Free_and_open-source_software), have a different approach to freedom and especially responsibility when it comes to writing software.

R ecosystem is putting the responsibility on developers and packaging. CRAN sounds great from a user's perspective, everything should _just work_. As a developer and a maintainer, I am glad I am not writing code in R. There seems to be a lack of transparency and
reproducibility of CRAN checks and compilations, restrictive rules on the composition of the package and its behavior and a very strict policy of removal when something breaks (the [Isoband incident](https://appsilon.com/cran-and-the-isoband-incident/) is a fascinating case). It is not necessarily a critique, I am sure it works for a user, but it illustrates the approach that seems to be adopted by the R-Spatial as well - don't let a user fail, make some paths forbidden, take care of units and types of data on behalf of a user. Again, not a critique, I am sure it works flawlessly with right use cases and is friendly to newcomers.

Python is a bit different, though. We feel that a user is responsible for their actions and that we should provide enough documentation but let them go beyond what we think is correct, if they want to. That is why `geopandas` does not raise an error when you try to measure area using geographic coordinates but gives a warning[^3]. We warned you, and now it is your responsibility to decide what to do about it. Similar approach of _responsibility is on a user not a provider_ is linked to PyPI which does not do nearly any checks of what you upload and to conda-forge which lets community directly interact with the (transparent) packaging ecosystem and change things when they do not seem to work optimally.

[^3]: `geopandas` will eventually return correct values, once the support of spherical geometries via `spherely` lands. Until then, the warning stays.

Can't tell which of the two is a better approach. Maybe bits of each would work the best in the end.

## What is next?

We'll see. There is now a [discussion forum](https://github.com/spatial-data-science/discuss), some ideas, and a plan to meet again in a year for SDSL vol.2. This time in Prague. See you there!
