# VisIT Elements

The two main VisIt elements are the databases and the plots. The databases are the files that
contain the data that we aim to visualize with VisIt. The plots are the visualizations we create with
the data. In this section, we introduce and provide examples of different databases and plots, as well
as other relevant aspects of VisIt, that we will later use for our numerical relativity visualizations.

## Databases

Databases are files or collections of files that are loaded into VisIt for visualization. In numerical
relativity, databases usually contain 3D spatial data at multiple time steps. However, VisIt also
supports 1D and 2D databases. VisIt has an extensive list of file formats that it supports. When
these file are loaded—provided that they are generated and formatted properly—VisIt automatically
detects the type of data that the database contains (e.g. scalar or vector, 2D or 3D). In this section,
we will describe a few file types that are commonly used in numerical relativity.

### HDF5 Data

HDF5 (Hierarchical Data Format) data [8] is a file format that allows developers to store and
manipulate large amounts of data. In numerical relativity, outputting the variables we are interested
in visualizing often requires terabytes of data, which is why we use the HDF5 file format for the
simulation output. Additionally, HDF5 has multi-threading support, which allows us to leverage
parallel processing to speed up the scientific visualization process.
HDF5 organizes data in two ways: groups and datasets. Groups can be thought of as links
between data objects, such as a directory file structure that connects folders together. Datasets are
packages of raw data that the file contains with additional information describing the data itself.
The HDF5 data that the Illinois GRMHD code [4] outputs for visualization, stores datasets.
Since outputting HDF5 data is done by the evolution code, we will not discuss how to generate
HDF5 files. However, we will go over how to view the contents and structure of HDF5 files. We
provide a sample HDF5 file that can be found at VisIt-Guide/sec_4/Bx.file_0.h5. This file is
just 1 out of 128 separate files that together contain data for the x component of the magnetic field
at a couple of times.
While working with HDF5 data, there are two command line tools that are especially useful to
know, h5ls and h5dump, which can be used to view the h5 data. The command h5dump displays the
content of the HDF5 file. When executed with no options, this command displays (dumps) all of
the raw data in the terminal. Several useful options that can allow the user to extract information
from an HDF5 file more effectively are listed below.

<ol>
    <li><strong>Viewing file contents:</strong> With the -n or -contents flag after the h5dump command (Fig. 12),
we can list all of the data stored in the h5 file, which can be especially useful when trying to
pick up minute pieces of information quickly from the file. Additionally, we are able to view
the headers or titles of the data with corresponding information through the -H or -header
flag after the h5dump command (Fig. 13), which can be used to extract specific details about
the datasets themselves, such as their attributes and data types.

**insert figure**

Similarly, the h5ls command can be used to display data stored within the groups and datasets
recursively. The output of h5ls is often more streamlined than the output of h5dump. When
the -v flag is used with the h5ls command, the different properties of the dataset such as the
storage capacity, file contents, and attributes are shown, providing a more detailed snapshot
of the HDF5 file.
**insert figure here**

</li>
    <li><b>Viewing datasets/groups:</b>If we want to view the datasets or groups in an HDF5 file, we can use the -d "Dataset Name" or -g "Group Name" flag with the h5dump command. These flags allow us to analyze specific components of the data that we are interested as well as the
raw data stored within the dataset.
**insert figure**
Some additional flags that might prove useful in combination with the h5dump command CLI
tool might be the -A 0 flag, which suppresses attributes when displaying data from the h5 file
and simplifies the output quite a bit. We could also index specific datasets or groups that
have specific additions through the -N PATH flag, which matches any object within the h5 file
the another object or attribute at a specific path.
Within the h5ls CLI tool, we can index specific datasets by simply adding the path of the
dataset within the h5 file. The -d or -data flag can be used in combination with the h5ls
command in order to display the corresponding data within the specific dataset of the h5 file.

**insert figure**

</li>
</ol>

### VTK Files

Another important data type used in these simulations are Visualization ToolKit (VTK) files. VTK
files are ASCII or binary files that can easily be generated using Python scripts or similar methods.
The VTK file format is versatile and allows users to convert a database to be plotted in VisIt. VTK
files can store scalar, vector, or tensor data on 2D or 3D grids. We break the VTK file format down
into six sections.

<ol>
   <li><u>Header:</u>The header is the first line of a VTK file and contains the file version and identifier.
The header is written as # vtk DataFile Version x.x where the x.x is the version number.</li> 
   <li><u>Title:</u>The title is a string limited to 256 characters. The title is not used by VisIt but should
be a description of the data contained in the VTK file.</li> 
   <li><u>Data Type:</u>The data type can either be BINARY or ASCII. Reading and writing binary data is
