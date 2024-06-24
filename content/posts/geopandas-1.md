---
title: "GeoPandas 1.0 is out!"
date: "2024-06-24"
tags:
  - "community"
  - "python"
  - "geopandas"
---

We have released [GeoPandas
1.0](https://github.com/geopandas/geopandas/releases/tag/v1.0.0)! Yes, I am excited and
a bit relieved as it took a bit longer than expected. Anyway, it's out and we're waiting
to hear what we broke 🙃.

It is a major milestone for GeoPandas, not only in a semantic sense, but it literally
closes a long development cycle. If you have been following the ecosystem for a while,
you might know the story, but it is worth refreshing your memories. So, very briefly:

Originally (by that I mean 10 years ago when Kelsey Jordahl and a few others put
together the first versions), a typical geometric operation on a GeoPandas object was an
internal `for` loop over an array of scalar
[Shapely](https://github.com/shapely/shapely) geometries. This was slow and there was no
way to speed it up given the design of Shapely. So
[PyGEOS](https://github.com/pygeos/pygeos) was born - a new, fully vectorised connection
to GEOS. And boy, was PyGEOS fast. Of course, GeoPandas included support for PyGEOS, but
since it was considered a bit experimental, it was done __alongside__ Shapely. Later,
the community got together and PyGEOS was merged with Shapely to become Shapely 2.0.
This was an absolutely amazing decision from which everyone benefited, but GeoPandas,
which tends to provide long-term support for older versions of dependencies, had to
implement __third geometry engine__ support, exposing the same
[GEOS](https://libgeos.org) library. You can imagine the mess we got into. Every new
geometry method had to be implemented three times, once for Shapely 1.x, once for PyGEOS
and once for Shapely 2. The latter two were super similar, but there were minor
differences.

This is all gone now. Hurray! With the 1.0 release, we have removed all this complexity
and only support Shapely 2. While most users don't care and never noticed any of this,
simply because things _just_ worked, it is an important step for us.

Performance was also an issue with I/O. Scalar-based
[Fiona](https://github.com/Toblerity/Fiona) was replaced by vectorised
[PyOGRIO](https://github.com/geopandas/pyogrio), which now comes with GeoPandas by
default.

There is a long list of improvements in the [release
notes](https://github.com/geopandas/geopandas/releases/tag/v1.0.0). A notable one is API
parity with Shapely (all relevant functions are exposed as GeoPandas methods), but
there's much more. Have a look for yourself.

What comes next? We'll be complicating our lives again with another geometry engine.
This time not GEOS, but [S2](http://s2geometry.io), which supports spherical geometry
and operations on spheres. We're finally going to break the main limitation of GEOS,
which is restricted to operations on the Euclidean plane. When will this happen? Some
preliminary support may be ready by the end of the year, but knowing how things are
going with our promises, probably sometime next year.

We're also planning some other things, but you can check them out on the
[roadmap](https://geopandas.org/en/stable/about/roadmap.html).

I also wrote a [post](geopandas-1-0-alpha) earlier about the major API changes in
GeoPandas. If you are wondering what might break your code, this is the post to check.

Let's get updating!

```sh
pip install geopandas==1.0.0

conda install geopandas==1.0.0 -c conda-forge
```

PS: There will likely be bugs. Please [report
them](https://github.com/geopandas/geopandas/issues)! We can't fix what we're not aware
of.