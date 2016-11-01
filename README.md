
# 3dfier

![](https://dl.dropboxusercontent.com/s/tojiay8cmomu2v5/Delft_3dfier-3.png)


Takes 2D GIS datasets (eg topographical datasets) and "3dfies" them (as in "making them three-dimensional") by lifting every polygon to 3D.
The elevation is obtained from a point cloud (we support LAS/LAZ at this moment), and the semantics of every polygon is used to perform the lifting.
That is, water polygons are extruded to horizontal polygons, buildings to LOD1 blocks, roads as smooth surfaces, etc.
Every polygon is triangulated (constrained Delaunay triangulation) and the lifted polygons are "stitched" together so that one digital surface model (DSM) is constructed.
Our aim is to obtain one DSM that is error-free, ie no intersecting triangles, no holes (the surface is watertight), where buildings are integrated in the surface, etc.
This surface will then be used as input in simulation software for instance.

The lifting options can be configured in the YAML file (`myconfig.yml`) provided.
Any 2D input (which should be a planar partition) can be used as input, and each class must be mapped to one of the following:

  1. Building
  1. Terrain
  1. Road
  1. Water
  1. Forest
  1. Bridge
  1. Separation (used for concrete slabs used along canals for instance, surely very "Dutch")

It is possible to define new classes, although that would require a bit of programming.

Output is at this moment in either OBJ or CityGML (and CVS for buildings only, ie their ID and height (ground+roof) are output).
The ID of each polygon is preserved, and there is a 1-to-1 mapping between the input and the output. 

Notice that this version is very much a beta version, although it is in our opinion usable. 
If you use it, feedback is very much appreciated.

## LAS/LAZ Pointcloud

We expect the LAS/LAZ to be classified according to the ASPRS Standard LIDAR Point Classes v1.4 (Table 4.9 of this [PDF](http://www.asprs.org/wp-content/uploads/2010/12/LAS_1-4_R6.pdf)), and at a minimum these should be defined:

  - 0-1 : Created, never classified and/or Unclassified
  - 2 : Ground
  - 3-5: Vegetation

If the vegetation is not classified or not filtered out, then buildings will be taller and there will be artefacts in the terrain.

## Compiling

To build you'll normally do (from 3dfier root directory):

```
mkdir build && cd build
cmake ..
cmake .. 
make
```

Notice that cmake is indeed called *twice*, we have noticed that on some machines the compiler optimisation is activated only when you cmake twice.
Why that is we are not sure, but to be sure do it twice.
With the optimisation, the test dataset should take around 20s to produce; if more (>5min) then the optimisation is not activated properly.

The dependencies that are necessary (under Mac we suggest using [Homebrew](http://brew.sh)):

  1. LIBLAS *with* LASzip support (`brew install liblas --with-laszip`)
  1. GDAL (`brew install gdal`)
  1. Boost (`brew install boost`)
  1. CGAL (`brew install cgal`)
  4. yaml-cpp (`brew install yaml-cpp`)


## To run:

`$ ./3dfier myconfig.yml > output.obj`

In the folder `example_data` there is a small part of the [BGT datasets](http://www.kadaster.nl/web/Themas/Registraties/BGT.htm) (2D 1:1k topographic datasets of the Netherlands) and a part of the [AHN3 LIDAR dataset](https://www.pdok.nl/nl/ahn3-downloads) that can be used for testing. 
The resulting model (in OBJ) can be seen in `example_data/output/test_area.obj`




  