quicker. However, binary data is only portable across different machines if the machines have
the same byte ordering. If the byte ordering between machines differed, then binary VTK
files need to be preprocessed before they are able to be read by VisIt. Because we don’t work
with large datasets when using VTK files, we use ASCII data because of its flexibility and
simplicity.</li> 
   <li><u>Grid Structure:</u>This section begins with a line containing DATASET followed by a keyword that
describes the structure of the grid. The options are STRUCTURED_POINTS, STRUCTURED_GRID,
UNSTRUCTURED_GRID, POLYDATA, STRUCTURED_POINTS, RECTILINEAR_GRID, or FIELD. Depend-
ing on which option is used, there are additional lines that are required to specify the grid
(e.g. the dimensions and spacing). Detailed information can be found in the VTK file format
documentation:

<a href="https://examples.vtk.org/site/VTKFileFormats/"><code>https://examples.vtk.org/site/VTKFileFormats/</code></a>
</li> 
   <li><u>Data Set Attributes:</u>This section begins with either POINT_DATA or CELL_DATA followed by an
integer specifying the number of points or cells of the data. The next line specifies the type of
data using one of the keywords SCALARS, VECTORS, or TENSORS. Following this keyword are
the dataName and dataType. The dataName is a short string-identifier and is the variable
that shows up in VisIt when a VTK file is loaded as a database. The dataType—which differs
from the earlier ASCII vs binary data type—describes the type of numerical data (e.g. int,
float, double).</li> 
   <li><u>Data:</u>Following the first five sections, data is output. The structure of the data will depend
on the grid chosen in step 4 as well as the type of data (scalar, vector, tensor).</li> 
</ol>

In Code Lst. 4.1.2, we show an example of a truncated VTK data file that contains vector field
data.

**insert code listing**
Here, the header is # vtk DataFile Version 2.0, the title is spin_vector, and the data type
is ASCII. The grid structure is DATASET STRUCTURED_POINTS, which is a 1D, 2D, or 3D grid with
evenly spaced grid points. To fully specify the grid structure of STRUCTURED_POINTS, we must add the
lines DIMENSIONS, ORIGIN, and SPACING. By setting DIMENSIONS to 3 3 3, we choose a 3×3×3 grid
(which also implicitly chooses 3D). By setting ORIGIN to -30 -30 -30 and SPACING to 30 30 30,
we set the grid to be [−30, 30] × [−30, 30] × [−30, 30] where the coordinates can take the values
x, y, z ∈ {−30, 0, 30}. The ORIGIN specifies the corner of the grid closest to the −x, −y, −z quadrant
and the SPACING specifies the distance between grid points. In the data set attributes section, we
have the lines POINT_DATA 27 and VECTORS spinvec float. The first line communicates that we
are specifying data on the grid points and that we have a total of 33 = 27 grid points. The second line
specifies that our data is vector data (i.e. we need to specify a vector at each point on the grid). We
set the dataName to spinvec and the dataType to float. After this section is the actual data, which
is truncated but should contain 27 lines of 3 floats separated by spaces. With STRUCTURED_POINTS,
the data is ordered with x increasing fastest, then y, then z. So the first four lines of data would
correspond to the points (−30, −30, −30), (−30, −30, 0), (−30, −30, 30), (−30, 0, −30).

## Plots and Operators
Once databases are loaded into VisIt, plots are created using the data to produce a visualization.
Operators can be applied to the plots to modify the resulting information. Plots and operators both
have settings that change different aspects of the visualization ranging from aesthetics to compute
time and numerical accuracy. It is important to understand how different plots and operators are used and how their settings affect the resulting visualization.
### Volume Rendering
Volume rendering, which is employed by the Volume plot, is a way of visualizing 3D scalar fields such
as the fluid density. With volume rendering, different values in the scalar field are mapped to different
colors and opacities. This mapping allows certain values to be emphasized. Additionally, because
the mapping is continuous, volume rendering can create high-quality plots that can incorporate all
the input data in the final rendered image with proper configuration. Since GRMHD simulations
involve a fluid density (which is a scalar field) that is used to model neutron stars and accretion
disks, volume rendering is an important tool for visualization in numerical relativity.
Volume rendering within VisIt can be broken into three distinct methods: Splatting, 3D
Texturing, and Ray Casting, however Ray Casting is essentially the only method that is used within
scientific visualizations due to its precision. For both Splatting and 3D Texturing, the volume
rendering methods are hardware accelerated, which means that they leverage the graphics card to
produce the image. Unfortunately, this means that the dataset must fit onto the graphics card,
so, as the dataset becomes larger, the data will have to be resampled and fed into the graphics
card, resulting in a loss of quality within the images. On the other hand, Ray Casting is a more
computationally intensive volume rendering technique and uses the entire dataset; however, the
computational load can be parallelized to produce high quality images in a timely manner.
Ray casting volume rendering works in three steps. First, the scalar database must be loaded
into VisIt. From here, we specify a camera/viewing angle (think a camera positioned at a point
in 3D space pointed at the data). From this camera angle, rays are shot out from each pixel of
the camera and intersect with the volume data’s cells. After the rays interact with the data, VisIt
assigns a color and opacity to each pixel based on the “transfer function”, which is the configuration
settings set by the user that controls exactly how VisIt decides the pixel’s colors and opacity.
An example of ray casting volume rendering used to visualize a magnetized accretion disk that
surrounds a binary black hole system can be seen in Fig. 14. For the remainder of the guide, we
will refer to ray casting volume rendering as simply volume rendering.
For our visualizations, we find that the following Volume plot settings are the most important.
<ol>
   <li><u><code>colorControlPointList</code>: colorbar used for plot</u>
