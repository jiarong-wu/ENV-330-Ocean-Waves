This document contains the steps to set up the tsunami case using Basilisk (given that Basilisk is properly installed).

# Table of contents
1. [Introduction](#introduction)
2. [Terrain setup](#paragraph1)
3. [Fault models](#paragraph2)
4. [Compile and running the simulations](#running)
5. [Outputs](#outputs)
    1. [Comparison with gauge data](#gauge)
6. [Reference](#reference)

## Introduction <a name="introduction"></a>
The solver uses spherical coordinates and the Saint-Venant solver, together with (dynamic) terrain reconstruction. We can use different terrains and fault setups to model different realistic tsunamis. We provide two examples, one modeled after the 2004 Indian ocean tsunami, the other modeled after the 2011 Tohoku tsunami. The basic solver and code structures are the same, but the terrain and fault setups are slightly different.

The following setup steps have been tested only on the tiger cluster.

## Terrain setup <a name="paragraph1"></a>
One important information we need to set up the simulation is the bathymetry. There are two major databases that we can fetch the bathymetry data from, [ETOPO](https://www.ncei.noaa.gov/products/etopo-global-relief-model) and [SRTM](https://www.usgs.gov/centers/eros/science/usgs-eros-archive-digital-elevation-shuttle-radar-topography-mission-srtm). (What's the difference? Resolution?)

**The terrain files have been prepared in advance for you; download from [here](https://drive.google.com/drive/folders/1bUBvUA6-bmJLVJDFnRKHl1B_VCOzAT4V?usp=sharing).** See [this doc](https://github.com/jiarong-wu/ENV-330-Ocean-Waves/blob/main/terrain_prep.md) if you are curious about where they come from.
 
## The fault model <a name="paragraph2"></a>
A fault is a fracture or zone of fractures between two blocks of rock. Faults allow the blocks to move relative to each other. When the movement occurs rapidly, it forms an earthquake. The faults that trigger the tsunami are modeled using the [Okada model](http://basilisk.fr/src/okada.h). There can be several faults at different locations that shape the initial surface elevation profile together.

For the Indian ocean example, there is only one fault used to initialize the sea surface elevation (see the source code for its location and strength).
For the Tohoku example, the fault information is obtained through seismic inversion of the actual earthquake. There are several sub-faults and they are listed in [this header file](http://basilisk.fr/src/examples/tohoku/faults.h), which is already included and nothing additional needs to be done.

## Compile and run the code <a name="running"></a>
Compile using the makefile `make -f Makefile.parallel.tiger OUTPUT=SOURCE_CODE_NAME_WITHOUT_c`. Run using a slurm script (keep writing this part).

The `tohoku2011` case took about 300 CPU hrs (64 cores 4 hrs) at refinement LEVEL=13.

## Outputs <a name="outputs"></a>
The outputs are the following:
1. Movies of surface elevation $\eta$
2. Movies of refinement level
3. Flooding info in text files `flooding`
4. Google Earth KML file (for the tsunami case)
5. ...

### Comparison with gauge data <a name="gauge"></a>
For the Tohoku case, the gauge locations are included in [this header file](http://basilisk.fr/src/examples/tohoku/gauges.h). For the Indian ocean case, the gauge locations are included in the source code directly.

We (or the students) still need to find the gauge data.

## Remaining questions
1. The `conserve_elevation` function tells the Saint-Venant solver to conserve water surface elevation rather than volume when adapting the mesh. This is important for tsunamis since most of the domain will be close to “lake-at-rest” balance.

## Reference <a name="reference"></a>
[The Basilisk page of the 2011 Tohoku tsunami example](http://basilisk.fr/src/examples/tohoku.c) </br>
[The Basilisk page of the 2004 Indian Ocean tsunami example](http://basilisk.fr/src/examples/tsunami.c) </br>
[The Gerris page](https://gfs.sourceforge.net/wiki/index.php/Xyz2kdt) (Gerris is the predecessor of Basilisk.) </br>
[Popinet 2011](https://link.springer.com/article/10.1007/s10236-011-0438-z) </br>
[Popinet 2020](https://www.sciencedirect.com/science/article/pii/S0021999120303831?via%3Dihub) </br>