The colorbar is created through a series of color points placed at different relative density
values. The colorControlPoint field takes in the RGBA color value and the density data
distribution percentage. Unlike isosurface rendering, rendering automatically fills in the
missing color values. Thus, we don’t need to create an elaborate color table with too many
intermediate colors.
**insert figure** 
</li> 
   <li><u><code>freeformOpacity</code>: Opacity map used for plot</u>
   Instead of filling up the colorbar with different color palettes, we are able to exercise fine
control over what density values are displayed through the 256-bit freeform array.The colorbar is projected over this array with each value corresponding to a unique density distribution
percentage. A 0 corresponds to complete transparency whereas 256 corresponds to complete
opacity. In terms of array positioning, the first index corresponds to the lowest density
value (this is the outermost layer of the disk/neutron star). Similarly, the last index position
corresponds to the highest density value. By assigning different opacity values to different
array positions we are able to “activate” different parts of the volume plot (for example we
can emphasize the higher densities in an accretion disk by increasing the opacity of the higher
density values relative to lower densities).
</li>
   <li><u><code>opacityAttenuation</code>: Adjusts overall transparency</u>
While the freeform array helps control the finer transparency details, we can also increase
or decrease the overall transparency of the volume plot. This is often done at the end after
constructing the colorbar and opacity array (for example we decrease the opacityAttenuation
when we want to be able to see a black hole that is obstructed by the accretion disk).
</li> 
    <li><u><code>colorVarMin/Max:</code>: Changes limits on colorbar</u>
If we want to only visualize a certain range of the data, we can explicitly specify the minimum
and maximum density values after setting useColorVarMin/Max to true. In volume plots of the
density, we actually plot the logarithm of a normalized rest-mass density log(ρ0 /ρmax
). Because
0
of this, we always set colorVarMax to 0. For the accretion disk, we set colorVarMin to −4.
This range of (−4, 0) is reflected by the colorbar in Fig. 17. Note that changes in min and max
values should be followed up with corresponding changes in the opacityAttenuation array since opacityAttenuation assigns opacity relative to the range specified by colorVarMax/Min.
For instance, if the first half of opacityAttenuation is filled with zeros, then the densities
with log(ρ0 /ρmax
) ∈ (−4, −2) will be transparent. If we increase colorVarMin to −2 while
0
keeping opacityAttenuation the same, then the densities with log(ρ0 /ρmax
) ∈ (−2, −1)
0
will be transparent. Note that values outside the range (colorVarMin,colorVarMax) are
transparent.

</li> 
   <li><code>samplesPerRay:</code>: Adjusts smoothness and resolution of plot</code> 
As mentioned before, ray casting creates the plot by shooting rays and sampling the points
in its path. By increasing the number of samples that are being cast through the data for
each ray, we are able to improve the overall quality of the image. Having too few sample
points along a ray gives rise to sampling artifacts such as rings or voids and decreases the
overall ”smoothness”. However, sampling more points takes longer to render. We observe a
1:1 correlation between changes in image rendering time and the number of samples per ray.</li> 
</ol>

To get a better grasp of the volume rendering process, we will be visualizing a 3D model of
density VTK file through both the VisIt GUI and then through the VisIt CLI. This file containing
this 3D data can be found at <a href="https://github.com/tsokaros/Illinois-NR-VisIt-Guide/blob/main/sec_4/sample_density.vtk"><code>VisIt-Guide/sec 4/sample density.vtk</code></a>


### Isosurface Rendering

### Vector Fields

### Streamline Intergration

## Expressions

## Exporting Attributes


