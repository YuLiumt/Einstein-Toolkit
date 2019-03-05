## Flesh parameters

```
# Flesh parameters
Cactus::<Flesh parameters> = <Value>
```

The default value is shown in square brackets, while curly braces show allowed parameter values.

| Value | Describe |
| ------------ | ------------- |
| Cactus::cctk_run_title | Description of this simulation [""] |
| Cactus::cctk_full_warnings | Give detailed information for each warning statement [yes] |
| Cactus::highlight_warning_messages | Highlight CCTK warning messages [yes] |
| Cactus::cctk_timer_output | Give timing information [off] {off, full} |
| Cactus::allow_mixeddim_gfs | Allow use of GFs from different dimensions [no] |
| Cactus::cctk_brief_output | Give only brief output [no] |
| Cactus::cctk_show_banners | Show any registered banners for the different thorns [yes] |
| Cactus::cctk_show_schedule | Print the scheduling tree to standard output [yes] |
| Cactus::cctk_strong_param_check | Die on parameter errors in CCTK_PARAMCHECK [yes] |
| Cactus::recovery_mode | How to behave when recovering from a checkpoint [strict] {strict, relaxed} |
| Cactus::info_format | Specifies the content and format of CCTK_INFO()/CCTK_VINFO messages. [basic] {"basic", "numeric time stamp", "human-readable time stamp", "full time stamp"} |

| Value | Describe |
| ------------ | ------------- |
| Cactus::terminate | Condition on which to terminate evolution loop [iteration] {never, iteration, time, runtime, any, all} |
| Cactus::cctk_final_time | Final time for evolution, overridden by cctk_itlast unless it is positive [-1.0] |
| Cactus::cctk_initial_time | Initial time for evolution [0.0] |
| Cactus::cctk_itlast | Final iteration number [10] |
| Cactus::max_runtime | Terminate evolution loop after a certain elapsed runtime (in minutes); set to zero to disable this termination condition [0] |
| Cactus::terminate_next | Terminate on next iteration ? [no] |

## Grid structure

### CoordBase

The `CoordBase` thorn provides a method of registering coordinate systems and their properties.

#### Specifying coordinate systems in the interface.ccl

```
CCTK_REAL SphericalVectorField TYPE=ARRAY DIM=2 TAGS=’COORDSYSTEM="sphere2d" TENSORTYPE="vector"’  
{  
  field_theta, field_phi  
}
```

#### Specifying the extent of the physical domain

CoordBase provides a way for specifying the extent of the simulation domain that is independent of the actual coordinate and symmetry thorns. This is necessary because the size of the physical domain is not necessarily the same as the size of the computational grid, which is usually enlarged by symmetry zones and/or boundary zones. The physical domain is characterised by the location of its lower and upper boundary and by its grid spacing. 

```
CoordBase::domainsize = <Value>
```

| Value | Describe |
| ------------ | ------------- |
| minmax | by the location of its lower and upper boundary. |
| extent | by its extent, i.e. its width. |
| spacing | by grid spacing and the number of grid cells. |

___

The domain specification uses the number of grid cells instead of the number of grid points because the latter can easily lead to one-off errors.

The domain size in each dimension is specificied in equivalent ways. For example, the x-dimension is specified by a set of some of the following parameters:

| Value | Describe |
| ------------ | ------------- |
| zero_origin_x | When the domain size is specified by extent or by spacing, then the origin (lower boundary) can either be located at $x = 0$, which leads to the domain $x \in[0, L]$ with the extent L, or the domain can be symmetric with respect to $x = 0$, which leads to $x \in[-L / 2, L / 2]$. |
| xmin and xmax | When the domain is specified by the location of its lower and upper boundary, then these specify the locations. |
| xextent | When the domain is specified by its extent, then this specifies the extent. |
| dx (real) and ncells_x (int) | When the domain is specified by grid spacing and the number of grid cells, then these specify the grid spacing and the number of grid cells. |

#### Specifying the location of the boundary points

`CoordBase` also provides a way for specifying the discretisation of the boundary. The specification does not depend on the resolution, so that it need not be adapted when the resolution is changed.

| Value | Describe |
| ------------ | ------------- |
| boundary_size_x_lower| the computational grid can have more than one boundary points. |
| boundary_internal_x_lower | speciﬁes whether the boundary points extend inwards at the lower x face. |
| boundary_staggered_x_lower | the boundary points should either be staggered about the physical boundary, or the last boundary point should be located exactly on the physical boundary. |
| boundary_shiftout_x_lower | shift the boundary points outwards (or inwards with negative values) by multiples of the grid spacing. | 

### [CartGrid3D](http://cactuscode.org/documentation/thorns/CactusBase-CartGrid3D.pdf)

`CartGrid3D` allows you to set up coordinates on a 3D Cartesian grid in a flexible manner.

#### Specifying the Grid Size, Range, and Spacing

`CartGrid3D` provides several different methods for setting up the integer grid size, floating-point grid spacing, and floating-point grid range. You specify which method to use, with the `grid::type` parameter.

| Value | Describe |
| ------------ | ------------- |
| byrange | You specify the x, y, and z grid ranges, either with separate `grid::xmin`, `grid::xmax`, `grid::ymin`, `grid::ymax`, `grid::zmin`, and `grid::zmax` parameters, or with the `grid::xyzmin` and `grid::xyzmax` parameters. |
| box | This is a special case of `grid::type = "byrange"` with the grid ranges hard-wired to `grid::xyzmin = -0.5` and `grid::xyzmax = +0.5`. |
| byspacing | You specify the x, y, and z grid spacings, either with separate `grid::dx`, `grid::dy`, and `grid::dz` parameters, or with the `grid::dxyz` parameter. |

`grid::avoid_originx` This is a Boolean parameter; if set to true then the grid will be “half-centered” across $x = 0$, ie there will be grid points at $\ldots, x=-\frac{3}{2} \Delta x, x=-\frac{1}{2} \Delta x, x=+\frac{1}{2} \Delta x, x=+\frac{3}{2} \Delta x, \ldots$, but not at $x = 0$.

#### Specifying the Grid Symmetry

`CartGrid3D` allows you to specify the grid symmetry with the `grid::domain` parameter.

| Value | Describe |
| ------------ | ------------- |
| full | There are no symmetries. |
| bitant | The grid includes only the $z \geq 0$ half-space; there is a reflection symmetry across the $z = 0 $plane. |
| quadrant | The grid includes only the $\{x \geq 0, y \geq 0\}$ quadrant. ; there is a reflection symmetry across both the $x = 0$ plane and the $y = 0$ plane. |
| octant | The grid includes only the $\{x \geq 0, y \geq 0, z \geq 0\}$ octant; there is a reflection symmetry across each of the $x = 0$ plane, the $y = 0$ plane and the $z = 0$ plane. |

### CT_MultiLevel

`CT_MultiLevel` is a Cactus thorn that implements a multigrid solver for elliptic partial differential equations (PDEs).

$$
\begin{aligned} c_{x x} \partial_{x x} \psi+c_{x y} \partial_{x y} \psi+c_{x z} \partial_{x z} \psi+c_{y y} \partial_{y y} \psi+c_{y z} \partial_{y z} \psi+c_{z z} \partial_{z z} \psi &+\\ c_{x} \partial_{x} \psi+c_{y} \partial_{y} \psi+c_{z} \partial_{z} \psi+c_{0} \psi^{n_{0}}+c_{1} \psi^{n_{1}}+c_{2} \psi^{n_{2}}+c_{3} \psi^{n_{3}}+c_{4} \psi^{n_{4}} &=0 \end{aligned}
$$

### [IOASCII](http://cactuscode.org/documentation/thorns/CactusBase-IOASCII.pdf)

Thorn `IOASCII` provides I/O methods for 1D, 2D, and 3D output of grid arrays and grid functions into files in ASCII format.


## Boundary Conditions

### [Boundary](http://cactuscode.org/documentation/thorns/CactusBase-Boundary.pdf)

This thorn also provides some standard outer boundary conditions.

| Value | defaults | Describe |
| ------------ | ------------- | ------------- |
| register_scalar | yes | the value of the given field at the boundary is set to a given scalar value. |
| register_flat | yes | the value of the given field at the boundary is copied from the value one grid point in, in any direction. |
| register_radiation | yes | Grid functions are given for the current time level as well as grid functions from a past timelevel which are needed for constructing the boundary condition. |
| register_copy | yes | Copy the boundary values from a different grid function, for example the previous timelevel. |
| register_robin | yes |  |
| register_static | yes | the boundary values do not evolve in time, by copying their values from previous timelevels. |
| register_none | yes |  |

## Initial data

The initial data are computed using the Compact Object CALculator (COCAL) [^1]

![-w534](media/15512756872829.jpg)


[^1]: Lorem ipsum dolor sit amet, consectetur adipiscing elit.

## Diagnostics

The BH apparent horizon is located and monitored through the AHFinderDirect thorn. We estimate the BH mass $M_{\mathrm{BH}}$ and the BH dimensionless spin parameter $a / M_{\mathrm{BH}}$ using the isolated horizon formalism.

## Extract Gravitational Wave

To measure the flux of energy and angular momentum carried away by GWs, we use a modiﬁed version of the Psikadelia thorn.


https://arxiv.org/pdf/1502.05674.pdf
https://arxiv.org/pdf/1809.08237.pdf
https://arxiv.org/pdf/gr-qc/0306056.pdf
https://arxiv.org/pdf/gr-qc/0206008.pdf

## KrancNumericalTools

### GenericFD

| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| assume_stress_energy_state | -1 | Assume stress_energy_state has a particular value |   -1  :: "do not assume anything"*OR*  0:1 :: "assume off or on"*OR* |
| assume_use_jacobian | -1 | Assume use_jacobian has a particular value |   -1  :: "do not assume anything"*OR*  0:1 :: "assume off or on"*OR* |
| jacobian_group | "" | Name of group containing Jacobian |   "" :: "String of the form <implementation>::<groupname>"*OR* |
| jacobian_determinant_group | "" | Name of group containing Jacobian determinant |   "" :: "String of the form <implementation>::<groupname>"*OR* |
| jacobian_inverse_group | "" | Name of group containing Jacobian inverse |   "" :: "String of the form <implementation>::<groupname>"*OR* |
| jacobian_derivative_group | "" | Name of group containing Jacobian derivative |   "" :: "String of the form <implementation>::<groupname>"*OR* |
| allocate_genericfd_jacobian | "no" | Allocate memory for the GenericFD Jacobian (used for benchmarks) |  |


## CactusIO

### IOJpeg

Cactus Code Thorn IOJpeg
Author(s)    : John Shalf
               Gabrielle Allen
               Tom Goodale
               Gerd Lanfermann
               Thomas Radke
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn does output of 2D slices from Cactus grid functions and arrays
in jpeg image format.

It registers the I/O method "IOJpeg" with the flesh.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| out_every | -1 | How often to do IOJpeg output, overrides IO::out_every |    1:* :: "Every so many iterations"*OR*   0:  :: "Disable IOJpeg output"*OR*  -1:  :: "Choose the default from IO::out_every"*OR* |
| out_vars | "" | Variables to output by IOJpeg |   ".+" :: "Space-separated list of fully qualified variable/group names"*OR*  "^$" :: "An empty string to output nothing"*OR* |
| out_dir | "" | Output directory for IOJpeg files, overrides IO::out_dir |   ".+" :: "A valid directory name"*OR*  "^$" :: "An empty string to choose the default from IO::out_dir"*OR* |
| mode | "standard" | Output mode to use |   "remove"   :: "Remove files from old timesteps"*OR*  "standard" :: "Generate a file for each out_every timesteps"*OR* |
| gridpoints | "hyperslab" | How to access grid points |   "hyperslab"   :: "use locations of grid points"*OR*  "interpolate" :: "interpolate to arbitrary points"*OR* |
| colormap_bias | 0.5 | Bias automatic colormap generation towards red (low) or blue (high) |   -1:1 :: "Could be unrestricted"*OR* |
| colormap_quality | 75 | JPEG quality of level |   0:100 :: "Percentage of full quality"*OR* |
| colormap_factor | 32 | How to scale float values to rgb color |   2:256 :: "Positive scaling"*OR* |
| colormap | "custom" | How to set the colormap |   "auto"     :: "Set automatically using min/max of grid variables"*OR*  "auto-old" :: "Set automatically using min/max of grid variables, using the old reduction interface which is still used by Carpet"*OR*  "custom"   :: "Set min/max manually"*OR* |
| colormap_min | -1.0 | minimum value to be mapped to colors |   *:* :: "Only for custom colormap scale"*OR* |
| colormap_max | +1.0 | maximum value to be mapped to colors |   *:* :: "Only for custom colormap scale"*OR* |
| refinement_factor | 1 | Refine each 2D slice by a certain factor (using interpolation) ? |    1:* :: "A factor greater 0"*OR* |
| out2D_yzplane_x | -424242 | x-coord for 2D planes in yz |   *:*      :: "A value between [xmin, xmax]"*OR*  -424242: :: "Default to IO::out_yzplane_x"*OR* |
| out2D_xzplane_y | -424242 | y-coord for 2D planes in xz |   *:*      :: "A value between [ymin, ymax]"*OR*  -424242: :: "Default to IO::out_xzplane_y"*OR* |
| out2D_xyplane_z | -424242 | z-coord for 2D planes in xy |   *:*      :: "A value between [zmin, zmax]"*OR*  -424242: :: "Default to IO::out_xyplane_z"*OR* |
| out2D_yzplane_xi | -1 | x-index (from 0) for 2D planes in yz |    0:* :: "An index between [0, nx)"*OR*  -1:  :: "Choose the default from IO::out_yzplane_xi"*OR* |
| out2D_xzplane_yi | -1 | y-index (from 0) for 2D planes in xz |    0:* :: "An index between [0, ny)"*OR*  -1:  :: "Choose the default from IO::out_xzplane_yi"*OR* |
| out2D_xyplane_zi | -1 | z-index (from 0) for 2D planes in xy |    0:* :: "An index between [0, nz)"*OR*  -1:  :: "Choose the default from IO::out_xyplane_zi"*OR* |
| interpolator_name | "Lagrange | Name of the interpolator |   ".*" :: "must be a registered interpolator"*OR* |
| interpolator_options | "order=2" | Options for the interpolator |   ".*" :: "must be a valid option specification"*OR* |
| interpolator_coordinates | "cart3d" | Coordinate system |   ".*" :: "must be a registered coordinate system"*OR* |
| multiply_by_radius | "no" | Multiply valus by r |  |
| array2d_x0 | 0.0 | Origin |   *:* :: ""*OR* |
| array2d_y0 | 0.0 | Origin |   *:* :: ""*OR* |
| array2d_z0 | 0.0 | Origin |   *:* :: ""*OR* |
| array2d_npoints_i | 10 | Number of grid points for the 2D grid arrays in the i direction |   0:* :: ""*OR* |
| array2d_dx_i | 0.0 | Spacing |   0.0:* :: ""*OR* |
| array2d_dy_i | 0.0 | Spacing |   0.0:* :: ""*OR* |
| array2d_dz_i | 0.0 | Spacing |   0.0:* :: ""*OR* |
| array2d_npoints_j | 10 | Number of grid points for the 2D grid arrays in the j direction |   0:* :: ""*OR* |
| array2d_dx_j | 0.0 | Spacing |   0.0:* :: ""*OR* |
| array2d_dy_j | 0.0 | Spacing |   0.0:* :: ""*OR* |
| array2d_dz_j | 0.0 | Spacing |   0.0:* :: ""*OR* |


## ExternalLibraries

### hwloc

Cactus Code Thorn hwloc
Author(s)    : Erik Schnetter
Maintainer(s): Cactus team
Licence      : New BSD Licence
--------------------------------------------------------------------------

1. Purpose

Distribute the hwloc library; see
<http://www.open-mpi.org/projects/hwloc/>.



From the hwloc web site:

The Portable Hardware Locality (hwloc) software package provides a
portable abstraction (across OS, versions, architectures, ...) of the
hierarchical topology of modern architectures, including NUMA memory
nodes, sockets, shared caches, cores and simultaneous multithreading.
It also gathers various system attributes such as cache and memory
information. It primarily aims at helping applications with gathering
information about modern computing hardware so as to exploit it
accordingly and efficiently.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### LORENE2

Cactus Code Thorn LORENE
Author(s)    : Cactus interface: Erik Schnetter, Roberto De Pietri, Frank Löffler
               Lorene: see Lorene library
Maintainer(s): Cactus team
Licence      : GPL
--------------------------------------------------------------------------

1. Purpose

Distribute the LORENE library; see <http://www.lorene.obspm.fr/>.



From the web site:

LORENE: Langage Objet pour la RElativité NumériquE

LORENE is a set of C++ classes to solve various problems arising in
numerical relativity, and more generally in computational
astrophysics.  It provides tools to solve partial differential
equations by means of multi-domain spectral methods.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### BLAS

Cactus Code Thorn BLAS
Author(s)    : Erik Schnetter
Maintainer(s): Cactus team
Licence      : ?
--------------------------------------------------------------------------

1. Purpose

Distribute the Basic Linear Algebra Subroutines (BLAS); see
<http://netlib.org/blas/>.



From the web site:

The BLAS (Basic Linear Algebra Subprograms) are routines that provide
standard building blocks for performing basic vector and matrix
operations.  The Level 1 BLAS perform scalar, vector and vector-vector
operations, the Level 2 BLAS perform matrix-vector operations, and the
Level 3 BLAS perform matrix-matrix operations.  Because the BLAS are
efficient, portable, and widely available, they are commonly used in
the development of high quality linear algebra software, LAPACK for
example.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### libjpeg

Cactus Code Thorn libjpeg
Author(s)    : Frank Löffler
               Erik Schnetter
Maintainer(s): Cactus team
Licence      : ?
--------------------------------------------------------------------------

1. Purpose

Distribute the libjpeg library; see <http://www.ijg.org/>



From the web site:

IJG is an informal group that writes and distributes a widely used
free library for JPEG image compression. The first version was
released on 7-Oct-1991.

The current version is release 8b of 16-May-2010. This is a stable and
solid foundation for many application's JPEG support.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### MPI

Cactus Code Thorn MPI
Author(s)    : Erik Schnetter
Maintainer(s): Cactus team
Licence      : New BSD license
--------------------------------------------------------------------------

1. Purpose

Distribute the OpenMPI library; see <http://www.open-mpi.org/>.



From the OpenMPI web site:

Open MPI: Open Source High Performance Computing

A High Performance Message Passing Library

The Open MPI Project is an open source MPI-2 implementation that is
developed and maintained by a consortium of academic, research, and
industry partners. Open MPI is therefore able to combine the
expertise, technologies, and resources from all across the High
Performance Computing community in order to build the best MPI library
available. Open MPI offers advantages for system and software vendors,
application developers and computer science researchers.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### PAPI

Cactus Code Thorn PAPI
Author(s)    : Erik Schnetter
Maintainer(s): Cactus team
Licence      : BSD
--------------------------------------------------------------------------

1. Purpose

Distribute the Performance Application Programming Interface; see
<http://icl.cs.utk.edu/papi/index.html>.



From the web site:

PAPI provides the tool designer and application engineer with a
consistent interface and methodology for use of the performance
counter hardware found in most major microprocessors. PAPI enables
software engineers to see, in near real time, the relation between
software performance and processor events.

In addition Component PAPI provides access to a collection of
components that expose performance measurement opportunites across the
hardware and software stack.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "no" | Output debug messages? |  |
| per_thread_statistics | "yes" | Collect statistics per thread (instead of per process) |  |
| define_statistics_clocks | "no" | Define multiple clocks for each PAPI counter, providing min/max/avg/sdv |  |
| use_multiplexing | "yes" | Use multiplexing to gather more, but less accurate information |  |
| out_every | 0 | Output statistics every so many iterations |   0   :: "after initial data and shutdown"*OR*  1:* :: "every so many iterations"*OR*  -1  :: "no statistics output"*OR* |
| events_flops | "PAPI_FP_OPS | PAPI events for flop/sec |   "" :: ""*OR* |
| events_ipc | "PAPI_TOT_CYC | PAPI events for instructions/cycle |   "" :: ""*OR* |
| events_icache | "PAPI_L1_ICA | PAPI events for instruction cache usage |   "" :: ""*OR* |
| events_dcache | "PAPI_L1_DCA | PAPI events for data cache usage |   "" :: ""*OR* |
| events_memory | "PAPI_LD_INS | PAPI evens for memory accesses |   "" :: ""*OR* |
| dgemm_N | 1000 | Matrix size; mem = 32 N^2 bytes, cpu = 2 N^3 flop |   1:* :: "N=2000 requires 128 Mbyte and 16 Gflop; N=5000 requires 800 Mbyte and 250 Gflop"*OR* |


### HDF5

Cactus Code Thorn HDF5
Author(s)    : Erik Schnetter
Maintainer(s): Cactus team
Licence      : ?
--------------------------------------------------------------------------

1. Purpose

Distribute the HDF5 library; see <http://www.hdfgroup.org/HDF5/>.

Provide some generic utility programs to manipulate HDF5 files. These
are independent of any specific Cactus file formats.



From the HDF5 web site:

HDF5 is a data model, library, and file format for storing and
managing data. It supports an unlimited variety of datatypes, and is
designed for flexible and efficient I/O and for high volume and
complex data. HDF5 is portable and is extensible, allowing
applications to evolve in their use of HDF5. The HDF5 Technology suite
includes tools and applications for managing, manipulating, viewing,
and analyzing data in the HDF5 format.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### LAPACK

Cactus Code Thorn LAPACK
Author(s)    : Erik Schnetter
Maintainer(s): Cactus team
Licence      : ?
--------------------------------------------------------------------------

1. Purpose

Distribute the Linear Algebra Package (LAPACK); see
<http://netlib.org/lapack/>.



From the web site:

LAPACK is written in Fortran 90 and provides routines for solving
systems of simultaneous linear equations, least-squares solutions of
linear systems of equations, eigenvalue problems, and singular value
problems.  The associated matrix factorizations (LU, Cholesky, QR,
SVD, Schur, generalized Schur) are also provided, as are related
computations such as reordering of the Schur factorizations and
estimating condition numbers.  Dense and banded matrices are handled,
but not general sparse matrices.  In all areas, similar functionality
is provided for real and complex matrices, in both single and double
precision.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### FFTW3

Cactus Code Thorn FFTW3
Author(s)    : Yosef Zlochower <yosef@astro.rit.edu>
Maintainer(s): 
Licence      : GPL
--------------------------------------------------------------------------

1. Purpose

Distribute the FFTW Library; see <http://www.fftw.org/>.



From the web site: FFTW is a C subroutine library for computing the
discrete Fourier transform (DFT) in one or more dimensions, of
arbitrary input size, and of both real and complex data (as well as of
even/odd data, i.e. the discrete cosine/sine transforms or DCT/DST).
We believe that FFTW, which is free software, should become the FFT
library of choice for most applications.

Our benchmarks, performed on on a variety of platforms, show that
FFTW's performance is typically superior to that of other publicly
available FFT software, and is even competitive with vendor-tuned
codes. In contrast to vendor-tuned codes, however, FFTW's performance
is portable: the same program will perform well on most architectures
without modification. Hence the name, "FFTW," which stands for the
somewhat whimsical title of "Fastest Fourier Transform in the West."

The FFTW package was developed at MIT by Matteo Frigo and Steven G.
Johnson


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### LORENE

Cactus Code Thorn LORENE
Author(s)    : Erik Schnetter
Maintainer(s): Cactus team
Licence      : GPL
--------------------------------------------------------------------------

1. Purpose

Distribute the LORENE library; see <http://www.lorene.obspm.fr/>.



From the web site:

LORENE: Langage Objet pour la RElativité NumériquE

LORENE is a set of C++ classes to solve various problems arising in
numerical relativity, and more generally in computational
astrophysics.  It provides tools to solve partial differential
equations by means of multi-domain spectral methods.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### zlib

Cactus Code Thorn zlib
Author(s)    : Erik Schnetter
Maintainer(s): Cactus team
Licence      : ?
--------------------------------------------------------------------------

1. Purpose

Distribute the zlib library; see <http://www.zlib.net/>



From the web site:

A Massively Spiffy Yet Delicately Unobtrusive Compression Library
(Also Free, Not to Mention Unencumbered by Patents)

zlib is designed to be a free, general-purpose, legally unencumbered
-- that is, not covered by any patents -- lossless data-compression
library for use on virtually any computer hardware and operating
system. The zlib data format is itself portable across platforms.
Unlike the LZW compression method used in Unix compress(1) and in the
GIF image format, the compression method currently used in zlib
essentially never expands the data. (LZW can double or triple the file
size in extreme cases.) zlib's memory footprint is also independent of
the input data and can be reduced, if necessary, at some cost in
compression. A more precise, technical discussion of both points is
available on another page.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### OpenCL

Cactus Code Thorn OpenCL
Author(s)    : Erik Schnetter
Maintainer(s): Cactus team
Licence      : GPL, public domain
--------------------------------------------------------------------------

1. Purpose

Configure with OpenCL; see <http://www.khronos.org/opencl/>.



From the web site:

OpenCL - The open standard for parallel programming of heterogeneous
systems

OpenCL(TM) is the first open, royalty-free standard for
cross-platform, parallel programming of modern processors found in
personal computers, servers and handheld/embedded devices. OpenCL
(Open Computing Language) greatly improves speed and responsiveness
for a wide spectrum of applications in numerous market categories from
gaming and entertainment to scientific and medical software.



2. Available implementations

There seem to be four OpenCL implementations available at this time.
Unfortunately, they each have their drawbacks:

- AMD: Available at
  <http://developer.amd.com/zones/openclzone/pages/default.aspx>. This
  supports both CPUs and ATI GPUs. Unfortunately, the OpenCL compiler
  seems to produce code with a low quality.

- Apple: Included with the operating system, available by default.
  This supports both CPU and GPU. The compiler is based on LLVM.
  Unfortunately, there seem to be serious bugs -- for example, I can't
  get the cos function to provide correct results.

- FreeOCL: Open source, available at
  <https://code.google.com/p/freeocl/>. This is a stand-alone
  implementation for CPUs only.

- Intel: Available at
  <http://software.intel.com/en-us/articles/opencl-sdk/>. This
  supports only (Intel?) CPUs. The compiler is based on LLVM, and the
  implementation is also based on Intel's TBB (Threading Building
  Blocks).

- Nvidia: Available at <http://developer.nvidia.com/opencl>, included
  in their CUDA distribution. This supports only GPUs.

- pocl: Open source, available at <https://launchpad.net/pocl>. This
  OpenCL implementation is based on LLVM.

In addition, Wikipedia <http://en.wikipedia.org/wiki/OpenCL> lists two
IBM implementations for their Power processor and for Intel compatible
CPUs, respectively. The latter may be identical with or similar to
AMD's implementation.



3. Disassembling OpenCL code

Intel:

env PATH=$PATH:/usr/local/intel_ocl_sdk_1.5_x64/usr/lib64/OpenCL/vendors/intel CLASSPATH=/usr/local/intel_ocl_sdk_1.5_x64/usr/lib64/OpenCL/vendors/intel LD_LIBRARY_PATH=/usr/local/intel_ocl_sdk_1.5_x64/usr/lib64/OpenCL/vendors/intel /usr/local/intel_ocl_sdk_1.5_x64/usr/bin/ioc -input=mk-mclachlan-cl-rhs/ML_BSSN_CL_InitRHS.cl -asm=ML_BSSN_CL_InitRHS.cl.s


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### OpenBLAS

Cactus Code Thorn OpenBLAS
Author(s)    : Erik Schnetter
Maintainer(s): Cactus team
Licence      : GPL
--------------------------------------------------------------------------

1. Purpose

Distribute OpenBLAS; see <https://github.com/xianyi/OpenBLAS/wiki>.



From the web site:

OpenBLAS is an optimized BLAS library based on GotoBLAS2 1.13 BSD
version.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### pciutils

Cactus Code Thorn pciutils
Author(s)    : Erik Schnetter
Maintainer(s): Cactus team
Licence      : GPL
--------------------------------------------------------------------------

1. Purpose

Distribute the PCI Utilities; see <http://mj.ucw.cz/sw/pciutils/>.



From the pciutils web site:

The PCI Utilities are a collection of programs for inspecting and
manipulating configuration of PCI devices, all based on a common
portable library libpci which offers access to the PCI configuration
space on a variety of operating systems.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### GSL

Cactus Code Thorn GSL
Author(s)    : Erik Schnetter
Maintainer(s): Cactus team
Licence      : GPL
--------------------------------------------------------------------------

1. Purpose

Distribute the GNU Scientific Library; see
<http://www.gnu.org/software/gsl/>.



From the web site:

The GNU Scientific Library (GSL) is a numerical library for C and C++
programmers.  It is free software under the GNU General Public
License.

The library provides a wide range of mathematical routines such as
random number generators, special functions and least-squares fitting.
There are over 1000 functions in total with an extensive test suite.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### pthreads

Cactus Code Thorn PTHREADS
Author(s)    : Roland Haas
Maintainer(s): Cactus team
Licence      : MIT
--------------------------------------------------------------------------

1. Purpose

Provide access to the POSIX threads libraries.

This thorn does not actually provide the library it only offers a convenient
way to include it in a Cactus configuration.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### PETSc

Cactus Code Thorn PETSc
Author(s)    : Erik Schnetter
Maintainer(s): Cactus team
Licence      : ?
--------------------------------------------------------------------------

1. Purpose

Distribute the PETSc library; see <http://www.mcs.anl.gov/petsc>.



From the web site:

Portable, Extensible Toolkit for Scientific Computation

PETSc, pronounced PET-see (the S is silent), is a suite of data
structures and routines for the scalable (parallel) solution of
scientific applications modeled by partial differential equations. It
employs the MPI standard for parallelism.



When referencing PETSc in a publication, please cite the following:

@Misc{petsc-web-page,
  Author =       "Satish Balay and Kris Buschelman and William D.
                  Gropp and Dinesh Kaushik and Matthew G. Knepley and
                  Lois Curfman McInnes and Barry F. Smith and Hong
                  Zhang",
  Title =        "{PETSc} {W}eb page",
  Note =         "http://www.mcs.anl.gov/petsc",
  Year =         2009
}

@TechReport{petsc-user-ref,
  Author =       "Satish Balay and Kris Buschelman and Victor Eijkhout
                  and William D. Gropp and Dinesh Kaushik and Matthew
                  G. Knepley and Lois Curfman McInnes and Barry F.
                  Smith and Hong Zhang",
  Title =        "{PETS}c Users Manual",
  Number =       "ANL-95/11 - Revision 3.0.0",
  Institution =  "Argonne National Laboratory",
  Year =         2008
}

@InProceedings{petsc-efficient,
  Author =       "Satish Balay and William D. Gropp and Lois Curfman
                  McInnes and Barry F. Smith",
  Title =        "Efficient Management of Parallelism in Object
                  Oriented Numerical Software Libraries",
  Booktitle =    "Modern Software Tools in Scientific Computing",
  Editor =       "E. Arge and A. M. Bruaset and H. P. Langtangen",
  Pages =        "163--202",
  Publisher =    "Birkh{\"{a}}user Press",
  Year =         1997
}


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### OpenSSL

Cactus Code Thorn OpenSSL
Author(s)    : Erik Schnetter
Maintainer(s): Cactus team
Licence      : Apache-style
--------------------------------------------------------------------------

1. Purpose

Distribute the OpenSSL library; see <http://www.openssl.org/>.



From the web site:

The OpenSSL Project is a collaborative effort to develop a robust,
commercial-grade, full-featured, and Open Source toolkit implementing
the Secure Sockets Layer (SSL v2/v3) and Transport Layer Security (TLS
v1) protocols as well as a full-strength general purpose cryptography
library. The project is managed by a worldwide community of volunteers
that use the Internet to communicate, plan, and develop the OpenSSL
toolkit and its related documentation.

OpenSSL is based on the excellent SSLeay library developed by Eric A.
Young and Tim J. Hudson. The OpenSSL toolkit is licensed under an
Apache-style licence, which basically means that you are free to get
and use it for commercial and non-commercial purposes subject to some
simple license conditions.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


## CactusNumerical

### Periodic

CVS info   : $Header$

Cactus Code Thorn Periodic
Thorn Author(s)     : Erik Schnetter <schnetter@uni-tuebingen.de>
Thorn Maintainer(s) : Erik Schnetter <schnetter@uni-tuebingen.de>
--------------------------------------------------------------------------

Purpose of the thorn:

Periodic boundary conditions that do not depend on PUGH.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "no" | Produce screen output while applying boundary conditions |  |
| periodic | "no" | Periodic boundary conditions |  |
| periodic_x | "no" | Periodic boundary conditions in x-direction |  |
| periodic_y | "no" | Periodic boundary conditions in y-direction |  |
| periodic_z | "no" | Periodic boundary conditions in z-direction |  |
| poison_boundaries | "no" | Fill the symmetry boundary with a poison value before the symmetry is applied |  |
| poison_value | 254 | Integer value (0..255) used to poison new timelevels (with memset) |   0:255 :: "Must fit into a byte.  Use 0 for zero, 255 for nan, and e.g. 113 for a large value."*OR* |
| check_boundaries | "no" | Check the boundaries for poison |  |


### Slab

Cactus Code Thorn Slab
Thorn Author(s)     : Erik Schnetter <schnetter@cct.lsu.edu>
Thorn Maintainer(s) : Erik Schnetter <schnetter@cct.lsu.edu>
--------------------------------------------------------------------------

Purpose of the thorn:

This thorn provides routines that use MPI to transfer slabs of arrays
between processors.

There is a thorn TAT/SlabTest that contains additional routines for
testing this thorn.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| timer_output | "no" | Print slabbing timings at shutdown time |  |
| use_alltoallv | "no" | Use MPI_Alltoallv for communication? |  |


### RotatingSymmetry180

Cactus Code Thorn RotatingSymmetry180
Author(s)    : Erik Schnetter <schnetter@cct.lsu.edu>
Maintainer(s): Erik Schnetter <schnetter@cct.lsu.edu>
Licence      : GPL
--------------------------------------------------------------------------

1. Purpose

Provide a 180 degree rotational symmetry boundary condition.  This is
the kind of symmetry that exists e.g. in a binary black hole evolution
when the two black holes are identical.  The same setup also admits an
additional bitant symmetry.

Currently the symmetry is fixed to be located at the lower x boundary,
so that there is a rotation about the z axis.

The coordinate thorn does not know about this symmetry.  You will have
to set up the domain such that you have an upper bitant in the x
direction.

This symmetry boundary condition can be mixed with arbitrary symmetry
conditions in the z direction, e.g. bitant or periodicity.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "no" | Produce screen output while applying boundary conditions |  |
| hyperslabber | "TAT/Slab" | Hyperslabbing mechanism |   "TAT/Slab"     :: ""*OR*  "GetHyperslab" :: ""*OR* |
| use_coordbase | "no" | Get symmetry boundary location from CoordBase (and ignore symmetry_boundary_* parameters) |  |
| symmetry_boundary_x | 0.0 | x-coordinate of symmetry boundary (ignored if use_coordbase is set) |   *:* :: ""*OR* |
| symmetry_boundary_y | 0.0 | y-coordinate of symmetry boundary (ignored if use_coordbase is set) |   *:* :: ""*OR* |
| poison_boundaries | "no" | Fill the symmetry boundary with a poison value before the symmetry is applied |  |
| poison_value | 254 | Integer value (0..255) used to poison new timelevels (with memset) |   0:255 :: "Must fit into a byte.  Use 0 for zero, 255 for nan, and e.g. 113 for a large value."*OR* |
| check_boundaries | "no" | Check the boundaries for poison |  |


### RotatingSymmetry90

Cactus Code Thorn RotatingSymmetry90
Author(s)    : Erik Schnetter <schnetter@cct.lsu.edu>
Maintainer(s): Erik Schnetter <schnetter@cct.lsu.edu>
Licence      : GPL
--------------------------------------------------------------------------

1. Purpose

Provide a 90 degree rotational symmetry boundary condition.  This is
the kind of symmetry that exists e.g. in a single black hole evolution
when the black hole has spin.  The same setup also admits an
additional bitant symmetry.

Currently the symmetry is fixed to be located at the lower x and y
boundaries, so that there is a rotation about the z axis.

The coordinate thorn does not know about this symmetry.  You will have
to set up the domain such that you have an upper quadrant in the x and
y directions.

This symmetry boundary condition can be mixed with arbitrary symmetry
conditions in the z direction, e.g. bitant or periodicity.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "no" | Produce screen output while applying boundary conditions |  |
| poison_boundaries | "no" | Fill the symmetry boundary with a poison value before the symmetry is applied |  |
| poison_value | 254 | Integer value (0..255) used to poison new timelevels (with memset) |   0:255 :: "Must fit into a byte.  Use 0 for zero, 255 for nan, and e.g. 113 for a large value."*OR* |
| check_boundaries | "no" | Check the boundaries for poison |  |


### InterpToArray

Cactus Code Thorn InterpToArray
Thorn Author(s)     : Erik Schnetter <schnetter@cct.lsu.edu>
Thorn Maintainer(s) : Erik Schnetter <schnetter@cct.lsu.edu>
--------------------------------------------------------------------------

Purpose of the thorn:

Interpolate grid functions to grid arrays on a regular grid of
locations, so that these can e.g. be output more easily.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| interpolator_name | "Lagrange | Name of the interpolator |   ".*" :: "must be a registered interpolator"*OR* |
| interpolator_options | "order=2" | Options for the interpolator |   ".*" :: "must be a valid option specification"*OR* |
| interpolator_coordinates | "cart3d" | Coordinate system |   ".*" :: "must be a registered coordinate system"*OR* |
| use_carpetinterp2 | "no" | Use InterpGridArrays rather than CCTK_InterpGridArrays |  |
| carpetinterp2_interpolator_order | 2 | Order of interpolation for CarpetInterp2 |   0:* :: "any order supported by CarpetInterp2"*OR* |
| nghosts | 0 | Number of ghost zones |   0:* :: ""*OR* |
| nscalars | 0 | Number of grid scalars |   0:100 :: ""*OR* |
| scalar_x0 | 0.0 | Origin |   *:* :: ""*OR* |
| scalar_y0 | 0.0 | Origin |   *:* :: ""*OR* |
| scalar_z0 | 0.0 | Origin |   *:* :: ""*OR* |
| narrays1d | 0 | Number of 1D grid arrays |   0:100 :: ""*OR* |
| array1d_x0 | 0.0 | Origin |   *:* :: ""*OR* |
| array1d_y0 | 0.0 | Origin |   *:* :: ""*OR* |
| array1d_z0 | 0.0 | Origin |   *:* :: ""*OR* |
| array1d_npoints_i | 0 | Number of grid points for the 1D grid arrays in the i direction |   0:* :: ""*OR* |
| array1d_dx_i | 0.0 | Spacing |   0.0:* :: ""*OR* |
| array1d_dy_i | 0.0 | Spacing |   0.0:* :: ""*OR* |
| array1d_dz_i | 0.0 | Spacing |   0.0:* :: ""*OR* |
| narrays2d | 0 | Number of 2D grid arrays |   0:100 :: ""*OR* |
| array2d_x0 | 0.0 | Origin |   *:* :: ""*OR* |
| array2d_y0 | 0.0 | Origin |   *:* :: ""*OR* |
| array2d_z0 | 0.0 | Origin |   *:* :: ""*OR* |
| array2d_npoints_i | 0 | Number of grid points for the 2D grid arrays in the i direction |   0:* :: ""*OR* |
| array2d_dx_i | 0.0 | Spacing |   0.0:* :: ""*OR* |
| array2d_dy_i | 0.0 | Spacing |   0.0:* :: ""*OR* |
| array2d_dz_i | 0.0 | Spacing |   0.0:* :: ""*OR* |
| array2d_npoints_j | 0 | Number of grid points for the 2D grid arrays in the j direction |   0:* :: ""*OR* |
| array2d_dx_j | 0.0 | Spacing |   0.0:* :: ""*OR* |
| array2d_dy_j | 0.0 | Spacing |   0.0:* :: ""*OR* |
| array2d_dz_j | 0.0 | Spacing |   0.0:* :: ""*OR* |
| narrays3d | 0 | Number of 3D grid arrays |   0:100 :: ""*OR* |
| array3d_x0 | 0.0 | Origin |   *:* :: ""*OR* |
| array3d_y0 | 0.0 | Origin |   *:* :: ""*OR* |
| array3d_z0 | 0.0 | Origin |   *:* :: ""*OR* |
| array3d_npoints_i | 0 | Number of grid points for the 3D grid arrays in the i direction |   0:* :: ""*OR* |
| array3d_dx_i | 0.0 | Spacing |   0.0:* :: ""*OR* |
| array3d_dy_i | 0.0 | Spacing |   0.0:* :: ""*OR* |
| array3d_dz_i | 0.0 | Spacing |   0.0:* :: ""*OR* |
| array3d_npoints_j | 0 | Number of grid points for the 3D grid arrays in the j direction |   0:* :: ""*OR* |
| array3d_dx_j | 0.0 | Spacing |   0.0:* :: ""*OR* |
| array3d_dy_j | 0.0 | Spacing |   0.0:* :: ""*OR* |
| array3d_dz_j | 0.0 | Spacing |   0.0:* :: ""*OR* |
| array3d_npoints_k | 0 | Number of grid points for the 3D grid arrays in the k direction |   0:* :: ""*OR* |
| array3d_dx_k | 0.0 | Spacing |   0.0:* :: ""*OR* |
| array3d_dy_k | 0.0 | Spacing |   0.0:* :: ""*OR* |
| array3d_dz_k | 0.0 | Spacing |   0.0:* :: ""*OR* |
| nparrays1d | 0 | Number of 1D parallel grid arrays |   0:100 :: ""*OR* |
| parray1d_x0 | 0.0 | Origin |   *:* :: ""*OR* |
| parray1d_y0 | 0.0 | Origin |   *:* :: ""*OR* |
| parray1d_z0 | 0.0 | Origin |   *:* :: ""*OR* |
| parray1d_npoints_i | 0 | Number of grid points for the 1D grid parrays in the i direction |   0:* :: ""*OR* |
| parray1d_dx_i | 0.0 | Spacing |   0.0:* :: ""*OR* |
| parray1d_dy_i | 0.0 | Spacing |   0.0:* :: ""*OR* |
| parray1d_dz_i | 0.0 | Spacing |   0.0:* :: ""*OR* |
| nparrays2d | 0 | Number of 2D parallel grid arrays |   0:100 :: ""*OR* |
| parray2d_x0 | 0.0 | Origin |   *:* :: ""*OR* |
| parray2d_y0 | 0.0 | Origin |   *:* :: ""*OR* |
| parray2d_z0 | 0.0 | Origin |   *:* :: ""*OR* |
| parray2d_npoints_i | 0 | Number of grid points for the 2D parallel grid arrays in the i direction |   0:* :: ""*OR* |
| parray2d_dx_i | 0.0 | Spacing |   0.0:* :: ""*OR* |
| parray2d_dy_i | 0.0 | Spacing |   0.0:* :: ""*OR* |
| parray2d_dz_i | 0.0 | Spacing |   0.0:* :: ""*OR* |
| parray2d_npoints_j | 0 | Number of grid points for the 2D parallel grid arrays in the j direction |   0:* :: ""*OR* |
| parray2d_dx_j | 0.0 | Spacing |   0.0:* :: ""*OR* |
| parray2d_dy_j | 0.0 | Spacing |   0.0:* :: ""*OR* |
| parray2d_dz_j | 0.0 | Spacing |   0.0:* :: ""*OR* |
| nparrays3d | 0 | Number of 3D grid parrays |   0:100 :: ""*OR* |
| parray3d_x0 | 0.0 | Origin |   *:* :: ""*OR* |
| parray3d_y0 | 0.0 | Origin |   *:* :: ""*OR* |
| parray3d_z0 | 0.0 | Origin |   *:* :: ""*OR* |
| parray3d_npoints_i | 0 | Number of grid points for the 3D parallel grid arrays in the i direction |   0:* :: ""*OR* |
| parray3d_dx_i | 0.0 | Spacing |   0.0:* :: ""*OR* |
| parray3d_dy_i | 0.0 | Spacing |   0.0:* :: ""*OR* |
| parray3d_dz_i | 0.0 | Spacing |   0.0:* :: ""*OR* |
| parray3d_npoints_j | 0 | Number of grid points for the 3D parallel grid arrays in the j direction |   0:* :: ""*OR* |
| parray3d_dx_j | 0.0 | Spacing |   0.0:* :: ""*OR* |
| parray3d_dy_j | 0.0 | Spacing |   0.0:* :: ""*OR* |
| parray3d_dz_j | 0.0 | Spacing |   0.0:* :: ""*OR* |
| parray3d_npoints_k | 0 | Number of grid points for the 3D parallel grid arrays in the k direction |   0:* :: ""*OR* |
| parray3d_dx_k | 0.0 | Spacing |   0.0:* :: ""*OR* |
| parray3d_dy_k | 0.0 | Spacing |   0.0:* :: ""*OR* |
| parray3d_dz_k | 0.0 | Spacing |   0.0:* :: ""*OR* |


### TestLocalReduce

Cactus Code Thorn TestLocalReduce
Author(s)    : unknown
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

not documented


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| array_nx | 4 | Size of arrays in x direction |   0:* :: ""*OR* |
| array_ny | 5 | Size of arrays in y direction |   0:* :: ""*OR* |
| array_nz | 10 | Size of arrays in z direction |   0:* :: ""*OR* |


### TestLocalInterp2

Cactus Code Thorn LocalInterpTest
Author(s)    : David radice <dradice@caltech.edu>
Maintainer(s): David radice <dradice@caltech.edu>
Licence      : GPLv3
--------------------------------------------------------------------------

1. Purpose

not documented


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| warn_level | 1 | Warn level used to advertise failures |   0:4 :: "A valid error level"*OR* |
| tolerance | 1.0e-15 | Test tolerance |     (0:* :: "Any positive value"*OR* |
| test_symmetric_interpolation | "no" | Test symmetry preserving property of interpolation |  |


### Noise

Cactus Code Thorn Noise
Authors: Denis Pollney <pollney@aei.mpg.de>
CVS info: $Header$
--------------------------------------------------------------------------

Purpose of the thorn:

This thorn adds random noise to grid variables at initial data and/or
boundaries, for performing the "robust stability" tests proposed by
Jeff Winicour.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| apply_id_noise | "no" | Add random noise to initial data |  |
| apply_bc_noise | "no" | Add random noise to initial data |  |
| id_vars | "" | Initial data variables to modify with noise |   .* :: "A regex which matches everything"*OR* |
| bc_vars | "" | Variables to modify with noise at boundary |   .* :: "A regex which matches everything"*OR* |
| amplitude | 0.000001 | Maximum absolute value of random data |   0: :: "Positive number"*OR* |


### LocalInterp

Cactus Code Thorn LocalInterp
Author(s)    : Thomas Radke
               Jonathan Thornburg
               Erik Schnetter
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn does processor-local interpolation of N-dimensional data
arrays.  In general there may be many input arrays (all defined on the
same uniform Cartesian grid) all being interpolated to the same set
of interpolation points.


History
=======

This interpolator was written by Thomas Radke in early 2001 (drawing
on older code by Paul Walker), and supports the interpolation operator
  "uniform cartesian"
for the new Cactus interpolation API CCTK_InterpLocalUniform().
(Note that the word "cartesian" is in lower case here!)

This interpolator originally lived in the PUGHInterp thorn, but
it turned to have very little to do with PUGH, so was moved here in
winter 2001-2002.

From winter 2001-2002 to July 2003 this thorn also contained another
interpolator written by Jonathan Thornburg, but in July 2003 that
interpolator was moved to AEIThorns/AEILocalInterp because it was
(is) GPL and Cactus policies are that this arrangement (CactusBase)
is reserved for code under the Cactus-flesh license (= GPL except
that it's like LGPL for linking with other thorns).

Because CVS can't delete directories, this thorn still contains a lot
of empty-except-for-CVS-directories directory trees left over from
Jonathan Thornburg's interpolator.  You can/should ignore these.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### TensorTypes

Cactus Code Thorn TensorTypes
Thorn Author(s)     : Erik Schnetter <schnetter@aei.mpg.de>
Thorn Maintainer(s) : Erik Schnetter <schnetter@aei.mpg.de>
License             : LGPLv2.1 or later
--------------------------------------------------------------------------

Purpose of the thorn:

Provide information about various types of tensors.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### Dissipation

CVS info   : $Header$

Cactus Code Thorn Dissipation
Thorn Author(s)     : Erik Schnetter <schnetter@aei.mpg.de>
Thorn Maintainer(s) : Erik Schnetter <schnetter@aei.mpg.de>
--------------------------------------------------------------------------

Purpose of the thorn:

Add fourth order Kreiss-Oliger dissipation to the right hand side of
evolution equations.  This thorn works only for time evolutions that
use MoL.  I thank Scott Hawley who wrote a very similar thorn
HawleyThorns/Dissipation for evolutions that do not use MoL; this
thorn here is modelled after his.

For a description of the artificial dissipation, see H. Kreiss and
J. Oliger, "Methods for the Approximate Solution of Time Dependent
Problems", vol. 10 of Global Atmospheric Research Programme (GARP):
GARP Publication Series (GARP Publication, 1973).


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "no" | produce log output |  |
| order | 3 | Dissipation order |   1 :: "first order accurate dissipation (using a second derivative)"*OR*  3 :: "third order accurate dissipation (using a fourth derivative)"*OR*  5 :: "fifth order accurate dissipation (using a sixth derivative)"*OR*  7 :: "seventh order accurate dissipation (using an eighth derivative)"*OR*  9 :: "ninth order accurate dissipation (using a tenth derivative)"*OR* |
| epsdis | 0.2 | Dissipation strength |   *:* :: "0 for no dissipation.  Unstable for epsdis<0 and epsdis>1/3"*OR* |
| vars | "" | List of evolved grid functions that should have dissipation added |   .* :: "must be a valid list of grid functions"*OR* |
| use_dissipation_near_excision | "yes" | Apply excision near the excision boundary (does not work for high orders) |  |
| extra_dissipation_in_horizons | "no" | extra dissipation in horizons |  |
| update_ah_every | 1 | how often to update the AH information for dissipation |   0:* :: "positive iteration number"*OR* |
| ah_slope | 0.2 | Slope inside AH |   *:* :: "Slope from the outside value to the inside value in AHs"*OR* |
| ah_radius_offset | 0.0 | Offset to the distance from the AH. |   *:* :: "negative values shift inwards, positive outwards"*OR* |
| ah_max_epsdis | -1.0 | maximal epsdis |   *:* :: "<0 for 'off', >=0 for maximal epsdis in horizon"*OR* |
| respect_emask | "no" | respect excision mask |  |
| extra_dissipation_at_outerbound | "no" | increase dissipation at outer boundary |  |
| outer_bound_npoints | 3 | number of points in which dissipation should be increased |   0:* :: "positive number"*OR* |
| ob_slope | 5 | slope at outer boundary |   0:* :: "increase dissipation"*OR* |
| outer_boundary_max_epsdis | -1.0 | maximal epsdis |   *:* :: "<0 for 'off', >=0 for maximal epsdis at the outer boundary"*OR* |


### LocalReduce

Cactus Code Thorn LocalReduce
Author(s)    : Ravi Paruchuri
               Tom Goodale
               Yaakoub El Khamra
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Local array reduction implementation, using the new local reduction API. New features added, please refer to the thorn documentation for details and to the TestLocalReduce thorn for examples of usage. 


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### SummationByParts

Cactus Code Thorn SummationByParts
Author(s)     : Peter Diener <diener@cct.lsu.edu>
Maintainer(s) : Peter Diener <diener@cct.lsu.edu>
License       : LGPLv2.1 or later
--------------------------------------------------------------------------

Purpose of the thorn:

Calculate derivates of grid functions using finite difference stencils
that satisfy summation by parts.



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| norm_type | "Diagonal" | Type of norm |   "Diagonal" :: "Diagonal norm"*OR*  "Full restricted" :: "Full restricted norm"*OR* |
| operator_type | "Optimized" | Type of operator |   "Minimal Bandwidth" :: "Minimal bandwidth (except for 8-4 which is minimal spectral radius)"*OR*  "Optimized" :: "Optimized for performance"*OR* |
| sbp_1st_deriv | "yes" | Should the 1st derivative operator be SBP |  |
| sbp_2nd_deriv | "yes" | Should the 2nd derivative operator be SBP |  |
| sbp_upwind_deriv | "yes" | Should the upwind derivative operator be SBP |  |
| order | 2 | Order of accuracy |   2:8:2 :: ""*OR* |
| onesided_outer_boundaries | "yes" | Evaluate derivatives within ghost zones of the outer boundary |  |
| onesided_interpatch_boundaries | "yes" | Evaluate derivatives near the local grid boundary if it is an inter-patch boundary |  |
| use_dissipation | "no" | Should we add dissipation |  |
| use_variable_deltas | "no" | Use extra grid functions to allow for variable delta's in the dissipation operators |  |
| use_shiftout | "no" | Should we use the boundary_shift_out parameters from CoordBase to shift the stencils of derivatives and dissipation |  |
| poison_derivatives | "no" | Should we poison Dvar at boundary_shiftout perimeter when taking derivatives |  |
| poison_dissipation | "no" | Should we poison rhs at boundary_shiftout perimeter when applying dissipation |  |
| poison_value | 666.0 | Degree of intoxication |   *:* :: "Anything you want"*OR* |
| scale_with_h | "no" | Should we scale the dissipation with the grid spacing h |  |
| dissipation_type | "Mattson-Svard-Nordstrom" | Type of dissipation operator |   "Mattson-Svard-Nordstrom" :: "Mattson, Svaerd and Nordstroem type"*OR*  "Kreiss-Oliger" :: "Kreiss-Oliger modified near the boundaries"*OR* |
| epsdis | 0.2 | Dissipation strength |   *:* :: "Values typical between 0 and 1"*OR* |
| vars | "" | List of evolved grid functions that should have dissipation added |   .* :: "Must be a valid list of grid functions"*OR* |
| zero_derivs_y | "no" | set all derivatives to 0 in the y-direction |  |
| zero_derivs_z | "no" | set all derivatives to 0 in the z-direction |  |
| check_grid_sizes | "yes" | Should we check grid sizes and ghost zones |  |


### SphericalSurface

Cactus Code Thorn SphericalSurface
Thorn Author(s)     : Erik Schnetter <schnetter@cct.lsu.edu>
Thorn Maintainer(s) : Erik Schnetter <schnetter@cct.lsu.edu>
--------------------------------------------------------------------------

Purpose of the thorn:

Store spherical surfaces.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| nsurfaces | 0 | Number of surfaces |   0:42 :: ""*OR* |
| verbose | no | Shall I be verbose? |  |
| maxntheta | 19 | do not set this parameter directly |   0:* :: ""*OR* |
| maxnphi | 38 | do not set this parameter directly |   0:* :: ""*OR* |


### LocalInterp2

Cactus Code Thorn LocalInterp
Author(s)    : David Radice
               Thomas Radke
               Jonathan Thornburg
               Erik Schnetter
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn does processor-local interpolation of N-dimensional data
arrays.  In general there may be many input arrays (all defined on the
same uniform Cartesian grid) all being interpolated to the same set
of interpolation points.


History
=======

This interpolator was written by Thomas Radke in early 2001 (drawing
on older code by Paul Walker), and supports the interpolation operator
  "uniform cartesian"
for the new Cactus interpolation API CCTK_InterpLocalUniform().
(Note that the word "cartesian" is in lower case here!)

This interpolator originally lived in the PUGHInterp thorn, but
it turned to have very little to do with PUGH, so was moved here in
winter 2001-2002.

From winter 2001-2002 to July 2003 this thorn also contained another
interpolator written by Jonathan Thornburg, but in July 2003 that
interpolator was moved to AEIThorns/AEILocalInterp because it was
(is) GPL and Cactus policies are that this arrangement (CactusBase)
is reserved for code under the Cactus-flesh license (= GPL except
that it's like LGPL for linking with other thorns).

At the beginning of 2015 the low-level interpolation kernels of this
thorn were rewritten in C++ by David Radice.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### Cartoon2D

Cactus Code Thorn Cartoon2D
Author(s)    : Sai Iyer
               Denis Pollney
               Thomas Radke
               David Rideout
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Implements a "cartoon" axisymmetric evolution according to the method
described in gr-qc/9908012.  The original idea for 'Cartoon2D' was due
to Steve Brandt.

See doc/documentation.tex for usage notes.

See the test/ directory for sample parameter files.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| cartoon_active | "no" | Activate cartoon boundary condition |  |
| verbose | "no" | Verbose information |  |
| stencil | "yes" | Use custom 2D stencil if available |  |
| order | 4 | Cartoon's interpolation order |   1:5 :: "From linear to fifth order."*OR* |
| eno_order | 4 | The interpolation order applied to the ENO interpolator |   1:5 :: "From linear to fifth order."*OR* |
| allow_grid_resize | "no" | Allow grid to be resized in a cartoon-compatible way |  |
| old_excision | "no" | Are we doing excision based on the old style mask? |  |
| new_excision | "no" | Are we doing excision based on the new style mask? |  |
| old_style_excision_var | "" | The variable to be checked for old style excision |   ".*" :: "Expected to be \'Spacemask::emask\'"*OR* |
| new_style_excision_var | "" | The variable to be checked for new style excision |   ".*" :: "Expected to be \'Spacemask::space_mask\'"*OR* |
| new_mask_field_name | "" | The name of the field that describes excision for the new mask |   ".*" :: "Could be anything"*OR* |
| new_mask_excised_name | "" | The name of the descriptor that says the point is excised for the new mask |   ".*" :: "Could be anything"*OR* |


### SlabTest

CVS info   : $Header$

Cactus Code Thorn SlabTest
Thorn Author(s)     : Erik Schnetter <schnetter@uni-tuebingen.de>
Thorn Maintainer(s) : Erik Schnetter <schnetter@uni-tuebingen.de>
--------------------------------------------------------------------------

Purpose of the thorn:

This thorn tests the slab transfer routines of the thorn Slab.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### MoL

Cactus Code Thorn MoL
Author(s)    : Ian Hawke
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn provides generic time integrators.
This allows clean coupling when multiple thorns wish to integrate at the
same time.
It also means a physics thorn doesn't have to rewrite its own time
integrator.
This version is designed to work with Mesh Refinement codes as well as
unigrid.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| MoL_Num_Evolved_Vars | 0 | The maximum number of variables to be evolved by MoL (DPRECATED) |   0:*		:: "Anything non negative. Added to by other thorns."*OR* |
| MoL_Num_Evolved_Vars_Slow | 0 | The maximum number of 'slow' variables to be evolved by MoL (DPRECATED) |   0:*		:: "Anything non negative. Added to by other thorns."*OR* |
| MoL_Num_Constrained_Vars | 0 | The maximum number of constrained variables with timelevels that MoL needs to know about (DPRECATED) |   0:*		:: "Anything non negative. Added to by other thorns."*OR* |
| MoL_Num_SaveAndRestore_Vars | 0 | The maximum number of variables to be evolved outside of MoL but that MoL needs to know about (DPRECATED) |   0:*		:: "Anything non negative. Added to by other thorns."*OR* |
| MoL_Max_Evolved_Array_Size | 0 | The maximum total size of any grid arrays to be evolved |   0:*          :: "Anything non negative. Accumulated by other thorns"*OR* |
| MoL_Num_ArrayEvolved_Vars | 0 | The maximum number of array variables to be evolved by MoL (DPRECATED) |   0:*		:: "Anything non negative. Added to by other thorns."*OR* |
| MoL_Num_ArrayConstrained_Vars | 0 | The maximum number of array constrained variables with timelevels that MoL needs to know about (DPRECATED) |   0:*		:: "Anything non negative. Added to by other thorns."*OR* |
| MoL_Num_ArraySaveAndRestore_Vars | 0 | The maximum number of array variables to be evolved outside of MoL but that MoL needs to know about (DPRECATED) |   0:*		:: "Anything non negative. Added to by other thorns."*OR* |
| MoL_Num_Scratch_Levels | 0 | Number of scratch levels required by the ODE method |   0:*		:: "Anything non negative"*OR* |
| ODE_Method | "ICN" | The ODE method use by MoL to do time integration |   "Generic"	:: "Generic Shu-Osher Runge-Kutta type"*OR*  "ICN"		:: "Iterative Crank Nicholson"*OR*  "ICN-avg"	:: "Iterative Crank Nicholson with averaging"*OR*  "Euler"	:: "Euler"*OR*  "RK2"		:: "Efficient RK2"*OR*  "RK2-central"	:: "Central RK2"*OR*  "RK3"		:: "Efficient RK3"*OR*  "RK4"		:: "Efficient RK4"*OR*  "RK45"        :: "RK45 (Fehlberg) with error estimation"*OR*  "RK45CK"      :: "RK45CK (Cash-Karp) with error estimation"*OR*  "RK65"        :: "RK65 with error estimation"*OR*  "RK87"        :: "RK87 with error estimation"*OR*  "AB"          :: "Adams-Bashforth"*OR*  "RK2-MR-2:1"  :: "2nd order 2:1 multirate RK scheme based on RK2 due to Schlegel et al 2009. This requires init_RHS_zero='no'."*OR*  "RK4-MR-2:1"  :: "3rd order 2:1 multirate RK scheme based on RK43 due to Schlegel et al 2009. This requires init_RHS_zero='no'."*OR*  "RK4-RK2"     :: "RK4 as fast method and RK2 as slow method"*OR* |
| Generic_Type | "RK" | If using the generic method, which sort |   "RK"		:: "One of the standard TVD Runge-Kutta methods"*OR*  "ICN"		:: "Iterative Crank Nicholson as a generic method"*OR*  "Table"       :: "Given from the generic method descriptor parameter"*OR*  "Classic RK3"	:: "Efficient RK3 - classical version"*OR* |
| ICN_avg_theta | 0.5 | theta of averaged ICN method, usually 0.5 |   0:1 :: "0 <= theta <= 1"*OR* |
| ICN_avg_swapped | "no" | Use swapped averages in ICN method? |  |
| AB_Type | "1" | If using the the AB method, which sort |   "1" :: "same as forward Euler"*OR*  "2" :: "second order"*OR*  "3" :: "third order"*OR*  "4" :: "fourth order"*OR*  "5" :: "fifth order"*OR* |
| AB_initially_reduce_order | "yes" | Reduce order of accuracy initially so that no past timelevels of initial data are required |  |
| MoL_Intermediate_Steps | 3 | Number of intermediate steps taken by the ODE method |   1:*		:: "Anything greater than 1"*OR* |
| MoL_Memory_Always_On | "yes" | Do we keep the scratch arrays allocated all the time? |  |
| MoL_Tiny | 1.e-15 | Effective local machine zero; required by generic solvers |   0:*		:: "Defaults to 1.e-15"*OR* |
| initial_data_is_crap | "no" | If the initial data routine fails to set up the previous time levels, copy the current backwards |  |
| run_MoL_PostStep_in_Post_Recover_Variables | "yes" | Schedule the PostStep parts after recovery so that symmetries are automatically done correctly. |  |
| set_ID_boundaries | "yes" | Should boundaries be overwritten (via synchronization, prolongation, boundary conditions) by MoL? |  |
| Generic_Method_Descriptor | "GenericIntermediateSteps | A string used to create a table containing the description of the generic method |   ".*"          :: "Should contain the Alpha and Beta arrays, and the number of intermediate steps"*OR* |
| MoL_NaN_Check | "no" | Should the RHS GFs be checked for NaNs? |  |
| disable_prolongation | "yes" | If Mesh refinement is enabled should we use buffer zones in intermediate steps? |  |
| skip_initial_copy | "no" | Skip initial copy from previous to current time level |  |
| init_RHS_zero | "yes" | Initialise the RHS to zero |  |
| adaptive_stepsize | "no" | Choose the time step size adaptively |  |
| maximum_absolute_error | 1.0e-6 | Maximum allowed absolute error for adaptive stepsize control |   0.0:*) :: ""*OR* |
| maximum_relative_error | 1.0e-6 | Maximum allowed relative error for adaptive stepsize control |   0.0:*) :: ""*OR* |
| RHS_error_weight | 1.0 | Weight of the RHS in the relative error calculation |   0.0:* :: "should be between zero and one"*OR* |
| safety_factor | 0.9 | Safety factor for stepsize control |   (0.0:*) :: "should be less than one"*OR* |
| maximum_decrease | 10.0 | Maximum stepsize decrease factor |   (1.0:*) :: "should be larger than one"*OR* |
| maximum_increase | 5.0 | Maximum stepsize increase factor |   (1.0:*) :: "should be larger than one"*OR* |
| verbose | "normal" | How verbose should MoL be? |   "none"     :: "No output at all (not implemented)"*OR*  "normal"   :: "Standard verbosity"*OR*  "register" :: "List the variables registered as well"*OR*  "extreme"  :: "Everything you never wanted to know"*OR* |


### SpaceMask

Cactus Code Thorn SpaceMask
Author(s)    : Denis Pollney
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Provides utilities for assigning states to individual grid points
using a mask grid function.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| use_mask | "no" | Turn on storage for mask? |  |


### Norms

CVS info   : $Header$

Cactus Code Thorn Norms
Thorn Author(s)     : Frank Herrmann <herrmann@aei.mpg.de>
                    : Erik Schnetter <schnetter@aei.mpg.de>
Thorn Maintainer(s) : Frank Herrmann <herrmann@aei.mpg.de>
                    : Erik Schnetter <schnetter@aei.mpg.de>
--------------------------------------------------------------------------

Purpose of the thorn:

Compute norms of the state vector, e.g. for Mexico tests.  These are
special norms that make a difference between variables which have a
second derivative taken and variables which have not; see
gr-qc/0503056.  These norms include the gradients of those variables
that have a second derivative taken.  In doing so, they take all
wavelenghts into account -- in contrast, a norm that would be based
simply on using centered differences to calculate gradients would not.



References:

Gioel Calabrese, Ian Hinder, Sascha Husa: Numerical stability for
finite difference approximations of Einstein's equations,
gr-qc/0503056 (2005)


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| max_nr_vars | 100 | maximum number of variables for which norms to compute |   0:* :: "positive"*OR* |
| verbose | 0 | verbosity of output |   0:9 :: "higher number means more verbose output"*OR* |
| gridfunctions_1st | "" | Set of grid functions to compute norms for 2nd order variables |   ".*" :: "List of grid function names"*OR* |
| gridfunctions_2nd | "" | Set of grid functions to compute norms for 2nd order variables |   ".*" :: "List of grid function names"*OR* |


### ReflectionSymmetry

Cactus Code Thorn ReflectionSymmetry
Author(s)    : Erik Schnetter <schnetter@cct.lsu.edu>
Maintainer(s): Erik Schnetter <schnetter@cct.lsu.edu>
Licence      : GPL
--------------------------------------------------------------------------

1. Purpose

Provide reflection symmetries, i.e., bitant, quadrant, and octant mode.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "no" | Produce screen output while applying boundary conditions |  |
| reflection_x | "no" | Reflection symmetry at the lower x boundary |  |
| reflection_y | "no" | Reflection symmetry at the lower y boundary |  |
| reflection_z | "no" | Reflection symmetry at the lower z boundary |  |
| reflection_upper_x | "no" | Reflection symmetry at the upper x boundary |  |
| reflection_upper_y | "no" | Reflection symmetry at the upper y boundary |  |
| reflection_upper_z | "no" | Reflection symmetry at the upper z boundary |  |
| avoid_origin_x | "yes" | Stagger about the origin on the lower x boundary? |  |
| avoid_origin_y | "yes" | Stagger about the origin on the lower y boundary? |  |
| avoid_origin_z | "yes" | Stagger about the origin on the lower z boundary? |  |
| avoid_origin_upper_x | "yes" | Stagger about the origin on the upper x boundary? |  |
| avoid_origin_upper_y | "yes" | Stagger about the origin on the upper y boundary? |  |
| avoid_origin_upper_z | "yes" | Stagger about the origin on the upper z boundary? |  |


## EinsteinEvolve

### NewRad

Cactus Code Thorn NewRad
Author(s)    : Erik Schnetter <schnetter@cct.lsu.edu>
Maintainer(s): Erik Schnetter <schnetter@cct.lsu.edu>
Licence      : GPL
--------------------------------------------------------------------------

1. Purpose

Implement the "NewRad" radiative boundary conditions known from
BSSN_MoL.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| z_is_radial | "no" | Consider the local z direction to be the radial one; use with Llama |  |


### GRHydro_InitData

Cactus Code Thorn GRHydro_Init_Data  
Author(s)    : Luca Baiotti
               Ian Hawke
               Scott Hawley
               Frank Löffler
               Christian David Ott
Maintainer(s): CIGR developers team
Licence      : GPLv2+
--------------------------------------------------------------------------

1. Purpose

Provides simple initial data (shock tubes, ...) for the GRHydro code.



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| initial_hydro | "xshock" |  |   "shocktube" :: "Shocktube type"*OR*  "shocktube_hot" :: "Shocktube with hot nuclear EOS"*OR*  "only_atmo"  :: "Set only a low atmosphere"*OR*  "read_conformal":: "After reading in initial alp, rho and gxx from h5 files, sets the other quantities"*OR*  "simple_wave" :: "Set initial data from Anile Miller Motta, Phys.Fluids. 26, 1450 (1983)"*OR*  "monopole"    :: "Monopole at the center"*OR*  "cylexp"      :: "Cylindrical Explosion"*OR*  "rotor"       :: "Magnetic Rotor test from DelZanna,Bucciantini, and Londrillo A&A 400, 397-413 (2003)"*OR*  "advectedloop":: "Magnetic advected loop test"*OR*  "alfvenwave"  :: "Circularly polarized Alfven wave"*OR*  "hydro_bondi_solution" :: "Spherical single black hole Bondi solution"*OR*  "hydro_bondi_solution_iso" :: "Spherical single black hole Bondi solution - TEST ISO CASE!!!!!!"*OR*  "magnetized_bondi_solution" :: "Magnetized Spherical single black hole Bondi solution"*OR*  "magnetized_bondi_solution_iso" :: "Magnetized Spherical single black hole Bondi solution - TEST ISO CASE!!!!!!"*OR*}*OR**OR*EXTENDS KEYWORD initial_Bvec*OR*{*OR*  "shocktube"  :: "Shocktube type"*OR*  "cylexp"     :: "Poloidal Magnetic Field"*OR*  "poloidalmagfield" :: "Poloidal Magnetic Field"*OR*  "magnetized Bondi" :: "radial magnetic field appropriate for Bondi test"*OR*}*OR**OR*EXTENDS KEYWORD initial_Avec*OR*{*OR*  "poloidalmagfield" :: "Poloidal Magnetic Field"*OR*}*OR**OR*EXTENDS KEYWORD initial_entropy*OR*{*OR*  "magnetized Bondi" :: "Initial entropy for a radial magnetic field appropriate for Bondi test"*OR*}*OR**OR**OR*shares:ADMBase*OR**OR*EXTENDS KEYWORD initial_data ""*OR*{*OR*#  "shocktube"    :: "Shock tube initial data for GRHydro"*OR*  "con2primtest"  :: "Testing the con -> prim conversion"*OR*  "con2prim2con_test"   :: "Testing the con -> prim -> con conversion"*OR*  "prim2con2prim_test"  :: "Testing the prim -> con -> prim conversion"*OR*  "prim2con2prim_polytype_test"  :: "Testing the prim -> con -> prim conversion - polytype version"*OR*  "reconstruction_test" :: "Testing reconstruction"*OR*}*OR**OR*private:*OR**OR*KEYWORD shocktube_type "Diagonal or parallel shock?"*OR*{*OR*  "diagshock"  :: "Diagonal across all axes"*OR*  "diagshock2d"   :: "Diagonal across x-y axes"*OR*  "xshock"  :: "Parallel to x axis"*OR*  "yshock"  :: "Parallel to y axis"*OR*  "zshock"  :: "Parallel to z axis"*OR*  "sphere"  :: "spherically symmetric shock"*OR* |
| shock_case | "Sod" | Simple, Sod's problem or other? |   "Simple"  :: "GRAstro_Hydro test case"*OR*  "Sod"     :: "Sod's problem"*OR*  "Blast"   :: "Strong blast wave"*OR*  "Balsaralike1" :: "Hydro version of Balsara Test #1"*OR*  "Balsara0"    :: "Balsara Test #1, but unmagnetized"*OR*  "Balsara1"    :: "Balsara Test #1"*OR*  "Balsara2"    :: "Balsara Test #2"*OR*  "Balsara3"    :: "Balsara Test #3"*OR*  "Balsara4"    :: "Balsara Test #4"*OR*  "Balsara5"    :: "Balsara Test #5"*OR*  "Alfven"      :: "Generical Alfven Test"*OR*  "Komissarov1" :: "Komissarov Test #1"*OR*  "Komissarov2" :: "Komissarov Test #2"*OR*  "Komissarov3" :: "Komissarov Test #3"*OR*  "Komissarov4" :: "Komissarov Test #4"*OR*  "Komissarov5" :: "Komissarov Test #5"*OR*  "Komissarov6" :: "Komissarov Test #6"*OR*  "Komissarov7" :: "Komissarov Test #7"*OR*  "Komissarov8" :: "Komissarov Test #8"*OR*  "Komissarov9" :: "Komissarov Test #9"*OR* |
| shock_xpos | 0.0 | Position of shock plane: x |   *:*  :: "Anything"*OR* |
| shock_ypos | 0.0 | Position of shock plane: y |   *:*  :: "Anything"*OR* |
| shock_zpos | 0.0 | Position of shock plane: z |   *:*  :: "Anything"*OR* |
| shock_radius | 1.0 | Radius of sperical shock |   0.0:*   :: "Anything positive"*OR* |
| change_shock_direction | 0.3 | Change the shock direction |   0:1     :: "It is the sound speed where the fluid velocity is zero"*OR* |
| simple_wave_v_max | 0.7 | The v_max constant in Anile Miller Motta, Phys.Fluids. 26, 1450 (1983) |   0:1     :: "It is the maximum velocity in the initial configuration (see p. 1457, bottom of first column)"*OR* |
| attenuate_atmosphere | "no" | Attenuate the velocity in the atmosphere |  |
| Bx_init | 0.0 | Initial B-field in the x-dir |   *:*     :: "Anything"*OR* |
| By_init | 0.0 | Initial B-field in the y-dir |   *:*     :: "Anything"*OR* |
| Bz_init | 0.0 | Initial B-field in the z-dir |   *:*     :: "Anything"*OR* |
| rho_init | 1.0d-6 | Initial rest mass density |  (0:*    :: "Anything positive."*OR* |
| velx_init | 1.0d-1 | Initial x velocity |  *:*    :: "Anything."*OR* |
| vely_init | 1.0d-1 | Initial y velocity |  *:*    :: "Anything."*OR* |
| velz_init | 1.0d-1 | Initial z velocity |  *:*    :: "Anything."*OR* |
| eps_init | 1.0d-6 | Initial specific internal energy |  (0:*    :: "Anything positive."*OR* |
| press_init | 6.666666666666667d-7 | Initial pressure |  (0:*    :: "Anything positive."*OR* |
| use_c2p_with_entropy_eqn | no | Use the con2prim routine that uses the entropy equation instead of the energy equation |  |
| dens_init | 1.29047362 | Initial conserved mass density |  (0:*    :: "Anything positive."*OR* |
| sx_init | 0.166666658 | Initial x component of conserved momentum density |  *:*    :: "Anything."*OR* |
| sy_init | 0.166666658 | Initial y component of conserved momentum density |  *:*    :: "Anything."*OR* |
| sz_init | 0.166666658 | Initial z component of conserved momentum density |  *:*    :: "Anything."*OR* |
| tau_init | 0.484123939 | Initial conserved total energy density |  (0:*    :: "Anything positive."*OR* |
| gxx_init | 1.0 | Initial xx metric componenent |  *:*    :: "Anything, but be carefull to set a positive definite 3-metric!"*OR* |
| gxy_init | 0.0 | Initial xy metric componenent |  *:*    :: "Anything, but be carefull to set a positive definite 3-metric!"*OR* |
| gxz_init | 0.0 | Initial xz metric componenent |  *:*    :: "Anything, but be carefull to set a positive definite 3-metric!"*OR* |
| gyy_init | 1.0 | Initial yy metric componenent |  *:*    :: "Anything, but be carefull to set a positive definite 3-metric!"*OR* |
| gyz_init | 0.0 | Initial yz metric componenent |  *:*    :: "Anything, but be carefull to set a positive definite 3-metric!"*OR* |
| gzz_init | 1.0 | Initial zz metric componenent |  *:*    :: "Anything, but be carefull to set a positive definite 3-metric!"*OR* |
| monopole_type | "Point" | Which kind of monopole? |   "Point" :: "Single point with Bx /= 0"*OR*  "Gauss" :: "Gaussian w/radius R_Gauss"*OR*  "1dalt" :: "1-d alternating"*OR*  "2dalt" :: "2-d alternating"*OR*  "3dalt" :: "3-d alternating"*OR* |
| R_Gauss | 1.0 | Radius for a Gaussian monopole |   0:* :: "Any positive number"*OR* |
| Monopole_point_Bx | 1.0 | Pointlike Monopole Bx value |   *:* :: "Any number"*OR* |
| cyl_r_inner | 0.8 | Inner Radius |   (0:* :: "Any positive number"*OR* |
| cyl_r_outer | 1.0 | Outer Radius |   (0:* :: "Any positive number"*OR* |
| cyl_rho_inner | 1.d-2 | density in inner core |   (0:* :: "any positive number"*OR* |
| cyl_press_inner | 1.d0 | pressure in inner core |   (0:* :: "any positive number"*OR* |
| cyl_rho_outer | 1.d-4 | density in outer region |   (0:* :: "any positive number"*OR* |
| cyl_press_outer | 3.d-5 | pressure in outer region |   (0:* :: "any positive number"*OR* |
| advectedloop_type | "2D" | 2-dimensional or 3-dimensional? |   "2D"  :: "2-dimensional (B^z=0)"*OR*  "3D"  :: "3-dimensional (B^3=0, where B^3 || oblique cylinder axis."*OR* |
| advectedloop_case | "V^z=0" | V^z=0 or not? |   "V^z=0"  :: "Useful to evaluate divB deviations"*OR*  "V^z/=0" :: "Useful to evaluate con2prim robustness in keeping V^z const."*OR* |
| advectedloop_delA | "Exact" | How to calculate B^i field from the potential A^b |   "Exact"   :: "Analytic, exact closed formula applied"*OR*  "Numeric" :: "Finite difference approximation of the derivatives applied"*OR* |
| alfvenwave_type | "1D" | 1-dimensional or 2-dimensional? |   "1D"  :: "1-dimensional"*OR*  "2D"  :: "2-dimensional (in x-y plane)"*OR* |
| alfvenwave_pressure | 1.0 | P_gas for the Alfven wave |   (0:* :: "positive"*OR* |
| mdot_sonicpt_bondi | 12.566370614359172954 | Accretion rate at sonic point in hydro units |   (0:* :: "positive"*OR* |
| bondi_bmag | 0.01 | B_0 parameter for magnetized Bondi |   0:* :: "Anything positive"*OR* |
| bondi_radial_offset | 0.0 | redefine r_grid=r_KS-r0 to avoid singularity on grid |   0:* :: "Any positive number"*OR* |
| set_bondi_beta_sonicpt | no | Set plasma beta parameter instead of bondi_bmag |  |
| bondi_Bvec_method | "direct" | how to compute the magnetic field vector |   "direct"    :: "directly from Cartesian metric"*OR*  "transform" :: "transform Schwarzschild solution to Kerr Schild"*OR* |
| bondi_evolve_only_annulus | "no" | reset to initial data outside of bondi_freeze_inner_radius and bondi_freeze_outer_radius |  |
| bondi_freeze_inner_radius | -1. | reset to initial at radii below this |   *:* :: "any value"*OR* |
| bondi_freeze_outer_radius | 1e300 | reset to initial at radii above this |   *:* :: "any value"*OR* |
| bondi_overwrite_boundary | "no" | reset data to initial data in outer boundary in boundary condition |  |
| poloidal_P_p | 1 | Index of pressure factor |  (0:* :: "Any non-negative integer"*OR* |
| rotor_xc | 0.5 | center of rotation |   *:* :: "Any location"*OR* |
| rotor_yc | 0.5 | center of rotation |   *:* :: "Any location"*OR* |
| rotor_bvcxl | 1.0 | intial component of Bvec[0] |   *:* :: "any real number"*OR* |
| rotor_bvcyl | 0.0 | intial component of Bvec[1] |   *:* :: "any real number"*OR* |
| rotor_bvczl | 0.0 | intial component of Bvec[2] |   *:* :: "any real number"*OR* |
| rotor_r_rot | 0.1 | radius of rotor |   (0:* :: "any positive number"*OR* |
| rotor_v_max | 0.995 | Maximum velocity |   (-1:1) :: "any subluminal speed (negative is clockwise)"*OR* |
| rotor_rhoin | 10.d0 | initial density inside rotor |   (0:* :: "any positive number"*OR* |
| rotor_pressin | 1.d0 | initial pressure inside rotor |   (0:* :: "any positive number"*OR* |
| rotor_rhoout | 1.d0 | initial density outside rotor |   (0:* :: "any positive number"*OR* |
| rotor_pressout | 1.d0 | initial pressure outside rotor |   (0:* :: "any positive number"*OR* |
| rotor_use_smoothing | yes | Smooth the edge? |  |
| rotor_rsmooth_rel | 0.05 | Define the radius in relative terms if so |   (0:* :: "any positive number"*OR* |


### GRHydro

Cactus Code Thorn GRHydro
Author(s)    : Luca Baiotti
               Ian Hawke
               Pedro Montero
               Sebastiano Bernuzzi
               Giovanni Corvino
               Toni Font
               Joachim Frieben
               Roberto De Pietri
               Thorsten Kellermann
               Frank Löffler
               Christian D. Ott
               Luciano Rezzolla
               Nikolaos Stergioulas
               Aaryn Tonita
               many others
Maintainer(s): CIGR developers team
Licence      : GPLv2+
--------------------------------------------------------------------------

1. Purpose

GRHydro is the evolution code for a general-purpose 3D relativistic
hydrodynamics code. To work it requires Einstein, MoL (the method of
lines thorn), CactusEOS (the general equation of state interface).
In theory any (suitably written) spacetime evolution thorn should work,
but for the moment the MoL version of BSSN will be the tested version.

For more information see the documentation in the ThornGuide.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| metric_type | "never" |  | }*OR* *OR*EXTENDS KEYWORD shift_evolution_method ""*OR*{*OR*  "Comoving" :: "Set the shift so the matter is approximately comoving"*OR*}*OR**OR**OR*#########################################*OR*###  PARAMETRES SHARED FROM HydroBase ###*OR*#########################################*OR**OR*shares: HydroBase*OR**OR*USES CCTK_INT timelevels*OR*USES KEYWORD prolongation_type*OR*USES INT hydro_excision*OR*EXTENDS KEYWORD evolution_method ""*OR*{*OR*  "GRHydro" :: "Use GRHydro to evolve the hydro variables"*OR*}*OR*EXTENDS KEYWORD Bvec_evolution_method ""*OR*{*OR*  "GRHydro"      :: "Use GRHydro to evolve the MHD variables, evolving the Magnetic Field"*OR*  "GRHydro_Avec" :: "Use GRHydro to evolve the MHD variables, evolving the Vector Potential"*OR*}*OR*EXTENDS KEYWORD Y_e_evolution_method ""*OR*{*OR*  "GRHydro" :: "Use GRHydro to evolve Y_e"*OR*}*OR*EXTENDS KEYWORD temperature_evolution_method ""*OR*{*OR*  "GRHydro" :: "Use GRHydro to evolve temperature"*OR*}*OR*EXTENDS KEYWORD entropy_evolution_method ""*OR*{*OR*  "GRHydro" :: "Use GRHydro to evolve entropy"*OR*}*OR*EXTENDS KEYWORD initial_Bvec ""*OR*{*OR*  "Bvec_from_Avec" :: "Calculate B^i for an initially specified A_i"*OR*}*OR**OR*#########################################*OR*###  PARAMETRES SHARED FROM MoL       ###*OR*#########################################*OR**OR*shares: MethodOfLines*OR**OR*USES CCTK_INT MoL_Num_Evolved_Vars*OR*USES CCTK_INT MoL_Num_Evolved_Vars_Slow*OR*USES CCTK_INT MoL_Num_Constrained_Vars*OR*USES CCTK_INT MoL_Num_SaveAndRestore_Vars*OR*USES CCTK_INT MoL_Max_Evolved_Array_Size *OR*USES CCTK_INT MoL_Num_ArrayEvolved_Vars*OR**OR*shares:SpaceMask*OR**OR*USES boolean use_mask*OR**OR*#########################################*OR*###  PARAMETRES SHARED FROM EOS_Omni  ###*OR*#########################################*OR**OR*SHARES: EOS_Omni*OR**OR*USES REAL poly_k*OR**OR*restricted:*OR**OR*KEYWORD use_evolution_mask "Set this to 'always' to skip validity tests in regions where CarpetEvolutionMask::evolution_mask vanishes." STEERABLE=ALWAYS*OR*{*OR*  "always" :: "use the mask"*OR*  "auto"   :: "check if CarpetEvolutionMask is active, then use the mask"*OR*  "never"  :: "do not use the mask"*OR* |
| GRHydro_enable_internal_excision | "true" | Set this to 'false' to disable the thorn-internal excision. |  |
| GRHydro_hydro_excision | 1 | Turns excision automatically on in HydroBase |   1:1 :: "Only '1' allowed"*OR* |
| sources_spatial_order | 2 | Order of spatial differencing of the source terms | 2 :: "2nd order finite differencing"*OR*4 :: "4th order finite differencing"*OR* |
| GRHydro_MaxNumEvolvedVars | 5 | The maximum number of evolved variables used by GRHydro |   0             :: "when using multirate"*OR*  5:12		:: "dens scon[3] tau (B/A)vec[3] psidc ye entropy Aphi"*OR* |
| GRHydro_MaxNumEvolvedVarsSlow | 0 | The maximum number of evolved variables used by GRHydro |   0             :: "do not use multirate"*OR*  5:12		:: "dens scon[3] tau (B/A)vec[3] psidc ye entropy Aphi"*OR* |
| GRHydro_MaxNumConstrainedVars | 37 | The maximum number of constrained variables used by GRHydro |   7:48		:: "A small range, depending on testing or not"*OR* |
| GRHydro_MaxNumSandRVars | 16 | The maximum number of save and restore variables used by GRHydro |   0:16		:: "A small range, depending on testing or not"*OR* |
| GRHydro_hot_atmo_temp | 0.1e0 | Temperature of the hot atmosphere in MeV |  (0.0:*   :: "Larger than 0 MeV"*OR* |
| GRHydro_max_temp | 90.0e0 | maximum temperature we allow |  (0.0:*   :: "Larger than 0 MeV"*OR* |
| GRHydro_hot_atmo_Y_e | 0.5e0 | Y_e of the hot atmosphere |  0.0:*   :: "Larger than 0"*OR* |
| GRHydro_Y_e_min | 0.0 | minimum allowed Y_e |  0.0:*   :: "Larger than or equal to zero"*OR* |
| GRHydro_Y_e_max | 1.0 | maximum allowed Y_e |  0.0:*   :: "Larger than or equal to zero; 1 is default"*OR* |
| sqrtdet_thr | -1.0 | Threshold to apply cons rescalings deep inside the horizon |  1.0:    :: "Larger values guarantees this sort of rescaling only deep inside the horizon"*OR* -1.0    :: "Do not apply limit"*OR* |
| max_magnetic_to_gas_pressure_ratio | -1.0 | consider pressure to be magnetically dominated if magnetic pressure to gas pressure ratio is higher than this |   (0:*  :: "any positive value, eg. 100."*OR*  -1.0  :: "disable"*OR* |
| GRHydro_rho_central | 1.e-5 | Central Density for Star |   : :: ""*OR* |
| tau_rel_min | 1.e-10 | A minimum relative tau (taumin = maxtau(t=0) * tau_rel_min) below which tau is reschaled |   0: :: ""*OR* |
| rho_abs_min | -1.0 | A minimum rho below which evolution is turned off (atmosphere). If negative, the relative minimum will be used instead. |   -1.0: :: ""*OR* |
| rho_rel_min | 1.e-9 | A minimum relative rho (rhomin = centden * rho_rel_min) below which evolution is turned off (atmosphere). Only used if rho_abs_min < 0.0 |   0: :: ""*OR* |
| initial_rho_abs_min | -1.0 | An absolute value for rho in the atmosphere. To be used by initial data routines only. Unused if negative. |   -1.0: :: ""*OR* |
| initial_rho_rel_min | -1.0 | A relative (to the central density) value for rho in the atmosphere. To be used by initial data routines only. Unused if negative. |   -1.0: :: ""*OR* |
| initial_atmosphere_factor | -1.0 | A relative (to the initial atmosphere) value for rho in the atmosphere. This is used at initial time only. Unused if negative. |   -1.0: :: ""*OR* |
| rho_abs_min_after_recovery | -1.0 | A new absolute value for rho in the atmosphere. To be used after recovering. Unused if negative. |   -1.0: :: ""*OR* |
| GRHydro_atmo_tolerance |  | A point is set to atmosphere in the Con2Prim's if its rho < GRHydro_rho_min *(1+GRHydro_atmo_tolerance). This avoids occasional spurious oscillations in carpet buffer zones lying in the atmosphere (because prolongation happens on conserved variables) |    0.0: :: "Zero or larger. A useful value could be 0.0001"*OR* |
| atmo_falloff_radius | 50.0 | The radius for which the atmosphere starts to falloff as (atmo_falloff_radius/r)**atmo_falloff_power |   0:* :: "Anything positive"*OR* |
| atmo_falloff_power | 0.0 | The power at which the atmosphere level falls off as (atmo_falloof_radius/r)**atmo_falloff_power |   0:* :: "Anything positive"*OR* |
| atmo_tolerance_radius | 50.0 | The radius for which the atmosphere tolerance starts to increase as (r/atmo_tolerance_radius)**atmo_tolerance_power |   0:* :: "Anything positive"*OR* |
| atmo_tolerance_power | 0.0 | The power at which the atmosphere tolerance increases as (r/atmo_tolerance_radius)**atmo_tolerance_power |   0:* :: "Anything positive"*OR* |
| wk_atmosphere | "no" | Use some of Wolfgang Kastauns atmosphere tricks |  |
| Check_Rho_Minimum | "no" | Should a check on rho < GRHydro_rho_min be performed and written as WARNING level 2? |  |
| use_weighted_fluxes | "no" | Weight the flux terms by the cell surface areas |  |
| particle_interpolator | "Lagrange | What interpolator should be used for the particles |   ".+"          :: "A valid interpolator name"*OR* |
| particle_interpolation_order | 2 | What order should be used for the particle interpolation |   1:*           :: "A valid positive interpolation order"*OR* |
| GRHydro_NaN_verbose | 2 | The warning level for NaNs occuring within GRHydro |   0:* :: "The warning level"*OR* |
| GRHydro_lorentz_overshoot_cutoff | 1.e100 | Set the Lorentz factor to this value in case it overshoots (1/0) |   0:* :: "Some big value"*OR* |
| kap_dc | 10.0 | The kap parameter for divergence cleaning |   0:* :: "Any non-negative value, but 1.0 to 10.0 seems preferred"*OR* |
| psidcspeed | "light | Which speed to set for psidc |   "char speed"  :: "Based on the characteristic speeds"*OR*  "light speed" :: "Set the characteristic speeds to speed of light"*OR*  "set speed"   :: "Manually set the characteristic speeds: [setcharmin,setcharmax]"*OR* |
| setcharmax | 1.0 | Maximum characteristic speed for psidc if psidcspeed is set |   0:1 :: "Any value smaller than speed of light"*OR* |
| setcharmin | -1.0 | Minimum characteristic speed for psidc if psidcspeed is set |   -1:0 :: "Any value smaller than speed of light - sign should be negative"*OR* |
| decouple_normal_Bfield | yes | when using divergence cleaning properly decouple Bx,psidc subsystem |  |
| Avec_gauge | "lorenz" | Which gauge condition to use when evolving the vector potential |   "algebraic"  :: "Algebraic gauge"*OR*  "lorenz"     :: "Lorenz gauge"*OR* |
| Tmunu_damping_radius_min | -1 | damping radius at which we start to damp with a tanh function |    -1  :: "damping switched off"*OR*   0:* :: "damping radius at which we start to damp"*OR* |
| Tmunu_damping_radius_max | -1 | damping radius at which Tmunu becomes 0 |    -1  :: "damping switched off"*OR*   0:* :: "greater than minimum radius above"*OR* |
| sync_conserved_only | no | Only sync evolved conserved quantities during evolution. |  |
| use_MoL_slow_multirate_sector | no | Whether to make use of MoL's slow multirate sector |  |
| verbose | no | Some debug output |  |
| constrain_to_1D | no | Set fluid velocities to zero for non-radial motion |  |
| use_cxx_code | yes | Use experimental C++ code? |  |


## CactusPUGH

### PUGH

Cactus Code Thorn PUGH
Author(s)    : Gabrielle Allen
               Tom Goodale
               Thomas Radke
               Matei Ripeanu
               Paul Walker
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn provides a unigrid parallel driver with MPI.



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| periodic | "no" | Periodic boundary conditions |  |
| periodic_x | "yes" | Periodic boundary conditions in x-direction |  |
| periodic_y | "yes" | Periodic boundary conditions in y-direction |  |
| periodic_z | "yes" | Periodic boundary conditions in z-direction |  |
| global_nx | 10 | The size of the grid in the x direction |     0:*     :: "Grid of this size distributed across all processors"*OR* |
| global_ny | 10 | The size of the grid in the y direction |     0:*     :: "Grid of this size distributed across all processors"*OR* |
| global_nz | 10 | The size of the grid in the z direction |     0:*     :: "Grid of this size distributed across all processors"*OR* |
| global_nsize | -1 | The size of the grid in each spatial direction |     -1:*     :: "Grid of this size in each dir distributed across all processors"*OR* |
| ghost_size_x | 1 | The width of the ghost zone in the x direction |     0:*     :: "Must be a positive integer"*OR* |
| ghost_size_y | 1 | The width of the ghost zone in the y direction |     0:*     :: "Must be a positive integer"*OR* |
| ghost_size_z | 1 | The width of the ghost zone in the z direction |     0:*     :: "Must be a positive integer"*OR* |
| ghost_size | -1 | The width of the ghost zone in each direction |     -1:*     :: "Any positive number to override the ghost_size_[xyz] parameters"*OR* |
| info | "none" | Provide additional information about what PUGH is doing |   "none" :: "No extra information"*OR*  "load" :: "Load on each processor"*OR* |
| local_nx | -1 | The size of the grid in the x direction |     -1:*     :: "Grid of this size on each processor"*OR* |
| local_ny | -1 | The size of the grid in the y direction |     -1:*     :: "Grid of this size on each processor"*OR* |
| local_nz | -1 | The size of the grid in the z direction |     -1:*     :: "Grid of this size on each processor"*OR* |
| local_nsize | -1 | The size of the grid in each spatial direction |     -1:*     :: "Grid of this size in each dir on each processor"*OR* |
| local_size_includes_ghosts | "yes" | Does the local grid size include the ghost zones? |  |
| enable_all_storage | "no" | Enable storage for all GFs? |  |
| physical2logical | "direct" | Physical process to logical process mapping method to use |   "direct"  :: "Maps MPI IDs directly to IJKs"*OR*  "example" :: "Maps MPI IDs directly to IJKs using a lookup table"*OR* |
| processor_topology | "automatic" | How to determine the processor topology |   "manual"        :: "Specified by proc_top_nx etc"*OR*  "automatic"     :: "Automatically generated"*OR*  "automatic_old" :: "Automatically generated (old method)"*OR* |
| processor_topology_1d_x | 0 | Number of processors in X direction |   0:*  :: "See proc_topology"*OR* |
| processor_topology_2d_x | 0 | Number of processors in X direction |   0:*  :: "See proc_topology"*OR* |
| processor_topology_2d_y | 0 | Number of processors in Y direction |   0:*  :: "See proc_topology"*OR* |
| processor_topology_3d_x | 0 | Number of processors in X direction |   0:*  :: "See proc_topology"*OR* |
| processor_topology_3d_y | 0 | Number of processors in Y direction |   0:*  :: "See proc_topology"*OR* |
| processor_topology_3d_z | 0 | Number of processors in Z direction |   0:*  :: "See proc_topology"*OR* |
| initialize_memory | "none" | How to initialize memory for grid variables at allocation time |   "none" :: "Do not initialize storage for allocated grid variables (default)"*OR*  "zero" :: "Zero out all elements of all allocated grid variables"*OR*  "NaN"  :: "Set all elements of allocated floating point grid variables to Not-a-Number values"*OR* |
| partition | "automatic" | Is the partition manual |  "automatic"    :: "even"*OR* "manual"       :: "specified by partition_XYZ .."*OR* |
| partition_1d_x | "" | Tells how to partition on direction X | .* :: "A regex which matches anything"*OR* |
| partition_2d_x | "" | Tells how to partition on direction X | .* :: "A regex which matches anything"*OR* |
| partition_2d_y | "" | Tells how to partition on direction y | .* :: "A regex which matches anything"*OR* |
| partition_3d_x | "" | Tells how to partition on direction X | .* :: "A regex which matches anything"*OR* |
| partition_3d_y | "" | Tells how to partition on direction y | .* :: "A regex which matches anything"*OR* |
| partition_3d_z | "" | Tells how to partition on direction z | .* :: "A regex which matches anything"*OR* |
| storage_verbose | "no" | Report on memory assignment |   "yes"    :: "Standard storage information"*OR*  "report" :: "Provide a report of storage every storage_report_every iterations and at termination"*OR*  "no"     :: "Provide no information"*OR* |
| storage_report_every | 0 | How often to provide a report on storage information |   0:0 :: "Never report"*OR*  1:* :: "Report at intervals"*OR* |
| cacheline_mult | 4001 | Multiplier for cacheline number |   0:*  ::  "Any positive number"*OR* |
| overloadevolve | "yes" | Overload Evolve driver function |  |
| overloadsyncgroup | "no" | Overload SyncGroup driver function |  |
| overloadsyncgroupsbydiri | "yes" | Overload SyncGroupsByDirI driver function |  |
| overloadenablegroupstorage | "yes" | Overload EnableGroupStorage driver function |  |
| overloaddisablegroupstorage | "yes" | Overload DisableGroupStorage driver function |  |
| overloadenablegroupcomm | "yes" | Overload EnableGroupComm driver function |  |
| overloaddisablegroupcomm | "yes" | Overload DisableGroupComm driver function |  |
| overloadbarrier | "yes" | Overload Barrier driver function |  |
| overloadparallelinit | "yes" | Overload ParallelInit driver function |  |
| overloadexit | "yes" | Overload Exit driver function |  |
| overloadabort | "yes" | Overload Abort driver function |  |
| overloadmyproc | "yes" | Overload MyProc driver function |  |
| overloadnprocs | "yes" | Overload nProcs driver function |  |
| overloadarraygroupsizeb | "yes" | Overload ArrayGroupSizeB driver function |  |
| overloadquerygroupstorageb | "yes" | Overload QueryGroupStorageB driver function |  |
| overloadgroupdynamicdata | "yes" | Overload GroupDynamicData driver function |  |


### PUGHInterp

Cactus Code Thorn PUGHInterp
Author(s)    : Paul Walker
               Thomas Radke
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

   This thorn implements the Cactus flesh interpolation API routine
   CCTK_InterpGridArrays() which provides interpolation of CCTK grid
   arrays at arbitrary points.


2. Additional Information

   More detailed information on thorn PUGHInterp is given in chapter
   about PUGHInterp in the Cactus ThornGuide (see doc/documentation.tex).

   For information on how to invoke interpolation operators via the
   general flesh interpolation API please refer to the flesh documentation.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### PUGHReduce

Cactus Code Thorn PUGHReduce
Author(s)    : Gabrielle Allen
               Tom Goodale
               Thomas Radke
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Standard reduction operators which use PUGH for communications


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### PUGHSlab

Cactus Code Thorn PUGHSlab
Author(s)    : Gabrielle Allen
               Tom Goodale
               Thomas Radke
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn implements the following parts of the CCTK hyperslab API:

 * the definition of local and global hyperslab mappings based on grid indices
 * the extraction of local and global hyperslabs as specified by such
   mappings


2. Additional information

The complete specification of the CCTK Hyperslab API and the description of
PUGHSlab's implementation of it are described in the corresponding ThornGuide
chapter for PUGHSlab (see file doc/documentation.tex).


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


## Numerical

### AEILocalInterp

CVS info   : $Header$

Cactus Code Thorn AEILocalInterp
Thorn Author(s)     : Jonathan Thornburg <jthorn@aei.mpg.de>
Thorn Maintainer(s) : Jonathan Thornburg <jthorn@aei.mpg.de>
--------------------------------------------------------------------------

Purpose of the thorn
====================

This thorn does processor-local interpolation of N-dimensional data
arrays, using the CCTK_InterpLocalUniform() API.  The data arrays
(in general there may be many of them) must be defined on a uniformly
spaced grid.

This thorn provides the interpolation operators
   "Lagrange polynomial interpolation (tensor product)"
   "Lagrange polynomial interpolation (maximum degree)"
   "Hermite polynomial interpolation",
The interpolation operator
   "Lagrange polynomial interpolation"
is also accepted as a synonyms
   "Lagrange polynomial interpolation (tensor product)"
(So is "generalized polynomial interpolation", but that's deprecated
and will probably be removed at some point.)


Documentation
=============

There are 3 main sources of user documentation for this thorn:
- The Cactus Users' Guide, chapter "Application Thorn Writing",
  section "Cactus Application Interfaces", subsection "Interpolation
  Operators" (at present this is section B8.3), describes the general
  Cactus system for interpolation operators.
- The Cactus Users' Guide, chapter "Function Reference", section
  "Cactus Functions", has an entry giving a detailed description of
  the CCTK_InterpLocalUniform() interpolation API which this thorn
  supports.  This is probably the place to start reading; it also
  has a simple example.
- This thorn's Thorn Guide gives a detailed description of the
  interpolation operators this thorn supports, and their mandatory
  and optional parameter-table entries.  The Thorn Guide also
  includes both simple and more complicated examples.


History
=======

This interpolator was written by Jonathan Thornburg in winter 2001-2002.
Between then and July 2003 it lived in CactusBase/LocalInterp (in the
 src/GeneralizedPolynomial-Uniform/  subdirectory), but in July 2003 it
was moved to this (new) thorn so it could stay GPL (Cactus policies forbid
GPL code in the CactusBase arrangement).  See  CactusBase/LocalInterp/
for the older CVS history.


Copyright & Licensing
=====================

This thorn is copyright (C) 2001-2003
by Jonathan Thornburg <jthorn@aei.mpg.de>.

This thorn is free software; you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation; either version 2 of the License, or
(at your option) any later version.

This thorn is distributed in the hope that it will be useful, but
WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this thorn (see the file COPYING in this directory);
if not, write to the Free Software Foundation, Inc., 59 Temple
Place, Suite 330, Boston, MA  02111-1307  USA

See the README files in the individual interpolators' directories
for more information.


Other Interpolation Thorns
==========================

As well as this thorn, there are some other Cactus thorns that also
provide interpolation APIs:

CactusBase/LocalInterp
	This contains an older processor-local interpolator, supporting
	the interpolation operator
	   "uniform cartesian"

There are also driver-specific thorns providing global (grid array)
interpolation, eg. PUGHInterp, CarpetInterp, etc.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


## CactusTest

### TestTimers

Cactus Code Thorn TestTimers
Author(s)    : Cactus team
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn demonstrates and tests the use of Timers in thorns.



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### TestTable

Cactus Code Thorn TestTable
Author(s)    : Jonathan Thornburg
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn contains a test driver for the Cactus key/value tables
API, Util_Table*().


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### TestReduce

Cactus Code Thorn TestReduce
Author(s)    : unknown
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

not documented


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### TestFortranDependencies1

Cactus Code Thorn TestFortranDependencies1
Author(s)    : Erik Schnetter
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Test Fortran intra-thorn and inter-thorn make dependencies.  This
thorn contains the intra-thorn dependencies, and depends on the thorn
TestFortranDependencies2.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### TestCoordinates

Cactus Code Thorn TestCoordinates
Author(s)    : Gabrielle Allen
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Test the Cactus coordinate infrastructure.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| domain | "full" | Domain type |   "octant"   :: "Use an octant about the origin"*OR*  "quadrant" :: "Use a quadrant in x-y plane"*OR*  "bitant"   :: "Use a bitant about the x-y plane"*OR*  "full"     :: "Use the full domain"*OR* |
| bitant_plane | "xy" | Plane defining bitant domain |   "xy"       :: "xy-plane"*OR*  "xz"       :: "xz-plane"*OR*  "yz"       :: "yz-plane"*OR* |


### TestLoop

Cactus Code Thorn TestLoop
Author(s)    : Erik Schnetter <schnetter@cct.lsu.edu>
Maintainer(s): Erik Schnetter <schnetter@cct.lsu.edu>
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

not documented


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### TestInclude2

Cactus Code Thorn TestInclude2
Author(s)    : unknown
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

not documented


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### TestGlobalReduce

Cactus Code Thorn TestGlobalReduce
Author(s)    : Yaakoub Y El Khamra
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

not documented


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| array_nx | 5 | Size of arrays in x direction |   0:* :: ""*OR* |
| array_ny | 4 | Size of arrays in y direction |   0:* :: ""*OR* |
| array_nz | 10 | Size of arrays in z direction |   0:* :: ""*OR* |
| dest_proc | 0 | processor to which reduction is to be made |   *:* :: "number can be any valid integer"*OR* |
| map_x | 10 | number of original points to map to in x-direction |   0:* :: "has to be positive"*OR* |
| map_y | 10 | number of original points to map to in y-direction |   0:* :: "has to be positive"*OR* |
| map_z | 10 | number of original points to map to in z-direction |   0:* :: "has to be positive"*OR* |
| radius | 0.1 | The radius of the gaussian wave |  0:* :: "Positive"*OR* |
| sigma | 0.1 | The sigma for the gaussian wave |  0:* :: "Positive"*OR* |
| amplitude | 1.0 | The amplitude of the waves |   *:* :: "No restriction"*OR* |
| uniform_weight_value | 0.5 | The uniform value of the weight |   *:*  :: "Can be anything"*OR* |


### TestFortranDependencies2

Cactus Code Thorn TestFortranDependencies2
Author(s)    : Erik Schnetter
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Test Fortran intra-thorn and inter-thorn make dependencies.  Thorn
TestFortranDependencies1 depends on this thorn.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### TestStrings

Cactus Code Thorn TestStrings
Author(s)    : Cactus team
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Test use of strings in Cactus. At the moment:

1) Calling C routines from Fortran with 1, 2 and 3 strings in the
   argument list.



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### TestFortranCrayPointers

Cactus Code Thorn TestFortranCrayPointers
Author(s)    : Erik Schnetter <eschnetter@perimeterinstitute.ca>
Maintainer(s): Erik Schnetter <eschnetter@perimeterinstitute.ca>
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Test Fortran Cray pointers.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### TestFreeF90

Cactus Code Thorn TestFreeF90
Author(s)    : Erik Schnetter
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Test automatic line breaks in free-form Fortran 90 code.

This thorn contains no test parameter files, the test is whether or
not it compiles.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### TestMath

Cactus Code Thorn TestMath
Author(s)    : unknown
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Test whether <cmath> functions compile in C++.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### TestInclude1

Cactus Code Thorn TestInclude1
Author(s)    : unknown
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

not documented


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### TestTypes

Cactus Code Thorn TestTypes
Author(s)    : Erik Schnetter
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Test most of the variable types.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| pint | 1 | int parameter |   *:* :: ""*OR* |
| preal | 1.0 | real parameter |   *:* :: ""*OR* |
| pboolean | yes | boolean parameter |  |
| pstring | "a" | string parameter |   .* :: ""*OR* |


### TestFpointerNULL

Cactus Code Thorn TestFpointerNULL
Author(s)    : Thomas Radke
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn tests whether a Fortran routine accepts a NULL pointer reference
as a function argument when called from a C function. The code should not crash
as long as the NULL pointer argument isn't dereferenced.

Currently this mechanism is used by the Cactus scheduler bindings C functions
which pass unallocated grid variables as NULL pointers to a scheduled Fortran
routine. Cactus makes the assumption here that those NULL pointers are legal
function arguments.

Once we find out that this basic assumption is no longer true we have a problem.
So always run this thorn's testsuite on a new platform / with a new fortran
compiler !


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### TestComplex

Cactus Code Thorn TestComplex
Author(s)    : unknown
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

not documented


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| power_value | 0.5 | power of the complex number to take |  0.0: :: "Some positive value"*OR* |
| real_value | 3 | power of the complex number to take |  *:* :: "real part"*OR* |


### TestSchedule

Cactus Code Thorn TestSchedule
Author(s)    : Rion Dooley
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn is used to test that all schedule bins are executed.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### TestArrays

Cactus Code Thorn TestArrays
Author(s)    : unknown
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

not documented


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| xghost4d | 1 | Ghostzone size in x direction for 4d arrays |   0:* :: ""*OR* |
| yghost4d | 1 | Ghostzone size in y direction for 4d arrays |   0:* :: ""*OR* |
| zghost4d | 1 | Ghostzone size in z direction for 4d arrays |   0:* :: ""*OR* |
| tghost4d | 1 | Ghostzone size in t direction for 4d arrays |   0:* :: ""*OR* |
| xsize4d | 2 | Size of 4d array in x direction |   0:* :: ""*OR* |
| ysize4d | 3 | Size of 4d array in y direction |   0:* :: ""*OR* |
| zsize4d | 2 | Size of 4d array in z direction |   0:* :: ""*OR* |
| tsize4d | 2 | Size of 4d array in t direction |   0:* :: ""*OR* |
| xghost3d | 1 | Ghostzone size in x direction for 3d arrays |   0:* :: ""*OR* |
| yghost3d | 1 | Ghostzone size in y direction for 3d arrays |   0:* :: ""*OR* |
| zghost3d | 1 | Ghostzone size in z direction for 3d arrays |   0:* :: ""*OR* |
| xsize3d | 2 | Size of 3d array in x direction |   0:* :: ""*OR* |
| ysize3d | 3 | Size of 3d array in y direction |   0:* :: ""*OR* |
| zsize3d | 2 | Size of 3d array in z direction |   0:* :: ""*OR* |
| xghost2d | 1 | Ghostzone size in x direction for 2d arrays |   0:* :: ""*OR* |
| yghost2d | 1 | Ghostzone size in y direction for 2d arrays |   0:* :: ""*OR* |
| xsize2d | 2 | Size of 2d array in x direction |   0:* :: ""*OR* |
| ysize2d | 3 | Size of 2d array in y direction |   0:* :: ""*OR* |
| xghost1d | 1 | Ghostzone size in x direction for 1d arrays |   0:* :: ""*OR* |
| xsize1d | 2 | Size of 1d array in x direction |   0:* :: ""*OR* |


### TestPar

Cactus Code Thorn TestPar
Author(s)    : Erik Schnetter <schnetter@cct.lsu.edu>
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Test parameter file syntax.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| string1 | "" |  |   ".*" :: "Any string"*OR* |
| string2 | "" |  |   ".*" :: "Any string"*OR* |
| string3 | "" |  |   ".*" :: "Any string"*OR* |
| string4 | "" |  |   ".*" :: "Any string"*OR* |
| string5 | "" |  |   ".*" :: "Any string"*OR* |
| string6 | "" |  |   ".*" :: "Any string"*OR* |
| string7 | "" |  |   ".*" :: "Any string"*OR* |
| string8 | "" |  |   ".*" :: "Any string"*OR* |
| string9 | "" |  |   ".*" :: "Any string"*OR* |
| out_dir | "" | Where to write output |   ".*" :: "Any valid path"*OR* |


### TestAllTypes

Cactus Code Thorn TestAllTypes
Author(s)    : Erik Schnetter
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Test all the variable types.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| pint | 1 | int parameter |   *:* :: ""*OR* |
| preal | 1.0 | real parameter |   *:* :: ""*OR* |
| pboolean | yes | boolean parameter |  |
| pstring | "a" | string parameter |   .* :: ""*OR* |


### TestMoL

Cactus Code Thorn TestMoL
Author(s)    : Roland Haas <rhaas@caltech.edu>
Maintainer(s): Roland Haas <rhaas@caltech.edu>
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Tests the ODE integration methods in MoL by providing a simple right-hand-side
and tests that exercise all integration methods.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| RHSexpression | "1" | expression to use for the right-hand-side of the ODE |   "1" :: "unit rhs"*OR*  "t" :: "linear in time rhs"*OR*  "t**2" :: "quadratic in time rhs"*OR*  "t**3" :: "cubic in time rhs"*OR*  "t**4" :: "quartic in time rhs"*OR*  "t**5" :: "quintic in time rhs"*OR*  "t**6" :: "sixth order in time rhs"*OR*  "t**7" :: "seventh order in time rhs"*OR*  "t**8" :: "eight order in time rhs"*OR*  "t**9" :: "ninth order in time rhs"*OR*  "exp(t)" :: "exponential in time rhs"*OR* |
| RHSSlowexpression | "1" | expression to use for the right-hand-side of the slow ODE |   "1" :: "unit rhs"*OR*  "t" :: "linear in time rhs"*OR*  "t**2" :: "quadratic in time rhs"*OR*  "t**3" :: "cubic in time rhs"*OR*  "t**4" :: "quartic in time rhs"*OR*  "t**5" :: "quintic in time rhs"*OR*  "t**6" :: "sixth order in time rhs"*OR*  "t**7" :: "seventh order in time rhs"*OR*  "t**8" :: "eight order in time rhs"*OR*  "t**9" :: "ninth order in time rhs"*OR*  "exp(t)" :: "exponential in time rhs"*OR* |
| evolve_grid_function | "yes" | register an evolved grid function with MoL |  |
| evolve_grid_array | "yes" | register an evolved grid array with MoL |  |


## CactusDoc

### CoreDoc

CVS info   : $Header$

Cactus Code Thorn CoreDoc
Thorn Author(s)     : Cactus Maintainers <cactusmaint@cactuscode.org>
Thorn Maintainer(s) : Cactus Maintainers <cactusmaint@cactuscode.org>
--------------------------------------------------------------------------

Purpose of the thorn:

Documentation that spans multiple thorns.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


## EinsteinEOS

### EOS_Omni

Cactus Code Thorn EOS_Omni
Author(s)    : Christian D. Ott <cott@tapir.caltech.edu>
Maintainer(s): Christian D. Ott <cott@tapir.caltech.edu>
Licence      : LGPL
--------------------------------------------------------------------------

This Thorn provides a one-fits-all EOS handler.
All EOS are actually implemented right inside this single
thorn without crazy interfaces and lots of function calls.

eoskey controls the type of EOS being used:

eoskey:

1 --- polytropic EOS
2 --- gamma-law EOS
3 --- hybrid EOS
4 --- finite-T microphysical NSE EOS


keyerr settings:

-1 -- called EOS routine with keytemp=1, but
      routine does not allow such a call


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| poly_gamma | 2.0 | Adiabatic Index for poly EOS |  : :: ""*OR* |
| poly_gamma_initial | -1 | Initial Adiabatic Index for poly and hybrid EOS |  -1   :: "use poly_gamma/hybrid_gamma, ie no change in gamma during ID"*OR* (0:* :: "assume that ID used this adiabiatic index, change K accordingly"*OR* |
| poly_k | 100.0 | Polytropic constant in c=G=Msun=1 |  : :: ""*OR* |
| gl_gamma | 2.0 | Adiabatic Index for gamma-law EOS |  : :: ""*OR* |
| gl_k | 100.0 | Polytropic constant in c=G=Msun=1 for gamma-law EOS |  : :: ""*OR* |
| hybrid_gamma_th | 1.5 | Thermal gamma for hybrid EOS |  : :: ""*OR* |
| n_pieces | 2 | Number of polytropic pieces |  0:10 :: "Max 10 pieces"*OR* |
| hybrid_k0 | 0.0 | K coefficent of the first piece |  0:* :: "Any positive"*OR* |
| hybrid_gamma1 | 1.325 | subnuclear adiabatic Index for hybrid EOS |   : :: ""*OR* |
| hybrid_gamma2 | 2.5 | subnuclear adiabatic Index for hybrid EOS |  : :: ""*OR* |
| hybrid_k1 | 0.4640517 | Polytropic constant in c=G=Msun=1 for hybrid EOS |  0:* :: "Any positive"*OR* |
| hybrid_rho_nuc | 3.238607e-4 | Density at which to switch between gammas; c=G=Msun=1 | 0:* :: "Any positive"*OR* |
| coldeos_table_name | "blah.asc" | table name for cold EOS (ASCII) |  .* :: "Can be anything"*OR* |
| coldeos_read_table | "No" | Read in cold EOS table? |  |
| coldeos_use_thermal_gamma_law | "Yes" | use an additional thermal gamma? |  |
| barotropiceos_table_name | "blah.asc" | table name for barotropic EOS (ASCII) |  .* :: "Can be anything"*OR* |
| barotropiceos_read_table | "No" | Read in barotropic EOS table? |  |
| barotropiceos_use_thermal_gamma_law | "Yes" | use an additional thermal gamma? |  |
| barotropiceos_gammath | 2.0 | thermal gamma for barotropic EOS |  1.0:* :: "something"*OR* |
| nuceos_read_table | "No" | Read in EOS table? |  |
| do_energy_shift | "yes" | shift energies around? |  |
| dump_nuceos_table | "No" | Dump table in ASCII at beginning |  |
| dump_nuceos_table_name | "blah.asc" | nuceos dump table name (ASCII) |  .* :: "Can be anything"*OR* |
| nuceos_table_name | "blah.h5" | nuceos table name (hdf5) |  .* :: "Can be anything"*OR* |
| read_table_on_single_process | "no" | read table on one process only and bcast data |  |
| reader_process | 0 | read table on this process and bcast data |   0:* :: "read on process N"*OR* |


### EOS_Hybrid

Cactus Code Thorn EOS_Hybrid
Author(s)    : Harry Dimmelmeier
               Ian Hawke
               Christian Ott
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn implements the EOS used by Dimmelmeier Novak Font Ibanez Mueller PRD71 064023 (2005) in
core collapse simulations. Requires the CactusEOS infrastructure.



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| eos_gamma_th | 1.5 | Gamma_th for EOS |  : :: ""*OR* |
| eos_gamma_supernuclear | 1.66666666666666 | Gamma_2 for EOS |  : :: ""*OR* |
| rho_nuc | 1.e-10 | Nuclear matter density |  : :: ""*OR* |


### EOS_Polytrope

Cactus Code Thorn EOS_Polytrope
Author(s)    : Ian Hawke
               Frank Löffler
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This is a "faked" polytropic equation of state. It is designed to work
through the CactusEOS interface.
The EoS reads Pressure = eos_k * rho ** (eos_gamma). The specific internal
energy is given by the assumption of a perfect fluid.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| eos_gamma | 2.0 | Adiabatic Index for Ideal Fluid |  : :: ""*OR* |
| eos_k | 80.0 | Polytropic constant |  : :: ""*OR* |
| use_cgs | "no" | Use the CGS units |  |
| gamma_ini | 2.0 | Polytropic Gamma used for the initial model (e.g., by RNSID) |  : :: ""*OR* |


### EOS_IdealFluid

Cactus Code Thorn EOS_Ideal_Fluid
Author(s)    : ...
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

...


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| eos_ideal_fluid_gamma | 2.0 | Adiabatic Index for Ideal Fluid |  : :: ""*OR* |


## EinsteinUtils

### TGRtensor

Cactus Code Thorn TGRtensor
Authors    : Erik Schnetter <schnetter@uni-tuebingen.de>
CVS info   : $Header$
--------------------------------------------------------------------------

Purpose of the thorn:

It contains generic tensor operations and other generic stuff.

TODO: Move lapack routines to thorn LAPACK


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### SetMask_SphericalSurface

Cactus Code Thorn SetMask_SphericalSurface
Author(s)    : Frank Löffler
Maintainer(s): Frank Löffler
Licence      : GPLv2+
--------------------------------------------------------------------------

1. Purpose

Sets a given mask according to given spherical surfaces and parameters.
One example usage is to set the excision mask based on information from
an apparent horizon finder which is stored as a spherical surface.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| SetMask_MaskName | "HydroBase::hydro_excision_mask" | Full variable name of the mask to be set |   ".+" :: "Any full Cactus variable name, GF of type CCTK_BYTE"*OR* |
| SetMask_ResetAll | "yes" | 'yes' (default): Overwrite complete mask, 'no': Only set excision points | # |


## CactusElliptic

### EllSOR

Cactus Code Thorn EllSOR
Author(s)    : Cactus team
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn provides basic elliptic solvers for different equation types 
using SOR iterative methods. The thorn is written to be robust and 
pedagogical rather that a viable fast solver.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### EllPETSc

Cactus Code Thorn EllPETSc
Author(s)    : Cactus team
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn shows how PETSc can be used with the EllBase Elliptic 
solver interface. 



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| petsc_verbose | "yes" | PETSc verbose output | "no"   :: "No output"*OR*"yes"  :: "Some output"*OR*"debug":: "Tons of output"*OR* |
| petsc_reuse | "no" | Reuse parts of the PETSc structure |  |
| petsc_coeff_to_one | "no" | Divide each line of the matrix by the central value? |  |
| petsc_KSP_type | "PCJACOBI" | Which Krylov subspace method to use |  "KSPCR"         :: "pcr"*OR* "KSPCG"         :: "cg"*OR* "KSPCGS"        :: "cgs"*OR* "KSPBCGS"       :: "bcgs"*OR* "KSPLSQR"       :: "lsqr"*OR* "KSPGMRES"      :: "gmres"*OR* "KSPTCQMR"      :: "tcqmr"*OR* "KSPTFQMR"      :: "tfqmr"*OR* "KSPCHEBYCHEV"  :: "chebyshev"*OR* "KSPCHEBYSHEV"  :: "chebyshev"*OR* "KSPRICHARDSON" :: "richardson"*OR*}"KSPBCGS" *OR**OR*KEYWORD petsc_PC_type "Which preconditioner method to use"*OR*{*OR* "PCNONE"    :: "none"*OR* "PCJACOBI"  :: "jacobi"*OR* "PCBJACOBI" :: "bjacobi"*OR* "PCICC"     :: "icc"*OR* "PCILU"     :: "ilu"*OR* "PCASM"     :: "asm"*OR* "PCLU"      :: "lu"*OR* |
| petsc_nablaform | "down" | PETSC nabla form |   "up"      :: ""*OR*  "down"    :: ""*OR* |
| PetscTolStyle | 0 | PETSc Tolerance flavors FIXME | # 0: :: ""*OR*# |


### TATelliptic

Cactus Code Thorn TATelliptic
Authors    : Erik Schnetter <schnetter@uni-tuebingen.de>
--------------------------------------------------------------------------

Purpose of the thorn:

This is a generic interface to nonlinear elliptic solvers.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### TATPETSc

Cactus Code Thorn TGRPETSc
Authors    : Erik Schnetter <schnetter@uni-tuebingen.de>
--------------------------------------------------------------------------

Purpose of the thorn:

It is an interface to PETSc, which is used here to solve elliptic
equations.  An interface to SNESsolve, the solver for nonlinear
elliptic equations, is exported.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "no" | Produce log output while running |  |
| veryverbose | "no" | Produce much log output while running |  |
| options | "" | Command line options for PETSc |   .* :: "no restriction"*OR* |
| mglevels | 2 | Number of multigrid levels | #  2:* :: "Anything"*OR*# |


### EllBase

Cactus Code Thorn EllBase
Author(s)    : unknown
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn does ...

2. Dependencies of the thorn

This thorn additionally requires implementations and thorns ...

3. Thorn distribution

This thorn is available to ...

4. Additional information


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| elliptic_verbose | "no" | elliptic verbosity |   "yes"  :: "be verbose in elliptic"*OR*  "no"   :: "silence in elliptic"*OR*  "debug":: "even more verbose in elliptic"*OR* |


## Carpet

### CarpetIOBasic

Cactus Code Thorn CarpetIOBasic
Author(s)    : Erik Schnetter <schnetter@cct.lsu.edu>
Maintainer(s): Erik Schnetter <schnetter@cct.lsu.edu>
Licence      : GPLv2+
--------------------------------------------------------------------------

1. Purpose

This thorn provides info output for Carpet.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| outInfo_vars | "" | Variables to output in scalar form |   "" :: "A regex which matches everything"*OR* |
| outInfo_reductions | "minimum | List of reductions to output in scalar form |   "" :: "A regex which matches everything"*OR* |
| outInfo_criterion | "iteration" | Criterion to select scalar output intervals, overrides out_every |   "never"     :: "Never output"*OR*  "iteration" :: "Output every so many iterations"*OR*  "divisor"   :: "Output if iteration mod divisor == 0."*OR*  "time"      :: "Output every that much coordinate time"*OR* |
| outInfo_every | -2 | How often to do scalar output, overrides IO::out_every |    1:* :: "Output every so many time steps"*OR*  -1:0 :: "No output"*OR*  -2   :: "Default to IO::out_every"*OR* |
| outInfo_dt | -2 | How often to do scalar output, overrides out_dt |   (0:* :: "In intervals of that much coordinate time"*OR*   0   :: "As often as possible"*OR*  -1   :: "Disable output"*OR*  -2   :: "Default to IO::out_dt"*OR* |
| outHeader_every | 20 | How often to print the header |   1:* :: "Output every so many time steps" *OR* -1   :: "No header output"*OR* |
| iter_width | 9 | Field width for the current iteration |   1:* :: ""*OR* |
| time_width | 9 | Field width for the simulation time |   1:* :: ""*OR* |
| time_prec | 3 | Precision for the simulation time |   0:* :: ""*OR* |
| int_width | 9 | Field width for integer values |   1:* :: ""*OR* |
| real_width | 12 | Field width for real values |   1:* :: ""*OR* |
| real_prec | 7 | Precision for real values |   0:* :: ""*OR* |
| real_prec_sci | 6 | Precision for real values in scientific notation |   0:* :: ""*OR* |
| real_min | 1.0e-8 | Lower bound for numbers that are displayed in fixed notation |   (0.0:* :: ""*OR* |
| real_max | 1.0e+3 | Upper bound for numbers that are displayed in fixed notation |   (0.0:* :: ""*OR* |


### LoopControl

Cactus Code Thorn LoopControl
Author(s)    : Erik Schnetter <schnetter@gmail.com>
Maintainer(s): Erik Schnetter <schnetter@gmail.com>
Licence      : GPLv2+
--------------------------------------------------------------------------

1. Purpose

Iterate over multi-dimensional arrays in an efficient manner, using
OpenMP (if available) and cache-aware loop tiling.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "no" | Output some loop information at run time |  |
| veryverbose | "no" | Output detailed debug information at run time |  |
| selftest | "no" | Run a self test with every loop (expensive) |  |
| statistics_filename | "LoopControl-statistics" | File name for LoopControl statistics |   ""   :: "disable statistics output"*OR*  ".+" :: "file name"*OR* |
| statistics_every_seconds | -1.0 | Output statistics every so many seconds |   -1.0  :: "don't output"*OR*  0.0:* :: "output every so many seconds"*OR* |
| initial_setup | "tiled" | Initial configuration |   "legacy" :: "Like a non-LoopControl loop"*OR*  "tiled"  :: "Basic LoopControl setup"*OR* |
| explore_eagerly_before_iteration | 0 | Try to explore the parameter space as much as possible before this iteration |   0:* :: ""*OR* |
| settle_after_iteration | 0 | Do not explore the parameter space any more at or after this iteration |   -1  :: "always continue exploring"*OR*  0:* :: ""*OR*# Setting settle_after_iteration=0 apparently avoids an inefficiency,*OR*# so make it the default for the time being.*OR* |
| use_smt_threads | "yes" | Place SMT threads close together |  |
| align_with_cachelines | "yes" | Align innermost loops with cache line size |  |
| tilesize_i | 4 | Tile size in i direction (in grid points) for loop tiling |   1:* :: ""*OR* |
| tilesize_j | 4 | Tile size in j direction (in grid points) for loop tiling |   1:* :: ""*OR* |
| tilesize_k | 4 | Tile size in k direction (in grid points) for loop tiling |   1:* :: ""*OR* |
| loopsize_i | 8 | Size of each thread's loop in i direction (in grid points) for multithreading |   1:* :: ""*OR* |
| loopsize_j | 8 | Size of each thread's loop in j direction (in grid points) for multithreading |   1:* :: ""*OR* |
| loopsize_k | 8 | Size of each thread's loop in k direction (in grid points) for multithreading |   1:* :: ""*OR* |
| max_size_factor | 4 | Maximum size for modifying loop sizes |   1:* :: ""*OR* |
| very_expensive_factor | 1.5 | Params worse than the current-best by more than this factor are ignored more quickly |   1.0:* :: ""*OR* |
| tryout_iterations | 1 | Try out new params for this many iterations before judging them |   1:* :: ""*OR* |
| random_jump_probability | 0.1 | Probability of a random jump to begin exploring a very different param |   0.0:1.0 :: ""*OR* |


### CarpetRegrid

Cactus Code Thorn CarpetRegrid
Author(s)    : Erik Schnetter <schnetter@cct.lsu.edu>
Maintainer(s): Erik Schnetter <schnetter@cct.lsu.edu>
Licence      : GPLv2+
--------------------------------------------------------------------------

1. Purpose

This thorn handles regridding for Carpet.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "no" | Print screen output while running |  |
| veryverbose | "no" | Print much screen output while running |  |
| refinement_levels | 1 | Number of refinement levels (including the base level) |   1:* :: "must be positive, and must not be larger than Carpet::max_refinement_levels"*OR* |
| regrid_every | 0 | Regrid every n time steps |   -1   :: "regrid never"*OR*   0   :: "regrid during initial data calculation only"*OR*   1:* :: "regrid every n time steps"*OR* |
| activate_levels_on_regrid | "none" | Whether to activate or deactivate new levels on regridding |   "none"     :: "Do not activate or deactivate any levels"*OR*  "fixed"    :: "Activate or deactivate a fixed number of levels"*OR*  "function" :: "Activate or deactivate a variable number of levels, determined by a user-specified function.  When this option is used, the parameters num_new_levels and activate_next have no effect and should not be set."*OR* |
| num_new_levels | 0 | When regridding, activate this many new levels (if possible).  Note that this will steer the parameter refinement_levels. |   : :: "Number of new levels to activate (negative numbers deactivate)"*OR* |
| activate_next | 1 | The next iteration at which new levels should be activated |   0: :: "Note that this parameter is steered when new levels are activated"*OR* |
| keep_same_grid_structure | "no" | Do not allow the grid structure to change; only allow levels to be switched on or off |  |
| refined_regions | "centre" | Regions where the grid is refined |   "none"                   :: "Don't refine"*OR*  "centre"                 :: "Refine around the centre of the grid only"*OR*  "manual-gridpoints"      :: "Refine the regions specified by integer grid points l[123]i[xyz]{min,max}"*OR*  "manual-coordinates"     :: "Refine the regions specified by coordinates l[123][xyz]{min,max}"*OR*  "manual-gridpoint-list"  :: "Refine the regions specified by integer grid points in the parameter 'gridpoints'"*OR*  "manual-coordinate-list" :: "Refine the regions specified by coordinates in the parameter 'coordinates'"*OR*  "moving"                 :: "Refine a moving region"*OR*  "automatic"              :: "Refine automatically"*OR* |
| smart_outer_boundaries | no | Use the CoordBase interface for outer boundaries |  |
| merge_overlapping_components | no | Merge overlapping components |  |
| tracking | no | Enable tracking |  |
| symmetry_x | "no" | Refine the lower half in x-direction |  |
| symmetry_y | "no" | Refine the lower half in y-direction |  |
| symmetry_z | "no" | Refine the lower half in z-direction |  |
| moving_trajectory | "point" | Type of trajectory |   "point"  :: "Do not move"*OR*  "circle" :: "Move in a circle"*OR* |
| moving_region_radius | 1.0 | Radius of the moving region (on the first refined level) |   (0: :: ""*OR* |
| moving_centre_x | 0.0 | x-coordinate of the centre |   : :: ""*OR* |
| moving_centre_y | 0.0 | y-coordinate of the centre |   : :: ""*OR* |
| moving_centre_z | 0.0 | z-coordinate of the centre |   : :: ""*OR* |
| moving_circle_radius | 1.0 | Radius of the circle |   0: :: ""*OR* |
| moving_circle_frequency | 1.0 | Angular frequency on the circle |   0: :: ""*OR* |
| l1ixmin | 0 | Lower boundary of level 1 box in x-direction |   : :: ""*OR* |
| l1iymin | 0 | Lower boundary of level 1 box in y-direction |   : :: ""*OR* |
| l1izmin | 0 | Lower boundary of level 1 box in z-direction |   : :: ""*OR* |
| l1ixmax | -1 | Upper boundary of level 1 box in x-direction |   : :: ""*OR* |
| l1iymax | -1 | Upper boundary of level 1 box in y-direction |   : :: ""*OR* |
| l1izmax | -1 | Upper boundary of level 1 box in z-direction |   : :: ""*OR* |
| l2ixmin | 0 | Lower boundary of level 2 box in x-direction |   : :: ""*OR* |
| l2iymin | 0 | Lower boundary of level 2 box in y-direction |   : :: ""*OR* |
| l2izmin | 0 | Lower boundary of level 2 box in z-direction |   : :: ""*OR* |
| l2ixmax | -1 | Upper boundary of level 2 box in x-direction |   : :: ""*OR* |
| l2iymax | -1 | Upper boundary of level 2 box in y-direction |   : :: ""*OR* |
| l2izmax | -1 | Upper boundary of level 2 box in z-direction |   : :: ""*OR* |
| l3ixmin | 0 | Lower boundary of level 3 box in x-direction |   : :: ""*OR* |
| l3iymin | 0 | Lower boundary of level 3 box in y-direction |   : :: ""*OR* |
| l3izmin | 0 | Lower boundary of level 3 box in z-direction |   : :: ""*OR* |
| l3ixmax | -1 | Upper boundary of level 3 box in x-direction |   : :: ""*OR* |
| l3iymax | -1 | Upper boundary of level 3 box in y-direction |   : :: ""*OR* |
| l3izmax | -1 | Upper boundary of level 3 box in z-direction |   : :: ""*OR* |
| l1xmin | 0 | Lower boundary of level 1 box in x-direction |   : :: ""*OR* |
| l1ymin | 0 | Lower boundary of level 1 box in y-direction |   : :: ""*OR* |
| l1zmin | 0 | Lower boundary of level 1 box in z-direction |   : :: ""*OR* |
| l1xmax | -1 | Upper boundary of level 1 box in x-direction |   : :: ""*OR* |
| l1ymax | -1 | Upper boundary of level 1 box in y-direction |   : :: ""*OR* |
| l1zmax | -1 | Upper boundary of level 1 box in z-direction |   : :: ""*OR* |
| l2xmin | 0 | Lower boundary of level 2 box in x-direction |   : :: ""*OR* |
| l2ymin | 0 | Lower boundary of level 2 box in y-direction |   : :: ""*OR* |
| l2zmin | 0 | Lower boundary of level 2 box in z-direction |   : :: ""*OR* |
| l2xmax | -1 | Upper boundary of level 2 box in x-direction |   : :: ""*OR* |
| l2ymax | -1 | Upper boundary of level 2 box in y-direction |   : :: ""*OR* |
| l2zmax | -1 | Upper boundary of level 2 box in z-direction |   : :: ""*OR* |
| l3xmin | 0 | Lower boundary of level 3 box in x-direction |   : :: ""*OR* |
| l3ymin | 0 | Lower boundary of level 3 box in y-direction |   : :: ""*OR* |
| l3zmin | 0 | Lower boundary of level 3 box in z-direction |   : :: ""*OR* |
| l3xmax | -1 | Upper boundary of level 3 box in x-direction |   : :: ""*OR* |
| l3ymax | -1 | Upper boundary of level 3 box in y-direction |   : :: ""*OR* |
| l3zmax | -1 | Upper boundary of level 3 box in z-direction |   : :: ""*OR* |
| gridpoints | "" | List of bounding box gridpoints | # We want the string to contain a list of bboxes.  Each bbox contains*OR*# three vectors specifying the lower bound, upper bound, and stride.*OR*# (The upper bound is inclusive.  All values are nonnegative integers.)*OR*# The syntax for vectors, bboxes, and lists is described below.*OR*# All spaces are optional.*OR**OR*# Almost human readable explanation follows.*OR*# The specification is hierarchical.*OR**OR*# The following definitions are generic:*OR*# VECT2(x) := "[ x, x ]"*OR*#    two x, separated by commas, enclosed in square brackets*OR*# VECT3(x) := "[ x, x, x ]"*OR*#    three x, separated by commas, enclosed in square brackets*OR*# LIST(x) := "[ {{ x, }* x}? ]"*OR*#    zero or more x, separated by commas, enclosed in square brackets*OR**OR*# A domain is built up as follows:*OR*# REGION   := VECT3(VECT2(double))*OR*# REFLEVEL := LIST(REGION)*OR*# DOMAIN   := LIST(REFLEVEL)*OR**OR*  "^$" :: "leave empty for no refinement"*OR*  ".*" :: "[ [ ([<imin>,<jmin>,<kmin>]:[<imax>,<jmax>,<kmax>]:[<istride>,<jstride>,<kstride>]), ... ], ... ]"*OR* |
| coordinates | "" | List of bounding box coordinates | # We want the string to contain a list of bboxes.  Each bbox contains*OR*# three vectors specifying the lower bound, upper bound, and stride.*OR*# (The upper bound is inclusive.  All values are nonnegative integers.)*OR*# The syntax for vectors, bboxes, and lists is described below.*OR*# All spaces are optional.*OR**OR*# Almost human readable explanation follows.*OR*# The specification is hierarchical.*OR**OR*# The following definitions are generic:*OR*# VECT2(x) := "[ x, x ]"*OR*#    two x, separated by commas, enclosed in square brackets*OR*# VECT3(x) := "[ x, x, x ]"*OR*#    three x, separated by commas, enclosed in square brackets*OR*# LIST(x) := "[ {{ x, }* x}? ]"*OR*#    zero or more x, separated by commas, enclosed in square brackets*OR**OR*# A domain is built up as follows:*OR*# REGION   := VECT3(VECT2(double))*OR*# REFLEVEL := LIST(REGION)*OR*# DOMAIN   := LIST(REFLEVEL)*OR**OR*  "^$" :: "leave empty for no refinement"*OR*  ".*" :: "[ [ ([<xmin>,<ymin>,<zmin>]:[<xmax>,<ymax>,<zmax>]:[<xstride>,<ystride>,<zstride>]), ... ], ... ]"*OR* |
| num_offsets | 0 | Number of given offsets |   0:10 :: ""*OR* |
| offset_firstlevel | 1 | First (lowest) refinement level that should have an offset applied |   1:* :: ""*OR* |
| outerbounds | "" | Outer boundaries |   "^$" :: "leave empty for no outer boundaries"*OR**OR*# Each vector element is 0 or 1,*OR*# where 0 is handled by synchronisation or prolongation,*OR*# and 1 stands for a user-supplied ("outer") boundary condition.*OR**OR*# BND := " [ 0 , 0 ]"*OR*# VECT := " [ BND , BND , BND ]"*OR*# LIST := " [{{VECT ,}*VECT}? ]"*OR**OR*# Almost human readable explanation follows.*OR*# The specification is hierarchical.*OR**OR*# The following definitions are generic:*OR*# VECT2(x) := "[ x, x ]"*OR*#    two x, separated by commas, enclosed in square brackets*OR*# VECT3(x) := "[ x, x, x ]"*OR*#    three x, separated by commas, enclosed in square brackets*OR*# LIST(x) := "[ {{ x, }* x}? ]"*OR*#    zero or more x, separated by commas, enclosed in square brackets*OR**OR*# A domain is built up as follows:*OR*# REGION   := VECT3(VECT2(double))*OR*# REFLEVEL := LIST(REGION)*OR*# DOMAIN   := LIST(REFLEVEL)*OR**OR*  ".*" :: "[ [ [[?,?],[?,?],[?,?]], ... ], ...]"*OR* |
| minwidth | 8 | Minimum width of refined region |   1:* :: "must be positive"*OR* |
| minfraction | 0.75 | Minimum fraction of points in need of refinement in a refined region |   0:1 :: "must be positive and less than one"*OR* |
| maxerror | 1.0 | Maximum allowed error for non-refined grid points |   *:* :: "everything goes"*OR* |
| errorvar | "" | Name of grid function that contains the error |   ".*" :: "must be the name of a grid function"*OR* |


### TestLoopControl

Cactus Code Thorn TestLoopControl
Author(s)    : Erik Schnetter <schnetter@gmail.com>
Maintainer(s): Erik Schnetter <schnetter@gmail.com>
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

not documented


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### CarpetInterp2

Cactus Code Thorn CarpetInterp2
Author(s)    : Erik Schnetter <schnetter@cct.lsu.edu>
Maintainer(s): Erik Schnetter <schnetter@cct.lsu.edu>
Licence      : GPLv2+
--------------------------------------------------------------------------

1. Purpose

This thorn provides a parallel interpolator for Carpet.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "no" | Produce info output |  |
| veryverbose | "no" | Produce debugging output |  |
| interp_barrier | "no" | Wait for all processes before and after interpolating |  |


### Timers

Cactus Code Thorn Timers
Author(s)    : Erik Schnetter <schnetter@gmail.com>
Maintainer(s): Erik Schnetter <schnetter@gmail.com>
Licence      : n/a
--------------------------------------------------------------------------

1. Purpose

Provide various kinds of timers:

CactusTimer:    C++ wrapper around standard Cactus timers
CactusTimerSet: knows about all CactusTimers that were ever created
Timer:          hierarchical set of CactusTimers
TimerTree:      helper class for Timer


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "no" | Output (debug) messages when a timer is started or stopped |  |
| disable_cactus_timer_set | "no" | Disable Cactus timer set |  |
| disable_timer_trees | "no" | Disable timer trees |  |
| xml_clock | "gettimeofday" | Which clock to use in the XML timer output file |   ".*" :: "must be a legal clock name"*OR* |
| threshold_percentage | 1.0 | The percentage of the root timer below which timers are omitted |   0:* :: ""*OR* |
| output_precision | 1 | Number of decimal places to use in standard output for timer tree |   1:* :: "number of decimal places"*OR* |


### CarpetTracker

Cactus Code Thorn CarpetTracker
Author(s)    : Erik Schnetter <schnetter@cct.lsu.edu>
Maintainer(s): Erik Schnetter <schnetter@cct.lsu.edu>
Licence      : GPLv2+
--------------------------------------------------------------------------

1. Purpose

Track black holes and update the location of the refinement regions.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | no | Tell what is going on |  |


### ReductionTest3

CVS info   : $Header:$

Cactus Code Thorn ReductionTest
Thorn Author(s)     : Christian D. Ott <cott@aei.mpg.de>
Thorn Maintainer(s) : Christian D. Ott <cott@aei.mpg.de>
--------------------------------------------------------------------------

Purpose of the thorn:



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### ReductionTest2

CVS info   : $Header:$

Cactus Code Thorn ReductionTest
Thorn Author(s)     : Christian D. Ott <cott@aei.mpg.de>
Thorn Maintainer(s) : Christian D. Ott <cott@aei.mpg.de>
--------------------------------------------------------------------------

Purpose of the thorn:



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### Carpet

Cactus Code Thorn Carpet
Author(s)    : Erik Schnetter <schnetter@cct.lsu.edu>
Maintainer(s): Erik Schnetter <schnetter@cct.lsu.edu>
Licence      : GPLv2+
--------------------------------------------------------------------------

1. Purpose

This thorn provides a parallel AMR (adaptive mesh refinement) driver
with MPI.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| domain_from_coordbase | "no" | Use the domain description from CoordBase |  |
| domain_from_multipatch | "no" | Use the domain description from MultiPatch |  |
| global_nx | 10 | Grid size in x direction |   0:* :: "must be nonnegative"*OR* |
| global_ny | 10 | Grid size in y direction |   0:* :: "must be nonnegative"*OR* |
| global_nz | 10 | Grid size in z direction |   0:* :: "must be nonnegative"*OR* |
| global_nsize | -1 | Grid size in each spatial direction |   0:* :: "must be nonnegative"*OR*  -1  :: "use the per-dimension parameters"*OR* |
| ghost_size_x | 1 | Ghost zones in x direction |   0:* :: "must be nonnegative"*OR* |
| ghost_size_y | 1 | Ghost zones in y direction |   0:* :: "must be nonnegative"*OR* |
| ghost_size_z | 1 | Ghost zones in z direction |   0:* :: "must be nonnegative"*OR* |
| ghost_size | -1 | Ghost zones in each spatial direction |   0:* :: "must be nonnegative"*OR*  -1  :: "use the per-dimension parameters"*OR* |
| ghost_sizes | "" | Number of ghost zones for each refinement level |   "^$" :: "Use the value of ghost_size or ghost_size_[xyz]"*OR*  "^[[:space:]]*\[[[:space:]]*[[:digit:]]+[[:space:]]*(,[[:space:]]*[[:digit:]]+[[:space:]]*)*\][[:space:]]*$" :: "[ <ghost_size>, ... ]"*OR* |
| periodic | "no" | do not use this parameter |  |
| periodic_x | "yes" | do not use this parameter |  |
| periodic_y | "yes" | do not use this parameter |  |
| periodic_z | "yes" | do not use this parameter |  |
| refinement_centering | "vertex" | Centering |   "vertex" :: "use a vertex centred grid structure"*OR*  "cell"   :: "use a cell centred grid structure"*OR* |
| eno_interpolation_type | "samples" | What is represented by values in cells DEPRECATED |   "samples"  :: "grid values a sample values of the solution"*OR* |
| max_refinement_levels | 1 | Maximum number of refinement levels (including the base level) |   1:* :: "must be positive"*OR* |
| max_timelevels | -1 | Maximum number of time levels (including the current time level) |   -1 :: "Set automatically to prolonation_order_time+1"*OR*  1:* :: "Set this explicitly"*OR* |
| refinement_factor | 2 | Refinement factor |   1:* :: "must be positive"*OR* |
| space_refinement_factors | "" | Spatial refinement factors over the coarsest level |   "^$" :: "Use the value of refinement_factor"*OR*  # V = [SDS(,SDS)*]*OR*  # L = [SVS(,SVS)*]*OR*  #   = [S[SDS(,SDS)*]S(,S[SDS(,SDS)*]S)*]*OR*  "^[[:space:]]*\[[[:space:]]*\[[[:space:]]*[[:digit:]]+[[:space:]]*(,[[:space:]]*[[:digit:]]+[[:space:]]*)*\][[:space:]]*(,[[:space:]]*\[[[:space:]]*[[:digit:]]+[[:space:]]*(,[[:space:]]*[[:digit:]]+[[:space:]]*)*\][[:space:]]*)*\][[:space:]]*$" :: "[ [<ifact>,<jfact>,<kfact>], ... ]"*OR* |
| time_refinement_factors | "" | Temporal refinement factors over the coarsest level |   "^$" :: "Use the value of refinement_factor"*OR*  # L = [SDS(,SDS)*]*OR*  "^[[:space:]]*\[[[:space:]]*[[:digit:]]+[[:space:]]*(,[[:space:]]*[[:digit:]]+[[:space:]]*)*\][[:space:]]*$" :: "[ <tfact>, ... ]"*OR* |
| refine_timestep | "no" | Correct Time::dtfac for spacings on finer grids |  |
| convergence_level | 0 | Convergence level |   *:* :: "negative for finer, positive for coarser resolutions"*OR* |
| num_convergence_levels | 1 | Number of convergence levels (including the base level) |   1:* :: "must be positive"*OR* |
| convergence_factor | 2 | Multigrid factor |   1:* :: "must be positive"*OR* |
| num_maps | 1 | Number of maps |   1:* :: ""*OR* |
| model | "world" | Model name for multi-model simulations -- the model name is used to distribute the processors onto the models |   ".+" :: ""*OR* |
| prolongation_order_space | 1 | Order of prolongation operator in space |   0:* :: "vertex centred orders must be odd"*OR* |
| prolongation_orders_space | "" | Order of prolongation operator in space for each refinement level |   "^$" :: "Use the value of prolongation_order_space"*OR*  "^[[:space:]]*\[[[:space:]]*[[:digit:]]+[[:space:]]*(,[[:space:]]*[[:digit:]]+[[:space:]]*)*\][[:space:]]*$" :: "[ <order>, ... ]"*OR* |
| prolongation_order_time | 1 | Order of prolongation operator in time |   0:* :: ""*OR* |
| use_buffer_zones | "no" | Use buffer zones |  |
| additional_buffer_zones | 0 | Additional buffer zones |   *:* :: ""*OR* |
| use_overlap_zones | "no" | Use overlap zones |  |
| additional_overlap_zones | 0 | Additional overlap zones |   *:* :: ""*OR* |
| use_tapered_grids | "no" | Use tapered grids, avoiding time interpolation during evolution |  |
| num_integrator_substeps | -1 | Number of substeps of the time integrator |   -1: :: "Call MoLNumIntegratorSubsteps"*OR*  0:* :: ""*OR* |
| sync_during_time_integration | "yes" | Synchronise during time integration, even when prolongation is switched off |  |
| base_extents | braces | Extents of base grid components, in grid point units of the finest level |   "^$" :: "leave empty for one grid component covering the whole region (default)"*OR**OR*# We want the string to contain a list of bboxes.  Each bbox contains*OR*# three vectors specifying the lower bound, upper bound, and stride.*OR*# (The upper bound is inclusive.  All values are nonnegative integers.)*OR*# The syntax for vectors, bboxes, and lists is described below.*OR*# All spaces are optional.*OR**OR*# The () parentheses and [] brackets are literals.*OR*# The { |
| base_outerbounds | "" | Outer boundaries of base grid components |   "^$" :: "leave empty for using the default, which depends on cctk_gsh"*OR**OR*# See above for an explanation of this syntax, and of the tapeworm below.*OR*# Each vector element is 0 or 1,*OR*# where 0 is handled by synchronisation or prolongation,*OR*# and 1 stands for a user-supplied ("outer") boundary condition.*OR**OR*# BND := " [ 0 , 0 ]"*OR*# VECT := " [ BND , BND , BND ]"*OR*# LIST := " [{{VECT ,}*VECT}? ]"*OR**OR*# Almost human readable explanation follows.*OR*# The specification is hierarchical.*OR**OR*# The following definitions are generic:*OR*# VECT2(x) := "[ x, x ]"*OR*#    two x, separated by commas, enclosed in square brackets*OR*# VECT3(x) := "[ x, x, x ]"*OR*#    three x, separated by commas, enclosed in square brackets*OR*# LIST(x) := "[ {{ x, }* x}? ]"*OR*#    zero or more x, separated by commas, enclosed in square brackets*OR**OR*# A domain is built up as follows:*OR*# REGION   := VECT3(VECT2(double))*OR*# DOMAIN   := LIST(REGION)*OR**OR*  "^[[:space:]]*\[(([[:space:]]*\[[[:space:]]*\[[[:space:]]*[[:digit:]]+[[:space:]]*,[[:space:]]*[[:digit:]]+[[:space:]]*\][[:space:]]*,[[:space:]]*\[[[:space:]]*[[:digit:]]+[[:space:]]*,[[:space:]]*[[:digit:]]+[[:space:]]*\][[:space:]]*,[[:space:]]*\[[[:space:]]*[[:digit:]]+[[:space:]]*,[[:space:]]*[[:digit:]]+[[:space:]]*\][[:space:]]*\][[:space:]]*,)*[[:space:]]*\[[[:space:]]*\[[[:space:]]*[[:digit:]]+[[:space:]]*,[[:space:]]*[[:digit:]]+[[:space:]]*\][[:space:]]*,[[:space:]]*\[[[:space:]]*[[:digit:]]+[[:space:]]*,[[:space:]]*[[:digit:]]+[[:space:]]*\][[:space:]]*,[[:space:]]*\[[[:space:]]*[[:digit:]]+[[:space:]]*,[[:space:]]*[[:digit:]]+[[:space:]]*\][[:space:]]*\])?[[:space:]]*\][[:space:]]*$" :: "[ [ [<ilower>,<iupper>], [<jlower>,<jupper>], [<klower>,<kupper>] ], ... ]"*OR* |
| enable_all_storage | "no" | Enable storage for all grid functions |  |
| enable_no_storage | "no" | Exit before beginning to enable storage for grid functions |  |
| poison_new_timelevels | "yes" | Try to catch uninitialised grid elements by setting new timelevels to values that will catch your attention |  |
| check_for_poison | "no" | Explicitely check for the poison value after every time step |  |
| poison_value | 0 | UNUSED; use CarpetLib::poison_value instead |   * :: ""*OR* |
| max_poison_locations | 10 | Maximum number of poison locations that are printed to the screen |   -1  :: "print all locations"*OR*  0:* :: "print only that many locations"*OR* |
| checksum_timelevels | "no" | Try to catch unintentionally changed timelevels by taking checksums and comparing against these |  |
| suppress_restriction | "no" | Suppress the restriction operations.  This makes the coarser refinement levels independent of the finer ones. |  |
| verbose | "no" | Display more info on the screen |  |
| veryverbose | "no" | Display a lot of info on the screen |  |
| storage_verbose | "no" | Display verbose storage information if veryverbose |  |
| barriers | "no" | Insert barriers at strategic places for debugging purposes (slows down execution) |  |
| schedule_barriers | "no" | Insert barriers between scheduled items, so that timer statistics become more reliable (slows down execution) |  |
| sync_barriers | "no" | Insert barriers before and after syncs, so that the sync timer is more reliable (slows down execution) |  |
| output_internal_data | "no" | Periodically print internal data to the screen for debugging purposes |  |
| timing_average_window_minutes | 10.0 | Time interval (in wall time minutes) for calculating the current physics time per hour |   (0.0:* :: ""*OR* |
| print_timestats_every | 0 | Print interesting timing statistics periodically |   -1  :: "don't report"*OR*  0   :: "don't report"*OR*  1:* :: "report every so many iterations"*OR* |
| print_grid_info | yes | Print information about the grids on regridding |  |
| output_timers_every | 0 | Print detailed statistics periodically |   -1  :: "don't report"*OR*  0   :: "don't report"*OR*  1:* :: "report every so many iterations"*OR* |
| timer_file | "carpet-timing-statistics" | File name in which detailed timing statistics are collected |   "^$"   :: "empty filename: no file output"*OR*  "^.+$" :: "file name"*OR* |
| output_initialise_timer_tree | "no" | Output timing information in tree form to standard output for Initialise |  |
| output_timer_tree_every | 0 | Output timing information in tree form to standard output for Evolve every so many iterations |   0   :: "don't report"*OR*  1:* :: "report every so many iterations"*OR* |
| output_xml_timer_tree | "no" | Output timing information in tree form as XML |  |
| recompose_verbose | "no" | Output debug information during recomposing |  |
| processor_topology | "automatic" | How to determine the processor topology |   "manual"    :: "Specified by processor_topology_*"*OR*  "along-z"   :: "Split the region along the z direction only"*OR*  "along-dir" :: "Split the region along one direction only"*OR*  "automatic" :: "Choose the topology automatically"*OR*  "recursive" :: "Choose the topology automatically, using a different algorithm that may lead to better load balancing"*OR*  "balanced"  :: "Choose the topology automatically, ensuring a maximum load balance"*OR* |
| processor_topology_3d_x | 1 | Number of processors in x-direction |   1:* :: "must be positive"*OR* |
| processor_topology_3d_y | 1 | Number of processors in y-direction |   1:* :: "must be positive"*OR* |
| processor_topology_3d_z | 1 | Number of processors in z-direction |   1:* :: "must be positive"*OR* |
| split_direction | 2 | Direction in which the domain should be split (for processor_topology=along-dir) |   0:* :: "0 for x, 1 for y, 2 for z, etc."*OR* |
| no_split_direction | -1 | Direction in which the domain must not be split (for processor_topology=automatic) |   -1  :: "split in all directions"*OR*  0:* :: "0 for x, 1 for y, 2 for z, etc."*OR* |
| constant_load_per_processor | "no" | Keep the load per processor constant -- this is meant for benchmarks |  |
| aspect_ratio_x | 1.0 | Desired aspect ratio for each processor's domain |   (0:* :: ""*OR* |
| aspect_ratio_y | 1.0 | Desired aspect ratio for each processor's domain |   (0:* :: ""*OR* |
| aspect_ratio_z | 1.0 | Desired aspect ratio for each processor's domain |   (0:* :: ""*OR* |
| min_points_per_proc | 0 | Minimum number of grid points per processor |   0:* :: "that many"*OR* |
| split_components | "yes" | Split components onto processes; without this, one needs many components and few processes |  |
| granularity | 1 | When splitting components, create sizes that are multiples of this granularity |   1:* :: "TODO: query CoordBase or related thorns for this information"*OR* |
| granularity_boundary | 0 | When splitting components, assume this many boundary points that don't count towards the granularity |   0:* :: "TODO: use CoordBase's number of boundary points for this"*OR* |
| ghost_zone_cost | 0.025 | Relative cost of ghost zones for 'recursive' load balancing |   0:* :: ""*OR* |
| maximum_imbalance | 0.1 | Maximum load imbalance |   (0.0:* :: ""*OR* |
| same_number_of_components_on_each_process | "yes" | Ensure that each process has the same number of components, adding empty dummy components if necessary |  |
| num_threads | -1 | Number of threads per process |   -1  :: "use system default, probably influenced by OMP_NUM_THREADS"*OR*  1:* :: "use this many threads"*OR* |
| set_cpu_affinity | "no" | Set the process CPU affinity, overwriting the respective system setting |  |
| grid_structure_filename | "" | File name to output grid structure to (empty = no output) |   ".*" :: "must be a legal file name"*OR* |
| grid_coordinates_filename | "" | File name to output grid coordinates to (empty = no output) |   ".*" :: "must be a legal file name"*OR* |
| init_each_timelevel | "no" | Call initial data routines once for each timelevel |  |
| init_fill_timelevels | "no" | Fill past time levels from current time level after calling initial data routines |  |
| prolongate_initial_data | "no" | Prolongate the refined regions during initial data generation |  |
| regrid_during_initialisation | "no" | Regrid while initialising |  |
| regrid_during_recovery | "no" | Regrid while recovering |  |
| regrid_in_level_mode | "yes" | Regrid in level mode (instead of singlemap mode), enabling more efficient processor distributions when there are multiple maps |  |
| time_interpolation_during_regridding | "yes" | Interpolate finer levels in time during regridding |  |
| output_after_regridding | "no" | Call OutputGH after regridding |  |
| init_3_timelevels | "no" | Set up 3 timelevels of initial data |  |
| adaptive_stepsize | "no" | Allow adaptive timestep sizes |  |
| use_unusedpoints_mask | "no" | Turn on storage and usage of 'unusedpoints_mask' |  |


### CarpetSlab

Cactus Code Thorn CarpetSlab
Author(s)    : Erik Schnetter <schnetter@cct.lsu.edu>
Maintainer(s): Erik Schnetter <schnetter@cct.lsu.edu>
Licence      : GPLv2+
--------------------------------------------------------------------------

1. Purpose

This thorn provides hyperslabbing for Carpet.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### CycleClock

| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| register_clock | "yes" | Register cycle counter as Cactus clock |  |


### CarpetIOASCII

Cactus Code Thorn CarpetIOASCII
Author(s)    : Erik Schnetter <schnetter@cct.lsu.edu>
Maintainer(s): Erik Schnetter <schnetter@cct.lsu.edu>
Licence      : GPLv2+
--------------------------------------------------------------------------

1. Purpose

This thorn provides ASCII output for Carpet.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| one_file_per_group | "no" | Write one file per group instead of per variable |  |
| output_all_timelevels | "no" | Output all timelevels instead of only the current |  |
| separate_grids | "yes" | Separate grid levels in the output file by additional empty lines |  |
| separate_components | "no" | Separate grid components in the output file by additional empty lines |  |
| compact_format | "no" | Use a more compact output format with fewer columns and lines |  |
| use_grid_coordinates | "no" | Use grid coordinate grid functions to obtain coordinate locations |  |
| out_precision | 15 | How many digits to output floating-point numbers with |   0:* :: "Number of precision digits"*OR* |
| out0D_dir | "" | Name of 0D ASCII output directory, overrides IO::out_dir |   "^$" :: "Empty: use IO::out_dir"*OR*  ".+" :: "Not empty: directory name"*OR* |
| out1D_dir | "" | Name of 1D ASCII output directory, overrides IO::out_dir |   "^$" :: "Empty: use IO::out_dir"*OR*  ".+" :: "Not empty: directory name"*OR* |
| out2D_dir | "" | Name of 2D ASCII output directory, overrides IO::out_dir |   "^$" :: "Empty: use IO::out_dir"*OR*  ".+" :: "Not empty: directory name"*OR* |
| out3D_dir | "" | Name of 3D ASCII output directory, overrides IO::out_dir |   "^$" :: "Empty: use IO::out_dir"*OR*  ".+" :: "Not empty: directory name"*OR* |
| out0D_vars | "" | Variables to output in 0D ASCII file format |   "" :: "List of group or variable names"*OR* |
| out1D_vars | "" | Variables to output in 1D ASCII file format |   "" :: "List of group or variable names"*OR* |
| out2D_vars | "" | Variables to output in 2D ASCII file format |   "" :: "List of group or variable names"*OR* |
| out3D_vars | "" | Variables to output in 3D ASCII file format |   "" :: "List of group or variable names"*OR* |
| out0D_criterion | "default" | Criterion to select 0D ASCII output intervals, overrides out_every |   "default"   :: "Use IO::out_criterion"*OR*  "never"     :: "Never output"*OR*  "iteration" :: "Output every so many iterations"*OR*  "divisor"   :: "Output if iteration mod divisor == 0."*OR*  "time"      :: "Output every that much coordinate time"*OR* |
| out1D_criterion | "default" | Criterion to select 1D ASCII output intervals, overrides out_every |   "default"   :: "Use IO::out_criterion"*OR*  "never"     :: "Never output"*OR*  "iteration" :: "Output every so many iterations"*OR*  "divisor"   :: "Output if (iteration % out_every) == 0."*OR*  "time"      :: "Output every that much coordinate time"*OR* |
| out2D_criterion | "default" | Criterion to select 2D ASCII output intervals, overrides out_every |   "default"   :: "Use IO::out_criterion"*OR*  "never"     :: "Never output"*OR*  "iteration" :: "Output every so many iterations"*OR*  "divisor"   :: "Output if (iteration % out_every) == 0."*OR*  "time"      :: "Output every that much coordinate time"*OR* |
| out3D_criterion | "default" | Criterion to select 3D ASCII output intervals, overrides out_every |   "default"   :: "Use IO::out_criterion"*OR*  "never"     :: "Never output"*OR*  "iteration" :: "Output every so many iterations"*OR*  "divisor"   :: "Output if (iteration % out_every) == 0."*OR*  "time"      :: "Output every that much coordinate time"*OR* |
| out0D_every | -2 | How often to do 0D ASCII output, overrides out_every |   1:*  :: "Output every so many time steps"*OR*  -1:0 :: "No output"*OR*  -2   :: "Use IO::out_every"*OR* |
| out1D_every | -2 | How often to do 1D ASCII output, overrides out_every |   1:*  :: "Output every so many time steps"*OR*  -1:0 :: "No output"*OR*  -2   :: "Use IO::out_every"*OR* |
| out2D_every | -2 | How often to do 2D ASCII output, overrides out_every |   1:*  :: "Output every so many time steps"*OR*  -1:0 :: "No output"*OR*  -2   :: "Use IO::out_every"*OR* |
| out3D_every | -2 | How often to do 3D ASCII output, overrides out_every |   1:*  :: "Output every so many time steps"*OR*  -1:0 :: "No output"*OR*  -2   :: "Use IO::out_every"*OR* |
| out0D_dt | -2 | How often to do 0D ASCII output, overrides IO::out_dt |   (0:* :: "In intervals of that much coordinate time"*OR*   0   :: "As often as possible"*OR*  -1   :: "Disable output"*OR*  -2   :: "Default to IO::out_dt"*OR* |
| out1D_dt | -2 | How often to do 1D ASCII output, overrides IO::out_dt |   (0:* :: "In intervals of that much coordinate time"*OR*   0   :: "As often as possible"*OR*  -1   :: "Disable output"*OR*  -2   :: "Default to IO::out_dt"*OR* |
| out2D_dt | -2 | How often to do 2D ASCII output, overrides IO::out_dt |   (0:* :: "In intervals of that much coordinate time"*OR*   0   :: "As often as possible"*OR*  -1   :: "Disable output"*OR*  -2   :: "Default to IO::out_dt"*OR* |
| out3D_dt | -2 | How often to do 3D ASCII output, overrides IO::out_dt |   (0:* :: "In intervals of that much coordinate time"*OR*   0   :: "As often as possible"*OR*  -1   :: "Disable output"*OR*  -2   :: "Default to IO::out_dt"*OR* |
| out0D_point_xi | 0 | x-index (counting from 0) for 0D points |   0:* :: ""*OR* |
| out0D_point_yi | 0 | y-index (counting from 0) for 0D points |   0:* :: ""*OR* |
| out0D_point_zi | 0 | z-index (counting from 0) for 0D points |   0:* :: ""*OR* |
| out0D_point_x | 0 | x coordinate for 0D points |   *:* :: ""*OR* |
| out0D_point_y | 0 | y coordinate for 0D points |   *:* :: ""*OR* |
| out0D_point_z | 0 | z coordinate for 0D points |   *:* :: ""*OR* |
| out1D_x | "yes" | Do 1D IOASCII output in the x-direction |  |
| out1D_y | "yes" | Do 1D IOASCII output in the y-direction |  |
| out1D_z | "yes" | Do 1D IOASCII output in the z-direction |  |
| out1D_xline_yi | 0 | y-index (counting from 0) for 1D lines in x-direction |   0:* :: ""*OR* |
| out1D_xline_zi | 0 | z-index (counting from 0) for 1D lines in x-direction |   0:* :: ""*OR* |
| out1D_yline_xi | 0 | x-index (counting from 0) for 1D lines in y-direction |   0:* :: ""*OR* |
| out1D_yline_zi | 0 | z-index (counting from 0) for 1D lines in y-direction |   0:* :: ""*OR* |
| out1D_zline_xi | 0 | x-index (counting from 0) for 1D lines in z-direction |   0:* :: ""*OR* |
| out1D_zline_yi | 0 | y-index (counting from 0) for 1D lines in z-direction |   0:* :: ""*OR* |
| out1D_xline_y | 0 | y coordinate for 1D lines in x-direction |   *:* :: ""*OR* |
| out1D_xline_z | 0 | z coordinate for 1D lines in x-direction |   *:* :: ""*OR* |
| out1D_yline_x | 0 | x coordinate for 1D lines in y-direction |   *:* :: ""*OR* |
| out1D_yline_z | 0 | z coordinate for 1D lines in y-direction |   *:* :: ""*OR* |
| out1D_zline_x | 0 | x coordinate for 1D lines in z-direction |   *:* :: ""*OR* |
| out1D_zline_y | 0 | y coordinate for 1D lines in z-direction |   *:* :: ""*OR* |
| out2D_xy | "yes" | Do 2D IOASCII output in the xy-direction |  |
| out2D_xz | "yes" | Do 2D IOASCII output in the xz-direction |  |
| out2D_yz | "yes" | Do 2D IOASCII output in the yz-direction |  |
| out2D_xyplane_zi | 0 | z-index (counting from 0) for 2D planes in xy-direction |   0:* :: ""*OR* |
| out2D_xzplane_yi | 0 | y-index (counting from 0) for 2D planes in xz-direction |   0:* :: ""*OR* |
| out2D_yzplane_xi | 0 | x-index (counting from 0) for 2D planes in yz-direction |   0:* :: ""*OR* |
| out2D_xyplane_z | 0 | z coordinate for 2D planes in xy-direction |   *:* :: ""*OR* |
| out2D_xzplane_y | 0 | y coordinate for 2D planes in xz-direction |   *:* :: ""*OR* |
| out2D_yzplane_x | 0 | x coordinate for 2D planes in yz-direction |   *:* :: ""*OR* |
| output_symmetry_points | "yes" | Output symmetry points (assuming that there are nghostzones symmetry points) |  |
| output_ghost_points | "yes" | Output ghost points |  |
| output_boundary_points | "yes" | Output outer boundary points (assuming that there are nghostzones boundary points) |  |
| out3D_ghosts | "yes" | Output ghost zones (DEPRECATED) |  |
| out3D_outer_ghosts | "yes" | Output outer boundary zones (assuming that there are nghostzones boundary points) (DEPRECATED) |  |
| out1D_d | "yes" | Do output along the diagonal |  |


### CarpetReduce

Cactus Code Thorn CarpetReduce
Author(s)    : Erik Schnetter <schnetter@cct.lsu.edu>
Maintainer(s): Erik Schnetter <schnetter@cct.lsu.edu>
Licence      : GPLv2+
--------------------------------------------------------------------------

1. Purpose

This thorn provides parallel reduction operators for Carpet.



This thorn now uses a weight function.  This makes it possible to
perform physically meaningful spatial reduction operations.  The
weight is 1 for all "normal" grid points.

The weight is set to 0 on symmetry and possible the outer boundary,
and it might be set to 1/2 on the edge of the boundary.  Setting this
depends on the coordinate thorn, and currently works only when the
coordinates are defined via CoordBase.

The weight is also reduced or set to 0 on coarser grids that are
overlaid by finer grid.

The weight should also be reduced or set to 0 near and in excised
regions.  This should happen in conjunction with an excision boundary
thorn.

This weigth function should probably be extracted into its own thorn
MaskBase, so that many thorns can easily operate on it.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "no" | Produce screen output while running |  |
| debug_iweight | "no" | Allow debugging iweight grid function by keeping it allocated |  |
| min_max_time_interpolation | "yes" | Interpolate in time for min/max reductions |  |


### CarpetEvolutionMask

Cactus Code Thorn CarpetEvolutionMask
Author(s)    : Christian D. Ott <cott@aei.mpg.de>
               Erik Schnetter <schnetter@cct.lsu.edu>
Maintainer(s): Christian D. Ott <cott@aei.mpg.de>
               Erik Schnetter <schnetter@cct.lsu.edu>
Licence      : GPLv2+
--------------------------------------------------------------------------

1. Purpose

Provide a grid function "evolution_mask" which is zero for all grid
points that do not need to be evolved because they are going to be
restricted from a finer grid.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "no" | Do verbose debugging output |  |
| enforce_mask | "no" | Set RHS to zero where mask value is 0? |  |
| writeNaNs | "no" | Write NaNs into masked regions |  |
| enforce_vars | "" | Enforce mask in MoL automatically on the following variables |   "" :: "a list of variables or groups"*OR* |
| enlarge_evolved_region_by | 0 | enlarge or shrink evolved region |  0:*  :: "positive values enlarge the evolved region"*OR* *:0) :: "negative values shrink the evolved region. Up to buffer_width + nghostzones points can be removed"*OR* |
| provide_buffer_mask | "no" | Compute mask indicating which points need only be computed for some MoL substeps |  |


### CarpetRegridTest

Cactus Code Thorn CarpetRegridTest
Thorn Author(s)     : Christian D. Ott <cott@aei.mpg.de>
Thorn Maintainer(s) : Christian D. Ott <cott@aei.mpg.de>
--------------------------------------------------------------------------

Purpose of the thorn: This thorn tries to test Carpet Progressive Meshrefinement



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| radius | 0.0 | The radius of the gaussian wave |  *:*  :: "No restriction"*OR* |
| sigma | 0.1 | The sigma for the gaussian wave |  0:* :: "Positive"*OR* |
| amplitude | 1.0 | The amplitude of the wave |   *:* :: "No restriction"*OR* |


### CarpetInterp

Cactus Code Thorn CarpetInterp
Author(s)    : Erik Schnetter <schnetter@cct.lsu.edu>
Maintainer(s): Erik Schnetter <schnetter@cct.lsu.edu>
Licence      : GPLv2+
--------------------------------------------------------------------------

1. Purpose

This thorn provides a parallel interpolator for Carpet.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| barriers | "no" | Insert barriers at strategic places for debugging purposes (slows down execution) |  |
| poison | -4.20042e+30 | Poison value |   *:* :: ""*OR* |
| ipoison | -420042 | Integer poison value |   *:* :: ""*OR* |
| tree_search | "yes" | Use a tree search to find the source processor |  |
| check_tree_search | "no" | Cross-check the result of the tree search |  |


### CarpetIntegrateTest

Cactus Code Thorn CarpetIntegrateTest
Thorn Author(s)     : Burkhard Zink <bzink@mpa-garching.mpg.de>
Thorn Maintainer(s) : Erik Schnetter <schnetter@aei.mpg.de>
--------------------------------------------------------------------------

Purpose of the thorn:

Test integration in Carpet.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| constant | 1.0 | Integrand constant |   *:* :: ""*OR* |
| timefact | 0.0 | Time dependence of integrand |   *:* :: ""*OR* |


### doc

### CarpetProlongateTest

Cactus Code Thorn CarpetProlongateTest
Author(s)    : Erik Schnetter <schnetter@cct.lsu.edu>
Maintainer(s): Erik Schnetter <schnetter@cct.lsu.edu>
Licence      : GPL
--------------------------------------------------------------------------

1. Purpose

Test prolongation in Carpet.

The basic idea is to set up a grid function containing a polynomial of
a certain order of the coordinates. Prolongation (interpolation) with
sufficiently high order must preserve these polynomials exactly (up to
floating-point round-off).

We test restriction as well, but the restriction operators are not
high-order accurate. Since restriction is first order accurate, and we
disable the restriction tests for the higher orders.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| power_x | 0 | Polynomial power of x coordinate |   *:* :: ""*OR* |
| power_y | 0 | Polynomial power of y coordinate |   *:* :: ""*OR* |
| power_z | 0 | Polynomial power of z coordinate |   *:* :: ""*OR* |
| power_t | 0 | Polynomial power of t coordinate |   *:* :: ""*OR* |
| prolongation | "Lagrange" | The prolongation operator to use |   "Lagrange"          :: ""*OR*  "ENO"               :: ""*OR*  "WENO"              :: ""*OR*  "TVD"               :: ""*OR*  "Lagrange_monotone" :: ""*OR* |
| interpolator | "Lagrange | The interpolator to use |   "" :: "must be a registered interpolator"*OR* |
| interpolator_options | "order=2" | Options for the interpolator |   "" :: "must be a valid options specification"*OR* |
| interp_nx | 4 | number of points for interpolated region |   0:* :: ""*OR* |
| interp_ny | 4 | number of points for interpolated region |   0:* :: ""*OR* |
| interp_nz | 4 | number of points for interpolated region |   0:* :: ""*OR* |
| interp_xmin | -0.5 | xmin for interpolated region |   *:* :: ""*OR* |
| interp_ymin | -0.5 | ymin for interpolated region |   *:* :: ""*OR* |
| interp_zmin | -0.5 | zmin for interpolated region |   *:* :: ""*OR* |
| interp_xmax | +0.5 | xmax for interpolated region |   *:* :: ""*OR* |
| interp_ymax | +0.5 | ymax for interpolated region |   *:* :: ""*OR* |
| interp_zmax | +0.5 | zmax for interpolated region |   *:* :: ""*OR* |


### CarpetLib

Cactus Code Thorn CarpetLib
Author(s)    : Erik Schnetter <schnetter@cct.lsu.edu>
Maintainer(s): Erik Schnetter <schnetter@cct.lsu.edu>
Licence      : GPLv2+
--------------------------------------------------------------------------

1. Purpose

This thorn contains the backend library that provides mesh refinement.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "no" | Print info to the screen |  |
| barriers | "no" | Insert barriers at strategic places for debugging purposes (slows down execution) |  |
| commstate_verbose | "no" | Print debug info from the commstate class |  |
| omit_prolongation_points_when_restricting | "no" | Do not restrict to points which are used to prolongate the boundary | # |
| proper_nesting_distance | 4 | Minimum distance (in grid points) between two level interfaces | #  0:* :: "any non-negative value is fine; the default value is just a guess"*OR*# |
| use_dgfe | "no" | Use DGFE operators instead of Lagrange operators |  |
| interpolate_from_buffer_zones | "no" | Use buffer points for interpolation |  |
| use_loopcontrol_in_operators | "no" | Use LoopControl to parallelize AMR operators |  |
| use_openmp | "yes" | Use OpenMP to parallelize AMR operators |  |
| use_higher_order_restriction | "no" | Use third order cell centered restriction operators instead of first order |  |
| restriction_order_space | 3 | Order of restriction operator to use with use_higher_order_restriction |   1 :: "linear interpolation, this is Carpet's original implementation"*OR*  3 :: "third order accurate restriction for grid functions where prolongation is not (W)ENO"*OR*  5 :: "fifth order accurate restriction for grid functions where prolongation is not (W)ENO"*OR* |
| support_staggered_operators | "no" | Provide one extra ghost point during restriction for staggered operators - EXPERIMENTAL |  |
| output_bboxes | "no" | Output bounding box information to the screen |  |
| check_bboxes | "yes" | Check bounding box information for self-consistency |  |
| poison_new_memory | "no" | Try to catch uninitialised data by setting newly allocated memory to values that will catch your attention |  |
| electric_fence | "no" | Surround each allocated memory block by canaries to check for out-of-bounds accesses |  |
| fence_width | 1 | number of guard cells to use |   1:* :: "any number of cells"*OR* |
| poison_value | 255 | Integer value (0..255) used to poison new timelevels (with memset) |   0:255 :: "Must fit into a byte.  Use 0 for zero, 255 for nan, and e.g. 113 for a large value."*OR* |
| deadbeef | 666 | A strange integer value that indicates that something has gone wrong; the integer equivalent of a nan |   *:* :: "should be large and positive"*OR* |
| max_core_size_MB | -2 | Maximum size of a core file, set via setrlimit |   -2  :: "unchanged"*OR*  -1  :: "unlimited"*OR*  0:* :: "limited"*OR* |
| max_memory_size_MB | -2 | Maximum amount of memory per MPI process, set via setrlimit |   -2  :: "unchanged"*OR*  -1  :: "unlimited"*OR*  0:* :: "limited"*OR* |
| test_backtrace | "no" | Kill yourself to test the backtrace mechanism |  |
| print_timestats_every | -1 | Print timing statistics periodically |   -1  :: "don't report"*OR*  0   :: "report after initialisation"*OR*  1:* :: "report every so many iterations"*OR* |
| timestat_file | "carpetlib-timing-statistics" | File name in which timestat output is collected (because stdout from the root node may not be enough) |   "^$"   :: "empty filename: no file output"*OR*  "^.+$" :: "file name"*OR* |
| use_ipm_timing_regions | no | Call IPM (via MPI_Pcontrol) to define regions |  |
| print_memstats_every | -1 | Report periodically how much memory is used per process |   -1  :: "don't report"*OR*  0   :: "report after setting up initial data"*OR*  1:* :: "report every so many iterations"*OR* |
| max_allowed_memory_MB | 0 | Maximum allowed amount of memory per process that can be allocated for grid variables (in Megabytes) |   -1  :: "no maximum"*OR*  0   :: "no maximum"*OR*  1:* :: "abort if more memory is used"*OR* |
| memstat_file | "carpetlib-memory-statistics" | File name in which memstat output is collected (because stdout from the root node may not be enough) |   "^$"   :: "empty filename: no file output"*OR*  "^.+$" :: "file name"*OR* |
| combine_recompose | "yes" | Recompose all grid functions of one refinement levels at once |  |
| avoid_arraysize_bytes | 0 | Avoid array sizes that are multiples of this | #   0   :: "don't avoid anything"*OR*#   2:* :: ""*OR*#  |
| message_size_multiplier | 1 | Enlarge size of transmitted messages by this factor |   1:* :: ""*OR* |
| message_count_multiplier | 1 | Transmit messages this many times |   1:* :: ""*OR* |
| interleave_communications | "no" | Try to interleave communications with each other; each processor begins to communicate with its 'right neighbour' in rank, instead of with the root processor |  |
| barrier_between_stages | "no" | Add a barrier between the communication stages (slows down, but may make timing numbers easier to interpret) |  |
| check_communication_schedule | "no" | Check the communication schedule at run time (expensive) |  |
| combine_sends | "no" | Send data together and in order of processor ranks |  |
| use_mpi_send | "no" | Use MPI_Send instead of MPI_Isend |  |
| use_mpi_ssend | "no" | Use MPI_Ssend instead of MPI_Isend |  |
| pad_to_cachelines | "yes" | Pad arrays to the cache line size (only when VECTORISE_ALIGNED_ARRAYS is set) |  |


### PeriodicCarpet

Cactus Code Thorn PeriodicCarpet
Author(s)    : Erik Schnetter <eschnetter@perimeterinstitute.ca>
Maintainer(s): Erik Schnetter <eschnetter@perimeterinstitute.ca>
Licence      : GPL
--------------------------------------------------------------------------

1. Purpose

Periodic boundary conditions using Carpet's communication routines.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "no" | Produce screen output while applying boundary conditions |  |
| periodic | "no" | Periodic boundary conditions in all directions |  |
| periodic_x | "no" | Periodic boundary conditions in x-direction |  |
| periodic_y | "no" | Periodic boundary conditions in y-direction |  |
| periodic_z | "no" | Periodic boundary conditions in z-direction |  |


### CarpetRegrid2

Cactus Code Thorn CarpetRegrid2
Author(s)    : Erik Schnetter <schnetter@cct.lsu.edu>
Maintainer(s): Erik Schnetter <schnetter@cct.lsu.edu>
Licence      : GPLv2+
--------------------------------------------------------------------------

1. Purpose

Set up refined regions by specifying a set of centres and radii about
them.  The refined regions are then the conjunction of these regions.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "no" | Display regridding information on the terminal |  |
| veryverbose | "no" | Display much regridding information on the terminal |  |
| min_distance | 4 | Minimum distance (in grid points) between coarse and fine grid boundaries |   0:* :: ""*OR* |
| ensure_proper_nesting | "yes" | Ensure proper nesting automatically |  |
| freeze_unaligned_levels | "no" | Do not change refinement levels that do not exist at this time |  |
| freeze_unaligned_parent_levels | "no" | Do not change refinement levels where the parent does not exist at this time |  |
| min_fraction | 0.9 | Minimum fraction of required refined points that need to be present in a refined region |   0:* :: ""*OR* |
| snap_to_coarse | "no" | Ensure that the fine grid extent coincides with coarse grid points |  |
| granularity | 1 | Granularity of size of refined regions |   1:* :: ""*OR* |
| boundary_shiftout | 0 | Number of grid points added to the refinement boundary radius |   *:* :: ""*OR* |
| regrid_every | 0 | Regrid every n time steps |   -1   :: "regrid never"*OR*   0   :: "regrid during initial data calculation only"*OR*   1:* :: "regrid every n time steps"*OR* |
| symmetry_rotating90 | no | Ensure a 90 degree rotating symmetry about the z axis |  |
| symmetry_rotating180 | no | Ensure a 180 degree rotating symmetry about the z axis |  |
| symmetry_parity | no | parity  |  |
| symmetry_periodic_x | no | Ensure a periodicity symmetry in the x direction |  |
| symmetry_periodic_y | no | Ensure a periodicity symmetry in the y direction |  |
| symmetry_periodic_z | no | Ensure a periodicity symmetry in the z direction |  |
| expect_symmetric_grids | "no" | Expect a grid structure that is symmetric about the origin, and abort if it is not |  |
| adaptive_refinement | "no" | Use level_mask for adaptive refinement |  |
| adaptive_block_size | 8 | Block size for adaptive refinement |   1:* :: ""*OR* |
| adaptive_block_size_x | -1 | Block size in x direction for adaptive refinement |   -1 :: "use adaptive_block_size"*OR*  1:* :: ""*OR* |
| adaptive_block_size_y | -1 | Block size in y direction for adaptive refinement |   -1 :: "use adaptive_block_size"*OR*  1:* :: ""*OR* |
| adaptive_block_size_z | -1 | Block size in z direction for adaptive refinement |   -1 :: "use adaptive_block_size"*OR*  1:* :: ""*OR* |
| num_centres | 0 | Number of refinement centres |   0:10 :: ""*OR* |
| add_levels_automatically | "no" | Automatically add a new refinement level at each regrid |  |
| num_levels_1 | 1 | Number of refinement levels for this centre |   1:30 :: ""*OR* |
| active_1 | "yes" | Is this region active? |  |
| position_x_1 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| position_y_1 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| position_z_1 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| movement_threshold_1 | 0.0 | Minimum movement to trigger a regridding |   0:* :: ""*OR* |
| radius_rel_change_threshold_1 | 0.0 | Minimum RELATIVE change in radius to trigger a regridding |   0.0:* :: ""*OR* |
| num_levels_2 | 1 | Number of refinement levels for this centre |   1:30 :: ""*OR* |
| active_2 | "yes" | Is this region active? |  |
| position_x_2 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| position_y_2 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| position_z_2 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| movement_threshold_2 | 0.0 | Minimum movement to trigger a regridding |   0:* :: ""*OR* |
| radius_rel_change_threshold_2 | 0.0 | Minimum change in radius to trigger a regridding |   0.0:* :: ""*OR* |
| num_levels_3 | 1 | Number of refinement levels for this centre |   1:30 :: ""*OR* |
| active_3 | "yes" | Is this region active? |  |
| position_x_3 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| position_y_3 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| position_z_3 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| movement_threshold_3 | 0.0 | Minimum movement to trigger a regridding |   0:* :: ""*OR* |
| radius_rel_change_threshold_3 | 0.0 | Minimum change in radius to trigger a regridding |   0.0:* :: ""*OR* |
| num_levels_4 | 1 | Number of refinement levels for this centre |   1:30 :: ""*OR* |
| active_4 | "yes" | Is this region active? |  |
| position_x_4 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| position_y_4 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| position_z_4 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| movement_threshold_4 | 0.0 | Minimum movement to trigger a regridding |   0:* :: ""*OR* |
| radius_rel_change_threshold_4 | 0.0 | Minimum change in radius to trigger a regridding |   0.0:* :: ""*OR* |
| num_levels_5 | 1 | Number of refinement levels for this centre |   1:30 :: ""*OR* |
| active_5 | "yes" | Is this region active? |  |
| position_x_5 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| position_y_5 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| position_z_5 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| movement_threshold_5 | 0.0 | Minimum movement to trigger a regridding |   0:* :: ""*OR* |
| radius_rel_change_threshold_5 | 0.0 | Minimum change in radius to trigger a regridding |   0.0:* :: ""*OR* |
| num_levels_6 | 1 | Number of refinement levels for this centre |   1:30 :: ""*OR* |
| active_6 | "yes" | Is this region active? |  |
| position_x_6 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| position_y_6 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| position_z_6 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| movement_threshold_6 | 0.0 | Minimum movement to trigger a regridding |   0:* :: ""*OR* |
| radius_rel_change_threshold_6 | 0.0 | Minimum change in radius to trigger a regridding |   0.0:* :: ""*OR* |
| num_levels_7 | 1 | Number of refinement levels for this centre |   1:30 :: ""*OR* |
| active_7 | "yes" | Is this region active? |  |
| position_x_7 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| position_y_7 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| position_z_7 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| movement_threshold_7 | 0.0 | Minimum movement to trigger a regridding |   0:* :: ""*OR* |
| radius_rel_change_threshold_7 | 0.0 | Minimum change in radius to trigger a regridding |   0.0:* :: ""*OR* |
| num_levels_8 | 1 | Number of refinement levels for this centre |   1:30 :: ""*OR* |
| active_8 | "yes" | Is this region active? |  |
| position_x_8 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| position_y_8 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| position_z_8 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| movement_threshold_8 | 0.0 | Minimum movement to trigger a regridding |   0:* :: ""*OR* |
| radius_rel_change_threshold_8 | 0.0 | Minimum change in radius to trigger a regridding |   0.0:* :: ""*OR* |
| num_levels_9 | 1 | Number of refinement levels for this centre |   1:30 :: ""*OR* |
| active_9 | "yes" | Is this region active? |  |
| position_x_9 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| position_y_9 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| position_z_9 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| movement_threshold_9 | 0.0 | Minimum movement to trigger a regridding |   0:* :: ""*OR* |
| radius_rel_change_threshold_9 | 0.0 | Minimum change in radius to trigger a regridding |   0.0:* :: ""*OR* |
| num_levels_10 | 1 | Number of refinement levels for this centre |   1:30 :: ""*OR* |
| active_10 | "yes" | Is this region active? |  |
| position_x_10 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| position_y_10 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| position_z_10 | 0.0 | Position of this centre |   *:* :: ""*OR* |
| movement_threshold_10 | 0.0 | Minimum movement to trigger a regridding |   0:* :: ""*OR* |
| radius_rel_change_threshold_10 | 0.0 | Minimum change in radius to trigger a regridding |   0.0:* :: ""*OR* |


### TestCarpetGridInfo

CVS info   : $Header: /numrelcvs/ThornburgCVS/TestCarpetGridInfo/README,v 1.1 2006/07/03 14:41:50 jthorn Exp $

Cactus Code Thorn TestCarpetGridInfo
Thorn Author(s)     : Jonathan Thornburg <jthorn@aei.mpg.de>
Thorn Maintainer(s) : Jonathan Thornburg <jthorn@aei.mpg.de>
--------------------------------------------------------------------------

Purpose of the thorn:



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### CarpetMask

Cactus Code Thorn CarpetMask
Author(s)    : Erik Schnetter <schnetter@cct.lsu.edu>
Maintainer(s): Erik Schnetter <schnetter@cct.lsu.edu>
Licence      : GPLv2+
--------------------------------------------------------------------------

1. Purpose

Remove unwanted regions from Carpet's reduction operations.  This can
be used e.g. to excise the interior of horizons.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "no" | Produce screen output |  |


### CarpetIOScalar

Cactus Code Thorn CarpetIOScalar
Author(s)    : Erik Schnetter <schnetter@cct.lsu.edu>
Maintainer(s): Erik Schnetter <schnetter@cct.lsu.edu>
Licence      : GPLv2+
--------------------------------------------------------------------------

1. Purpose

This thorn provides scalar output for Carpet.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| one_file_per_group | "no" | Write one file per group instead of per variable |  |
| all_reductions_in_one_file | "no" | Write all requested reductions in one file instead of per reduction |  |
| out_precision | 15 | How many digits to output floating-point numbers with |   0:* :: "Number of precision digits"*OR* |
| outScalar_dir | "" | Name of scalar output directory, overrides out_dir |   "^$" :: "Empty: use IO::out_dir"*OR*  ".+" :: "Not empty: directory name"*OR* |
| outScalar_vars | "" | Variables to output in scalar form |   "" :: "A regex which matches everything"*OR* |
| outScalar_reductions | "count | List of reductions to output in scalar form |   "" :: "A regex which matches everything"*OR* |
| outScalar_criterion | "iteration" | Criterion to select scalar output intervals, overrides out_every |   "never"     :: "Never output"*OR*  "iteration" :: "Output every so many iterations"*OR*  "divisor"   :: "Output if iteration mod divisor == 0."*OR*  "time"      :: "Output every that much coordinate time"*OR* |
| outScalar_every | -2 | How often to do scalar output, overrides IO::out_every |    1:* :: "Output every so many time steps"*OR*  -1:0 :: "No output"*OR*  -2   :: "Default to IO::out_every"*OR* |
| outScalar_dt | -2 | How often to do scalar output, overrides out_dt |   (0:* :: "In intervals of that much coordinate time"*OR*   0   :: "As often as possible"*OR*  -1   :: "Disable output"*OR*  -2   :: "Default to IO::out_dt"*OR* |


### CarpetIOHDF5

Cactus Code Thorn CarpetIOHDF5
Author(s)    : Erik Schnetter <schnetter@cct.lsu.edu>
Maintainer(s): Erik Schnetter <schnetter@cct.lsu.edu>
Licence      : GPLv2+
--------------------------------------------------------------------------

1. Purpose

This thorn provides HDF5 based file I/O for Carpet.  See
http://www.hdfgroup.org/HDF5/ for information about HDF5.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| out_dir | "" | Name of CarpetIOHDF5 output directory, overrides 'IO::out_dir' |   "^$" :: "Empty: use IO::out_dir"*OR*  ".+" :: "Not empty: directory name"*OR* |
| out0D_dir | "" | Name of 0D HDF5 slice output directory, overrides IO::out_dir |   "^$" :: "Empty: use IO::out_dir"*OR*  ".+" :: "Not empty: directory name"*OR* |
| out1D_dir | "" | Name of 1D HDF5 slice output directory, overrides IO::out_dir |   "^$" :: "Empty: use IO::out_dir"*OR*  ".+" :: "Not empty: directory name"*OR* |
| out2D_dir | "" | Name of 2D HDF5 slice output directory, overrides IO::out_dir |   "^$" :: "Empty: use IO::out_dir"*OR*  ".+" :: "Not empty: directory name"*OR* |
| out3D_dir | "" | Name of 3D HDF5 slice output directory, overrides IO::out_dir |   "^$" :: "Empty: use IO::out_dir"*OR*  ".+" :: "Not empty: directory name"*OR* |
| out_vars | "" | Variables to output in CarpetIOHDF5 file format |   "" :: "List of group or variable names"*OR* |
| out0D_vars | "" | Variables to output in 0D HDF5 file format |   "" :: "List of group or variable names"*OR* |
| out1D_vars | "" | Variables to output in 1D HDF5 file format |   "" :: "List of group or variable names"*OR* |
| out2D_vars | "" | Variables to output in 2D HDF5 file format |   "" :: "List of group or variable names"*OR* |
| out3D_vars | "" | Variables to output in 3D HDF5 file format |   "" :: "List of group or variable names"*OR* |
| out_extension | ".h5" | File extension to use for CarpetIOHDF5 output |   "" :: "File extension (including a leading dot, if desired)"*OR* |
| out_criterion | "default" | Criterion to select CarpetIOHDF5 output intervals, overrides out_every |   "default"   :: "Use 'IO::out_criterion'"*OR*  "never"     :: "Never output"*OR*  "iteration" :: "Output every so many iterations"*OR*  "divisor"   :: "Output if (iteration % out_every) == 0."*OR*  "time"      :: "Output every that much coordinate time"*OR* |
| out0D_criterion | "default" | Criterion to select 0D HDF5 slice output intervals, overrides out_every |   "default"   :: "Use IO::out_criterion"*OR*  "never"     :: "Never output"*OR*  "iteration" :: "Output every so many iterations"*OR*  "divisor"   :: "Output if iteration mod divisor == 0."*OR*  "time"      :: "Output every that much coordinate time"*OR* |
| out1D_criterion | "default" | Criterion to select 1D HDF5 slice output intervals, overrides out_every |   "default"   :: "Use IO::out_criterion"*OR*  "never"     :: "Never output"*OR*  "iteration" :: "Output every so many iterations"*OR*  "divisor"   :: "Output if (iteration % out_every) == 0."*OR*  "time"      :: "Output every that much coordinate time"*OR* |
| out2D_criterion | "default" | Criterion to select 2D HDF5 slice output intervals, overrides out_every |   "default"   :: "Use IO::out_criterion"*OR*  "never"     :: "Never output"*OR*  "iteration" :: "Output every so many iterations"*OR*  "divisor"   :: "Output if (iteration % out_every) == 0."*OR*  "time"      :: "Output every that much coordinate time"*OR* |
| out3D_criterion | "default" | Criterion to select 3D HDF5 slice output intervals, overrides out_every |   "default"   :: "Use IO::out_criterion"*OR*  "never"     :: "Never output"*OR*  "iteration" :: "Output every so many iterations"*OR*  "divisor"   :: "Output if (iteration % out_every) == 0."*OR*  "time"      :: "Output every that much coordinate time"*OR* |
| out_every | -2 | How often to do CarpetIOHDF5 output, overrides out_every |   1:*  :: "Output every so many time steps"*OR*  -1:0 :: "No output"*OR*  -2   :: "Use 'IO::out_every'"*OR* |
| out0D_every | -2 | How often to do 0D HDF5 slice output, overrides out_every |   1:*  :: "Output every so many time steps"*OR*  -1:0 :: "No output"*OR*  -2   :: "Use IO::out_every"*OR* |
| out1D_every | -2 | How often to do 1D HDF5 slice output, overrides out_every |   1:*  :: "Output every so many time steps"*OR*  -1:0 :: "No output"*OR*  -2   :: "Use IO::out_every"*OR* |
| out2D_every | -2 | How often to do 2D HDF5 slice output, overrides out_every |   1:*  :: "Output every so many time steps"*OR*  -1:0 :: "No output"*OR*  -2   :: "Use IO::out_every"*OR* |
| out3D_every | -2 | How often to do 3D HDF5 slice output, overrides out_every |   1:*  :: "Output every so many time steps"*OR*  -1:0 :: "No output"*OR*  -2   :: "Use IO::out_every"*OR* |
| out_dt | -2 | How often to do CarpetIOHDF5 output, overrides 'IO::out_dt' |   (0:* :: "In intervals of that much coordinate time"*OR*   0   :: "As often as possible"*OR*  -1   :: "Disable output"*OR*  -2   :: "Default to 'IO::out_dt'"*OR* |
| out0D_dt | -2 | How often to do 0D HDF5 slice output, overrides IO::out_dt |   (0:* :: "In intervals of that much coordinate time"*OR*   0   :: "As often as possible"*OR*  -1   :: "Disable output"*OR*  -2   :: "Default to IO::out_dt"*OR* |
| out1D_dt | -2 | How often to do 1D HDF5 slice output, overrides IO::out_dt |   (0:* :: "In intervals of that much coordinate time"*OR*   0   :: "As often as possible"*OR*  -1   :: "Disable output"*OR*  -2   :: "Default to IO::out_dt"*OR* |
| out2D_dt | -2 | How often to do 2D HDF5 slice output, overrides IO::out_dt |   (0:* :: "In intervals of that much coordinate time"*OR*   0   :: "As often as possible"*OR*  -1   :: "Disable output"*OR*  -2   :: "Default to IO::out_dt"*OR* |
| out3D_dt | -2 | How often to do 3D HDF5 slice output, overrides IO::out_dt |   (0:* :: "In intervals of that much coordinate time"*OR*   0   :: "As often as possible"*OR*  -1   :: "Disable output"*OR*  -2   :: "Default to IO::out_dt"*OR* |
| out0D_point_xi | 0 | x-index (counting from 0) for 0D points |   0:* :: ""*OR* |
| out0D_point_yi | 0 | y-index (counting from 0) for 0D points |   0:* :: ""*OR* |
| out0D_point_zi | 0 | z-index (counting from 0) for 0D points |   0:* :: ""*OR* |
| out0D_point_x | 0 | x coordinate for 0D points |   *:* :: ""*OR* |
| out0D_point_y | 0 | y coordinate for 0D points |   *:* :: ""*OR* |
| out0D_point_z | 0 | z coordinate for 0D points |   *:* :: ""*OR* |
| out1D_x | "yes" | Do 1D HDF5 slice output in the x-direction |  |
| out1D_y | "yes" | Do 1D HDF5 slice output in the y-direction |  |
| out1D_z | "yes" | Do 1D HDF5 slice output in the z-direction |  |
| out1D_xline_yi | 0 | y-index (counting from 0) for 1D lines in x-direction |   0:* :: ""*OR* |
| out1D_xline_zi | 0 | z-index (counting from 0) for 1D lines in x-direction |   0:* :: ""*OR* |
| out1D_yline_xi | 0 | x-index (counting from 0) for 1D lines in y-direction |   0:* :: ""*OR* |
| out1D_yline_zi | 0 | z-index (counting from 0) for 1D lines in y-direction |   0:* :: ""*OR* |
| out1D_zline_xi | 0 | x-index (counting from 0) for 1D lines in z-direction |   0:* :: ""*OR* |
| out1D_zline_yi | 0 | y-index (counting from 0) for 1D lines in z-direction |   0:* :: ""*OR* |
| out1D_xline_y | 0 | y coordinate for 1D lines in x-direction |   *:* :: ""*OR* |
| out1D_xline_z | 0 | z coordinate for 1D lines in x-direction |   *:* :: ""*OR* |
| out1D_yline_x | 0 | x coordinate for 1D lines in y-direction |   *:* :: ""*OR* |
| out1D_yline_z | 0 | z coordinate for 1D lines in y-direction |   *:* :: ""*OR* |
| out1D_zline_x | 0 | x coordinate for 1D lines in z-direction |   *:* :: ""*OR* |
| out1D_zline_y | 0 | y coordinate for 1D lines in z-direction |   *:* :: ""*OR* |
| out2D_xy | "yes" | Do 2D HDF5 slice output in the xy-direction |  |
| out2D_xz | "yes" | Do 2D HDF5 slice output in the xz-direction |  |
| out2D_yz | "yes" | Do 2D HDF5 slice output in the yz-direction |  |
| out2D_xyplane_zi | 0 | z-index (counting from 0) for 2D planes in xy-direction |   0:* :: ""*OR* |
| out2D_xzplane_yi | 0 | y-index (counting from 0) for 2D planes in xz-direction |   0:* :: ""*OR* |
| out2D_yzplane_xi | 0 | x-index (counting from 0) for 2D planes in yz-direction |   0:* :: ""*OR* |
| out2D_xyplane_z | 0 | z coordinate for 2D planes in xy-direction |   *:* :: ""*OR* |
| out2D_xzplane_y | 0 | y coordinate for 2D planes in xz-direction |   *:* :: ""*OR* |
| out2D_yzplane_x | 0 | x coordinate for 2D planes in yz-direction |   *:* :: ""*OR* |
| output_all_timelevels | "no" | Output all timelevels instead of only the current |  |
| output_symmetry_points | "yes" | Output symmetry points (assuming that there are nghostzones symmetry points) |  |
| output_ghost_points | "yes" | Output ghost points |  |
| output_boundary_points | "yes" | Output outer boundary points (assuming that there are nghostzones boundary points) |  |
| output_buffer_points | "yes" | Output refinement buffer points |  |
| out3D_ghosts | "yes" | Output ghost zones (DEPRECATED) |  |
| out3D_outer_ghosts | "yes" | Output outer boundary zones (assuming that there are nghostzones boundary points) (DEPRECATED) |  |
| out1D_d | "yes" | Do output along the diagonal |  |
| checkpoint | "no" | Do checkpointing with CarpetIOHDF5 ? |  |
| checkpoint_next | "no" | Checkpoint at next iteration ? |  |
| checkpoint_every_divisor | -1 | Checkpoint if (iteration % out_every) == 0 |    1:* :: "Every so many iterations"*OR*  -1:0 :: "Disable periodic checkpointing"*OR* |
| use_reflevels_from_checkpoint | "no" | Use 'CarpetRegrid::refinement_levels' from the checkpoint file rather than from the parameter file ? |  |
| use_grid_structure_from_checkpoint | "yes" | Use the grid structure stored in the checkpoint file |  |
| one_file_per_proc | "no" | Write one file per process instead of per variable |  |
| one_file_per_group | "no" | Write one file per group instead of per variable |  |
| open_one_input_file_at_a_time | "no" | Open only one HDF5 file at a time when reading data from multiple chunked checkpoint/data files |   "no"  :: "Open all input files first, then import data (most efficient)"*OR*  "yes" :: "Process input files one after another (reduces memory requirements)"*OR* |
| skip_recover_variables | "" | Skip these variables while recovering |   "" :: ""*OR* |
| compression_level | 0 | Compression level to use for writing HDF5 data |   0:9 :: "Higher numbers compress better, a value of zero disables compression"*OR* |
| minimum_size_for_compression | 32768 | Only compress datasets larger than this many bytes |   0:* :: "This should to be large enough so that compression gains outweigh the overhead"*OR* |
| use_checksums | "no" | Use checksums for the HDF5 data |  |
| output_index | "no" | Output an index file for each output file |  |


### ReductionTest

CVS info   : $Header:$

Cactus Code Thorn ReductionTest
Thorn Author(s)     : Christian D. Ott <cott@aei.mpg.de>
Thorn Maintainer(s) : Christian D. Ott <cott@aei.mpg.de>
--------------------------------------------------------------------------

Purpose of the thorn:



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### RegridSyncTest

Cactus Code Thorn RegridSyncTest
Thorn Author(s)     : Christian D. Ott <cott@aei.mpg.de>
Thorn Maintainer(s) : Christian D. Ott <cott@aei.mpg.de>
--------------------------------------------------------------------------

Purpose of the thorn:

Test regridding.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### HighOrderWaveTest

Cactus Code Thorn HighOrderWaveTest
Author(s)    : Roland Haas <rhaas@caltech.edu>
Maintainer(s): Roland Haas <rhaas@caltech.edu>
Licence      : GPL
--------------------------------------------------------------------------

1. Purpose

Test Carpet's restriction with cell-centring and with higher-order
restriction operators.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


## EinsteinInitialData

### IDConstraintViolate

Cactus Code Thorn IDConstraintViolate
Authors    : Ed Seidel, Gabrielle Allen
CVS info   : $Header$
--------------------------------------------------------------------------

Purpose of the thorn:

To set up initial data for the metric and extrinsic curvature which 
violates the constraint equations.



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| radius | 0.0 | The radial position of the Gaussian wave |   0:* :: "Positive"*OR* |
| sigma | 0.1 | The sigma for the Gaussian wave |   (0:* :: "Positive"*OR* |
| amplitude | 0.001 | The amplitude of the waves |   *:* :: "No restriction"*OR* |


### TwoPunctures

Cactus Code Thorn TwoPunctures
Author(s)    : Marcus Ansorg
               Erik Schnetter
               Frank Löffler
Maintainer(s): Marcus Ansorg
               Erik Schnetter
Licence      : LGPLv2+
--------------------------------------------------------------------------

1. Purpose

Create initial for two puncture black holes using a single domain
spectral method.  This method is described in
Marcus Ansorg, Bernd Brügmann, Wolfgang Tichy,
"A single-domain spectral method for black hole puncture data",
PRD 70, 064011 (2004),
arXiv:gr-qc/0404056.

This thorn was extended to include source terms to be able
to handle matter with the same restrictions as the BHs before plus
zero momentum/ang. momentum (K_{ij} = 0).


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "no" | Print screen output while solving |  |
| keep_u_around | "no" | Keep the variable u around after solving |  |
| give_bare_mass | "yes" | User provides bare masses rather than target ADM masses |  |
| adm_tol | 1.0e-10 | Tolerance of ADM masses when give_bare_mass=no |   (0:*) :: ""*OR* |
| grid_setup_method | "Taylor | How to fill the 3D grid from the spectral grid |   "Taylor expansion" :: "use a Taylor expansion about the nearest collocation point (fast, but might be inaccurate)"*OR*  "evaluation"       :: "evaluate using all spectral coefficients (slow)"*OR* |
| npoints_A | 30 | Number of coefficients in the compactified radial direction |   4:* :: ""*OR* |
| npoints_B | 30 | Number of coefficients in the angular direction |   4:* :: ""*OR* |
| npoints_phi | 16 | Number of coefficients in the phi direction |   4:*:2 :: ""*OR* |
| Newton_tol | 1.0e-10 | Tolerance for Newton solver |   (0:*) :: ""*OR* |
| Newton_maxit | 5 | Maximum number of Newton iterations |   0:* :: ""*OR* |
| TP_epsilon | 0.0 | A small number to smooth out singularities at the puncture locations |   0:* :: ""*OR* |
| TP_Tiny | 0.0 | Tiny number to avoid nans near or at the pucture locations |   0:* :: "anything positive, usually very small"*OR* |
| TP_Extend_Radius | 0.0 | Radius of an extended spacetime instead of the puncture |   0:* :: "anything positive, should be smaller than the horizon"*OR* |
| par_b | 1.0 | x coordinate of the m+ puncture |   (0.0:*) :: ""*OR* |
| par_m_plus | 1.0 | mass of the m+ puncture |   0.0:*) :: ""*OR* |
| par_m_minus | 1.0 | mass of the m- puncture |   0.0:*) :: ""*OR* |
| target_M_plus | 0.5 | target ADM mass for m+ |   0.0:*) :: ""*OR* |
| target_M_minus | 0.5 | target ADM mass for m- |   0.0:*) :: ""*OR* |
| initial_lapse_psi_exponent | -2.0 | Exponent n for psi^-n initial lapse profile |   (*:*) :: "Should be negative"*OR* |
| swap_xz | "no" | Swap x and z coordinates when interpolating, so that the black holes are separated in the z direction |  |
| use_sources | "no" | Use sources? |  |
| rescale_sources | "yes" | If sources are used - rescale them after solving? |  |
| use_external_initial_guess | "no" | Set initial guess by external function? |  |
| do_residuum_debug_output | "no" | Output debug information about the residuum |  |
| do_initial_debug_output | "no" | Output debug information about initial guess |  |
| multiply_old_lapse | "no" | Multiply the old lapse with the new one |  |
| schedule_in_ADMBase_InitialData | "yes" | Schedule in (instead of after) ADMBase_InitialData |  |
| solve_momentum_constraint | "no" | Solve for momentum constraint? |  |


### TOVSolver

Cactus Code Thorn TOVSolver
Author(s)    : Ian Hawke
               Frank Löffler
               Luca Baiotti
               Christian David Ott
               Erik Schnetter
               Bruno Giacomazzo
Maintainer(s): Einstein Toolkit
Licence      : GPLv2+
--------------------------------------------------------------------------

1. Purpose

This thorn provides initial data for TOV star(s) in isotropic coordinates.



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| TOV_Num_TOVs | 1 | The number of TOVs |   1:* :: "Greater than 0"*OR* |
| TOV_Solve_for_TOVs | 3 | Solve for TOVs even if no TOV initial data was requested? |   0:3 :: "depreciated in favour of TOVSolver::TOV_Enforce_Interpolation"*OR* |
| TOV_Enforce_Interpolation | "no" | Enforce the interpolation of the data onto the Hydro GFs even without tov as specified initial data |  |
| TOV_Num_Radial | 100000 | The number of radial points for the ODE integration |   1:* :: "Greater than 0"*OR* |
| TOV_Gamma | 2.0 | The polytropic constant in P = K rho^Gamma |   1.0: :: "The physical range at high Lorentz factors is [1,2], but otherwise higher values of gamma can also be used"*OR* |
| TOV_K | 100.0 | The polytropic constant in P = K rho^Gamma |   (0.0:* :: "Greater than 0"*OR* |
| TOV_ProperPosition | "no" | For use only with two NSs, atm only handles equal mass |  |
| TOV_Fast_Interpolation | "yes" | Use faster interpolation algorithm? Default is yes. |  |
| TOV_Clear_Initial_Data | "yes" | Clear initial data (spacetime)? Default is yes. |  |
| TOV_Use_Old_Initial_Data | "no" | Take old initial data into account (spacetime)? Default is no. |  |
| TOV_Use_Old_Matter_Initial_Data | "no" | Use also old matter initial data? Default is no. |  |
| TOV_Conformal_Flat_Three_Metric | "no" | Use conformal factor to get the 3-metric flat. default is no |  |
| TOV_Populate_Timelevels | 1 | Populate that amount of timelevels |   1:3 :: "1 (default) to 3"*OR* |
| TOV_Momentum_Psi_Power | 0 | Power of Psi to be multiplied with J^i for Mom |   *:* :: "anything, 0 as default"*OR* |
| TOV_fake_evolution | 0 | Fake evolution by setting ID at every step |   *:* :: "anything, 0 as off (default), everything else as on"*OR* |
| TOV_save_to_datafile | "" | Only save data to file and exit |   ".*" :: "Any filename, not used if empty"*OR* |


### IDAxiBrillBH

Cactus Code Thorn IDAxiBrillBH
Author(s)    : Steve Brandt
               Paul Walker
               Ryoji Takahashi
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn calculates initial data for a black hole distorted by an
even parity perturbation.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| debug_ii | 14 | i coordinate for per-2D-grid-point debug printing | *:* :: "any integer"*OR* |
| debug_jj | 15 | j coordinate for per-2D-grid-point debug printing | *:* :: "any integer"*OR* |
| debug_i | 14 | i coordinate for per-grid-point debug printing | *:* :: "any integer"*OR* |
| debug_j | 15 | j coordinate for per-grid-point debug printing | *:* :: "any integer"*OR* |
| debug_k | 10 | k coordinate for per-grid-point debug printing | *:* :: "any integer"*OR* |
| amp | 0.1 | Brill wave amplitude |   *:* :: "No restriction"*OR* |
| eta0 | 0.0 | Brill wave center (in eta coords) |   *:* :: "No restriction"*OR* |
| sigma | 1.0 | Brill wave width (in eta) |   *:* :: "No restriction"*OR* |
| etamax | 5.0 | eta value for outer edge of grid |   *:* :: "No restriction"*OR* |
| n | 2 | sin^n theta in Brill wave |   *:* :: "No restriction"*OR* |
| ne | 300 | eta resolution for solve |   *:* :: "No restriction"*OR* |
| nq | 50 | theta resolution for solve |   *:* :: "No restriction"*OR* |
| error_tolerance | 1.0e-12 | tolerance parameter for elliptic solver | (0:* :: "any positive real number"*OR* |
| interpolator_pars | "" | parameters for the interpolation operator | ".*" :: \*OR*  "any nonempty string acceptable to Util_TableSetFromString()		\*OR*   and to the interpolator, or the empty string to use 'order=n',	\*OR*   where  n  is specified by the  interpolation_order  parameter"*OR* |
| interpolation_order | 1 | Order for interpolation | 0:9 :: "any integer accepted by the interpolator"*OR* |


### IDLinearWaves

Cactus Code Thorn IDLinearWaves
Author(s)    : Malcolm Tobias
               Joan Masso
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Provides analytic data for linear gravitational waves.




| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| amplitude | 0.001 | Amplitude of the wave: both for teuk and plane |   0: :: "positive amplitude"*OR* |
| mvalue | 0 | m value for teukwaves waves: integer from -2 to 2 |   -2:2 :: "implemented : m = -2..2"*OR* |
| wavecenter | 0.0 | linears waves thingie |   : :: ""*OR* |
| wavelength | 2.0 | linearwaves wave length |   0: :: "positive wavelength"*OR* |
| wavepulse | 1.0 | planewaves thingy for the gaussian pulse |   0: :: "positive pulse"*OR* |
| wavetheta | 0.0 | Theta angle for planewaves |   : :: ""*OR* |
| wavephi | 0.0 | Phi angle for planewaves |  : :: ""*OR* |
| parity | "even" | Parity for teukwaves: even or odd |   "even" :: "even parity"*OR*  "odd"  :: "odd parity"*OR* |
| packet | "eppley" | Packet for teukwaves: eppley,evans,square |   "eppley" :: "Eppley type"*OR*  "evans"  :: "Evans type"*OR*  "square" :: "Square type"*OR* |
| wavesgoing | "both" | in and outgoing waves... |   "in"   :: "Ingoing wave"*OR*  "out"  :: "Outgoing wave"	*OR*  "both" :: "In and outgoing wave"*OR* |
| teuk_no_vee | "no" | Initialize Teuk. waves with V=0? |   "no" :: "Bona Masso setting"*OR*  "yes":: "Bona Masso setting"*OR* |


### Meudon_Bin_NS

Cactus Code Thorn Meudon_Bin_NS
Author(s)    : Erik Schnetter
               Frank Löffler
               Roberto De Pietri
Maintainer(s): Einstein Toolkit Maintainers
Licence      : GPL
--------------------------------------------------------------------------

1. Purpose

Import LORENE Bin_NS binary neutron star initial data.

This thorn is one of three closely related thorns Meudon_Bin_BH,
Meudon_Bin_NS, and Meudon_Mag_NS which all import LORENE initial data.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| filename | "" | Input file name containing LORENE data |   "" :: "Any filename, potentially including path"*OR* |
| eos_table | "2D_Polytrope" | Initial data EOS identifyer |   "" :: "Any valid EOS name in EOSOmni"*OR* |
| eos_table_filepath | "" | Directory containing the EOS file used to generate the initial data |   "" :: "Directory name, default (empty string) is 'do not tell Lorene to overwrite info found in Lorene data'"*OR* |
| recalculate_eps | "yes" | Should eps be recalculated (default), or taken from Lorene data? |  |
| eos_precision | 1.e-8 | precision of EOS calculations in EOSOmni |   0:* :: "any positive number"*OR* |


### NoExcision

CVS info   : $Header$

Cactus Code Thorn NoExcision
Thorn Author(s)     : Erik Schnetter <schnetter@aei.mpg.de>
Thorn Maintainer(s) : Erik Schnetter <schnetter@aei.mpg.de>
--------------------------------------------------------------------------

Purpose of the thorn:

Overwrite the regions near a singularity with "something else",
possibly flat space, ignoring the constraints.  This is instead of
excision.

This method is modelled after an idea by Denis Pollney, following an
serendipitous bug by Ian Hawke.  The word "serendipity" was coined by
Horace Walpole in the 18th century, from the Persian fairy tale "The
Three Princes of Serendip" (www.wikipedia.org).


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "no" | Produce some screen output |  |
| method | "old" | Method to use |   "old" :: "Use old method"*OR*  "new" :: "Use new method"*OR* |
| num_regions | 0 | Number of no-excision regions |   0:10 :: ""*OR* |
| smoothing_iterations | 10 | Smoothing iterations |   0:* :: ""*OR* |
| smoothing_factor | 1.2 | Initial moothing factor |   (0:2) :: ""*OR* |
| smooth_regions | "no" | Smooth overwritten regions? |  |
| smoothing_order | 6 | Order of the derivatives used for CG smoothing |   2:6:2 :: ""*OR* |
| smoothing_eps | 1e-6 | CG smoothing stop criteria |   (0.0:* :: ""*OR* |
| use_user_regions | "no" | Use user defined regions for the smoothing regions |  |


### DistortedBHIVP

Cactus Code Thorn DistortedBHIVP
Authors    : Ryoji Takahashi 
CVS info   : $Header$
--------------------------------------------------------------------------

This thorn does calculates initial data for a black hole distorted
by a non-axisymmetric even parity perturbation 



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| amp | 0.1 | Brill wave amplitude |   *:* :: "No restriction"*OR* |
| eta0 | 0.0 | Brill wave center (in eta coords) |   *:* :: "No restriction"*OR* |
| c | 0.0 | Azimuthal dependence of Brill wave |   *:* :: "No restriction"*OR* |
| sigma | 1.0 | Brill wave width (in eta) |   *:* :: "No restriction"*OR* |
| etamax | 5.0 | Eta value for outer edge of grid |   *:* :: "No restriction"*OR* |
| n | 2 | sin^n theta in brill wave |   *:* :: "No restriction"*OR* |
| neta | 202 | Eta resolution for solve |   *:* :: "No restriction"*OR* |
| ntheta | 54 | Theta resolution for solve |   *:* :: "No restriction"*OR* |
| nphi | 5 | Eta resolution for solve |   *:* :: "No restriction"*OR* |
| interpolation_order | 1 | Order for interpolation |   1:3 :: "Choose between first, second, and third-order"*OR* |
| tolerance | 1.e-9 | Tolerance |  0:* :: "Tolerance"*OR* |


### Hydro_RNSID

Cactus Code Thorn RNSID
Author(s)    : Nik Stergioulas
               Tom Goodale
               Roberto De Pietri
               Frank Löffler
Maintainer(s): Roberto De Pietri
               Nik Stergioulas
Licence      : GPLv2+
--------------------------------------------------------------------------

Purpose of the thorn:

This thorn provides initial data for a rotating neutron star.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| rotation_type | "uniform" | Specify type of rotation law |  "uniform" :: "uniform rotation"*OR* "diff" :: "KEH differential rotation law"*OR* |
| axes_ratio | 1 | rnsid axes ratio |   0: :: "Any positive number"*OR* |
| rho_central | 1.24e-3 | Central Density for Star |   : :: ""*OR* |
| A_diff | 1.0 | constant A in differential rotation law |   0.0: :: "Any positive number"*OR* |
| eos_type | "poly" | Specify type of equation of state |  "poly" :: "Polytropic EOS"*OR* "tab"  :: "Tabulated EOS"*OR* |
| RNS_Gamma | 2 | If we're using a different EoS at run time, this is the RNS Gamma |   *:* :: "Will be ignored if negative"*OR* |
| RNS_K | 100 | If we're using a different EoS at run time, this is the RNS K |   *:* :: "Will be ignored if negative"*OR* |
| eos_file | "" | Equation of state table |  .* :: "EOS table file"*OR* |
| RNS_rho_min |  | A minimum rho below which evolution is turned off (atmosphere). |    0.0: :: "Zero or larger. A useful value could be 0.0001"*OR* |
| accuracy | 1.0e-7 | rnsid accuracy in convergence  |   0: :: "Any positive number"*OR* |
| zero_shift | "no" | Set shift to zero? |  "yes" :: "set shift to zero"*OR* "no" :: "don't set shift to zero"*OR* |
| save_2Dmodel | "no" | Save 2D model? |  "yes" :: "save 2D model"*OR* "no" :: "don't save 2D model"*OR* |
| recover_2Dmodel | "no" | Recover 2D model? |  "yes" :: "recover 2D model"*OR* "no" :: "don't recover 2D model"*OR* |
| model2D_file | "model2D.dat" | Name of 2D model file |  .* :: "Default 2D model file"*OR* |
| cf | 1.0 | Convergence factor |   0:  ::  "Any positive number"*OR* |
| RNS_lmax | 10 | max. term in Legendre poly. |  1: :: "Any positive, non zero number"*OR* |


### IDFileADM

CVS info   : $Header$

Cactus Code Thorn IDFileADM
Thorn Author(s)     : Erik Schnetter <schnetter@aei.mpg.de>
Thorn Maintainer(s) : Erik Schnetter <schnetter@aei.mpg.de>
--------------------------------------------------------------------------

Purpose of the thorn:

Make it possible to use the file reader to read initial data for the
ADM variables.  This checks whether the ADM variables are actually
supposed to be read, so that it is impossible to actually leave
something uninitialised.  However, read errors are not caught.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### IDAxiOddBrillBH

Cactus Code Thorn IDAxiOddBrillBH
Authors    : Ryoji Takahashi 
CVS info   : $Header$
--------------------------------------------------------------------------

This thorn does calculates initial data for a black hole distorted
by a axisymmetric odd parity perturbation 



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| amp | 0.1 | Brill wave amplitude for extrinsic curveture |   *:* :: "No restriction"*OR* |
| amp_me | 0.0 | Brill wave amplitude for metric |   *:* :: "No restriction"*OR* |
| xi | 0.0 | for recoil |   *:* :: "No restriction"*OR* |
| eta0 | 0.0 | Brill wave center (in eta coords) for extrisic curvature |   *:* :: "No restriction"*OR* |
| eta0_me | 0.0 | Brill wave center (in eta coords) for metric |   *:* :: "No restriction"*OR* |
| sigma | 1.0 | Brill wave width (in eta) for extrinsic curveture |   *:* :: "No restriction"*OR* |
| sigma_me | 1.0 | Brill wave width (in eta) for metric |   *:* :: "No restriction"*OR* |
| byJ | 0.0 | Bowen and York Momenta |   *:* :: "No restriction"*OR* |
| etamax | 5.0 | Eta value for outer edge of grid |   *:* :: "No restriction"*OR* |
| n | 3 | sin**n theta in brill wave for extrinsic curveture |   *:* :: "No restriction"*OR* |
| n_me | 3 | sin**n theta in brill wave for metric |   *:* :: "No restriction"*OR* |
| neta | 300 | Eta resolution for solve |   *:* :: "No restriction"*OR* |
| ntheta | 50 | Theta resolution for solve |   *:* :: "No restriction"*OR* |
| brandt_seidel | "no" | Brand-Seidel initial data |  |
| sergio | "no" | Sergio initial data |  |
| verbose | "yes" |  |  |
| interpolation_order | 1 | Order for interpolation |   1:3 :: "Choose between first, second, and third-order"*OR* |


### RotatingDBHIVP

Cactus Code Thorn RotatingDBHIVP
Authors    : Ryoji Takahashi 
CVS info   : $Header$
--------------------------------------------------------------------------

This thorn does calculates initial data for a black hole distorted
by a non-axisymmetric odd parity perturbation 



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| kerr_slice | "no" | initial slice for kerr |  |
| kerr_shift | "no" | kerr shift |  |
| amp | 0.1 | Brill wave amplitude |   *:* :: "No restriction"*OR* |
| eta0 | 0.0 | Brill wave center (in eta coords) |   *:* :: "No restriction"*OR* |
| sigma | 1.0 | Brill wave width (in eta) |   *:* :: "No restriction"*OR* |
| byJ | 1.0 | Bowen York Angular momenta |   *:* :: "No restriction"*OR* |
| mm | 0.0 | Azimuthal dependence factor |   *:* :: "No restriction"*OR* |
| etamax | 5.0 | Eta value for outer edge of grid |   *:* :: "No restriction"*OR* |
| neta | 202 | Eta resolution for solve |   *:* :: "No restriction"*OR* |
| ntheta | 54 | Theta resolution for solve |   *:* :: "No restriction"*OR* |
| nphi | 5 | Eta resolution for solve |   *:* :: "No restriction"*OR* |
| verbose | "yes" |  |  |
| interpolation_order | 1 | Order for interpolation |   1:3 :: "Choose between first, second, and third-order"*OR* |


### IDAnalyticBH

Cactus Code Thorn IDAnalyticBH
Author(s)    : Steve Brandt
               Carsten Gundlach
               Joan Masso
               Ed Seidel
               Paul Walker
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn calculates analytic initial data for the Einstein grid functions
(lapse, shift, metric, curv) for various black hole spacetimes:

	Schwarzschild black hole
 	2 Misner black holes placed on the z-axis
	n Misner black holes placed in a circle in the x-y plane
	Brill-Lindquist data




| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| mass | 2.0 | Mass of black hole | : :: "Not sure if it can be negative or not"*OR* |
| a_Kerr | 0.1 | Angular momentum parameter of black hole | -1:1 :: "Between +1 and -1"*OR* |
| mu | 1.2 | Misner mu value | 0: :: "Non-negative"*OR* |
| nmax | 30 | Number of terms to include for Misner series | 0: :: "Non-negative"*OR* |
| misner_nbh | 1 | Number of Misner black holes |  1:10 :: "Not more than 10"*OR* |
| bl_nbh | 1 | Number of Brill Lindquist black holes |  1:4 :: "Between one and four holes implemented"*OR* |
| bl_x0_1 | 0.0 | x-position of 1st BL hole | : :: "Anything"*OR* |
| bl_y0_1 | 0.0 | y-position of 1st BL hole | : :: "Anything"*OR* |
| bl_z0_1 | 0.0 | z-position of 1st BL hole | : :: "Anything"*OR* |
| bl_M_1 | 1.0 | Mass of 1st BL hole | : :: "Anything"*OR* |
| bl_x0_2 | 0.0 | x-position of 2nd BL hole | : :: "Anything"*OR* |
| bl_y0_2 | 0.0 | y-position of 2nd BL hole | : :: "Anything"*OR* |
| bl_z0_2 | 0.0 | z-position of 2nd BL hole | : :: "Anything"*OR* |
| bl_M_2 | 1.0 | Mass of 2nd BL hole | : :: "Anything"*OR* |
| bl_x0_3 | 0.0 | x-position of 3nd BL hole | : :: "Anything"*OR* |
| bl_y0_3 | 0.0 | y-position of 3nd BL hole | : :: "Anything"*OR* |
| bl_z0_3 | 0.0 | z-position of 3nd BL hole | : :: "Anything"*OR* |
| bl_M_3 | 1.0 | Mass of 3rd BL hole | : :: "Anything"*OR* |
| bl_x0_4 | 0.0 | x-position of 4th BL hole | : :: "Anything"*OR* |
| bl_y0_4 | 0.0 | y-position of 4th BL hole | : :: "Anything"*OR* |
| bl_z0_4 | 0.0 | z-position of 4th BL hole | : :: "Anything"*OR* |
| bl_M_4 | 1.0 | Mass of 4th BL hole | : :: "Anything"*OR* |
| epsilon | 1.e-16 | Fudge factor |   0.0:* :: ""*OR* |


### Meudon_Mag_NS

Cactus Code Thorn ID_Mag_NS
Author(s)    : Erik Schnetter <schnetter@cct.lsu.edu>
Maintainer(s): Erik Schnetter <schnetter@cct.lsu.edu>
Licence      : GPL
--------------------------------------------------------------------------

1. Purpose

Import LORENE Mag_NS magnetised neutron star initial data.

This thorn is one of three closely related thorns ID_Bin_BH,
ID_Bin_NS, and ID_Mag_NS which all import LORENE initial data.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| filename | "" | Input file name |   "" :: ""*OR* |


### Exact

Cactus Code Thorn Exact
Authors    : Carsten Gundlach initially, plus many other authors.
Maintainer : Jonathan Thornburg <jthorn@aei.mpg.de>
CVS info   : $Header$
--------------------------------------------------------------------------

Purpose of the thorn
====================

The initial purpose of this thorn was the comparison of numerical
spacetimes against exact solutions.  One particular solution,
the boost-rotation-symmetric spacetimes, was the first application.

Later, the thorn developed as a reservoir for a variety of exact spacetimes,
some of them in different coordinate systems, and even some non-Einstein
spcetimes.  All of these exact spacetimes have been found useful for
testing different aspect of the code.  Since mamy different people have
contributed to this thorn by adding new exact solutions, you should expect
many different styles of coding here.

For more details see the comments in the param.ccl file, and the
documentation in the directory doc.  For instructions on how to add
a new model, see doc/how_to_add_a_new_model.

Note that the June 2002 revisions to this thorn included renaming
most of the parameters.  The perl script  par/convert-par.pl  will
convert old parameter files to use the new names, renaming the old
parameter files to ".par.bak" as backups.


Compilation
===========

On some systems (notably the AEI Xeons using the Intel compilers) this
thorn only compiles if you use the Cactus (perl) preprocessor, i.e. you
need to configure with
   gmake foo-config CPP='$${PERL} $${CCTK_HOME}/lib/sbin/cpp.pl'
(along with any other configuration options), or put the line
      CPP=$${PERL} $${CCTK_HOME}/lib/sbin/cpp.pl
in your ~/.cactus/config or other Cactus options file.

[Note by Erik Schnetter, 2005-05-15: The adivce above is outdated and
incorrect.  This issue is also treated in the Cactus FAQ.  Read it for
more information.]

This thorn is mostly written in Fortran 77, with a few files in C
and Fortran 90.  At present Fortran 90 is only used for the "arbitrary slice"
option (see the thorn guide for details).  If this option isn't needed,
then by changing a single line in  src/include/Exact.inc  (see the comments
there for details) you can #ifdef-out the Fortran 90 code, allowing the
rest of this thorn to be compiled on a system with no Fortran 90 compiler
available.


Copyright
=========

As noted above, this code has been hacked on by many different people,
so its' copyright and licensing status is somewhat unclear.

Here is a (incomplete) list of authors and their wishes:

----------------------------------------

Many of the individual metrics (you can find them by grepping in the
   header comments in the  src/metrics/  directory) are
   copyright (C) 2000-2003 by Dumitru "Mitica" Vulcanov <vulcan@aei.mpg.de>

This code is under the same "GPL with exceptions for other thorns"
license as the Cactus flesh; see the file CACTUS.COPYRIGHT in this
directory for details.

----------------------------------------

The new code added in the June 2002 revisions to this thorn
   is copyright (C) 2001-2002 by Jonathan Thornburg <jthorn@aei.mpg.de>
Minkowski_conf_wave and the conformal factor code added in January 2003
   is copyright (C) 2003 by Frank Loeffler <frank.loeffler@aei.mpg.de>
Option to boost any non-stress-energy-tensor model
   is copyright (C) 2003 by Jonathan Thornburg <jthorn@aei.mpg.de>

This code is free software; you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation; either version 2 of the License, or
(at your option) any later version.

This code is distributed in the hope that it will be useful, but
WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this thorn (see the file COPYING in this directory);
if not, write to the Free Software Foundation, Inc., 59 Temple
Place, Suite 330, Boston, MA  02111-1307  USA

------------------------------------------


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| exblend_Ks | "yes" | Blend the K variables with the exact solution? |  |
| exblend_gs | "yes" | Blend the g variables with the exact solution? |  |
| exblend_gauge | "yes" | Blend the lapse and shift with the exact solution? |  |
| exblend_rout | -1.0 | Outer boundary of blending region | *:* :: "Positive means radial value, negative means use outer bound of grid"*OR* |
| exblend_width | -3.0 | Width of blending zone | *:* :: "Positive means width in radius, negative means width = exbeldn_width*dx"*OR* |
| slice_Gauss_ampl | 0.0 | Amplitude of Gauss slice in exact | 0.0:* :: "Positive please"*OR* |
| slice_Gauss_width | 1.0 | Width of Gauss slice in exact | 0.0:* :: "Positive please"*OR* |
| overwrite_boundary | "no" | Overwrite g and K on the boundary |   "no"    :: "Do nothing"*OR*  "exact" :: "Use boundary data from an exact solution on a trivial slice"*OR* |
| exact_order | 2 | finite differencing order | 2 :: "2"*OR*4 :: "4"*OR* |
| exact_eps | 1.0e-6 | finite differencing stencil size (in terms of the grid spacing) | (0.0:* :: "Positive please"*OR* |
| boost_vx | 0.0 | x component of boost velocity | *:* :: "any real number"*OR* |
| boost_vy | 0.0 | y component of boost velocity | *:* :: "any real number"*OR* |
| boost_vz | 0.0 | z component of boost velocity | *:* :: "any real number"*OR* |
| shift_add_x | 0.0 | x component of added shift | *:* :: "any real number"*OR* |
| shift_add_y | 0.0 | y component of added shift | *:* :: "any real number"*OR* |
| shift_add_z | 0.0 | z component of added shift | *:* :: "any real number"*OR* |
| rotation_euler_phi | 0.0 | Euler angle phi (first rotation, about z axis) (irrelevant for axisymmetric models) | *:* :: "any real number"*OR* |
| rotation_euler_theta | 0.0 | Euler angle theta (second rotation, about x axis) | *:* :: "any real number"*OR* |
| rotation_euler_psi | 0.0 | Euler angle psi (third rotation, about z axis) | *:* :: "any real number"*OR* |
| Schwarzschild_EF__mass | 1.0 | Schwarzschild/EF: BH mass | #	*:* :: "any real number"*OR*#	 |
| exact_model | "Minkowski" | The exact solution/coordinates used in thorn exact | #*OR*# Minkowski spacetime*OR*#*OR*"Minkowski"                    :: "Minkowski spacetime"*OR*"Minkowski/shift"              :: "Minkowski spacetime with time-dependent shift vector"*OR*"Minkowski/funny"              :: "Minkowski spacetime in non-trivial spatial coordinates"*OR*"Minkowski/gauge wave"         :: "Minkowski spacetime in gauge-wave coordinates"*OR*"Minkowski/shifted gauge wave" :: "Minkowski spacetime in shifted gauge-wave coordinates"*OR*"Minkowski/conf wave"          :: "Minkowski spacetime with 'waves' in conformal factor"*OR*#*OR*# black hole spacetimes*OR*#*OR*"Schwarzschild/EF"           :: "Schwarzschild spacetime in Eddington-Finkelstein coordinates"*OR*"Schwarzschild/PG"           :: "Schwarzschild spacetime in Painleve-Gullstrand coordinates"*OR*"Schwarzschild/BL"           :: "Schwarzschild spacetime in Brill-Lindquist coordinates"*OR*"Schwarzschild/Novikov"      :: "Schwarzschild spacetime in Novikov coordinates"*OR*"Schwarzschild-Lemaitre"     :: "Schwarzschild metric in Schwarzschild coordinates, with cosmological constant"*OR*"Kerr/Boyer-Lindquist"       :: "Kerr spacetime in Boyer-Lindquist coordinates"*OR*"Kerr/Kerr-Schild"           :: "Kerr spacetime in Kerr-Schild coordinates"*OR*"Kerr/Kerr-Schild/spherical" :: "Kerr spacetime in distorted Kerr-Schild coordinates such that the horizon is a coordinate sphere"*OR*"multi-BH"                   :: "Majumdar-Papapetrou or Kastor-Traschen maximally charged multi BH solutions"*OR*"Alvi"                       :: "Alvi post-Newtonian 2BH spacetime (not fully implemented yet)"*OR*"Thorne-fakebinary"          :: "Thorne's fake-binary spacetime (non-Einstein)"*OR*#*OR*# cosmological spacetimes*OR*#*OR*"Lemaitre"              :: "Lemaitre-type spacetime"*OR*##"Robertson-Walker"      :: "Robertson-Walker spacetime"*OR*"de Sitter"             :: "de Sitter spacetime (R-W cosmology, near t=0, p=0)"*OR*"de Sitter+Lambda"      :: "de Sitter spacetime with cosmological constant"*OR*"anti-de Sitter+Lambda" :: "anti-de Sitter spacetime with cosmological constant"*OR*"Bianchi I"             :: "approximate Bianchi type I spacetime"*OR*"Goedel"                :: "Goedel spacetime"*OR*"Bertotti"              :: "Bertotti spacetime"*OR*"Kasner-like"           :: "Kasner-like spacetime"*OR*"Kasner-axisymmetric"   :: "axisymmetric Kasner spacetime"*OR*"Kasner-generalized"    :: "generalized Kasner spacetime"*OR*"Gowdy-wave"	        :: "Gowdy spacetime with polarized wave on a torus"*OR*"Milne"                 :: "Milne spacetime for pre-big-bang cosmology"*OR*#*OR*# miscelaneous spacetimes*OR*#*OR*"boost-rotation symmetric" :: "boost-rotation symmetric spacetime"*OR*"bowl"                     :: "bowl (bag-of-gold) spacetime (non-Einstein)"*OR*"constant density star"    :: "constant density (Schwarzschild) star"*OR* |
| Minkowski_shift__amplitude | 0.5 | Minkowski/shift: amplitude of Gaussian | (-1:1) :: "any real number < 1 in absolute value"*OR* |
| Minkowski_shift__sigma | 1.0 | Minkowski/shift: width of Gaussian | (0.0:* :: "any real number > 0"*OR* |
| Minkowski_funny__amplitude | 0.5 | Minkowski/funny: amplitude of Gaussian | 0.0:1.0) :: "any real number in the range [0,1)"*OR* |
| Minkowski_funny__sigma | 1.0 | Minkowski/funny: width of Gaussian | (0.0: :: "any real number > 0"*OR* |
| Schwarzschild_EF__mass | 1.0 | Schwarzschild/EF: BH mass | *:* :: "any real number"*OR* |
| Schwarzschild_EF__epsilon | 1.e-16 | Schwarzschild/EF: numerical fudge | 0.0:* :: "any real number >= 0.0"*OR* |
| Schwarzschild_PG__mass | 1.0 | Schwarzschild/PG: BH mass | (0.0:* :: "any real number > 0.0"*OR* |
| Schwarzschild_PG__epsilon | 1.e-16 | Schwarzschild/PG: numerical fudge | 0.0:* ::  "any real number >= 0.0"*OR* |
| Schwarzschild_BL__mass | 1.0 | Schwarzschild/BL: BH mass | (0.0:* :: "any real number > 0.0"*OR* |
| Schwarzschild_BL__epsilon | 1.e-16 | Schwarzschild/BL: numerical fudge | 0.0:* :: "any real number >= 0.0"*OR* |
| Schwarzschild_Novikov__mass | 1.0 | Schwarzschild/Novikov: BH mass | (0.0:* :: "any real number > 0.0"*OR* |
| Schwarzschild_Novikov__epsilon | 1.e-16 | Schwarzschild/Novikov: numerical fudge | 0.0:* :: "any real number >= 0.0"*OR* |
| Schwarzschild_Lemaitre__Lambda | 1.0 | Schwarzschild-Lemaitre: cosmological constant | *:* :: "any real number"*OR* |
| Schwarzschild_Lemaitre__mass | 1.0 | Schwarzschild-Lemaitre: BH mass | (0.0:* :: "any real number > 0"*OR* |
| Kerr_BoyerLindquist__mass | 1.0 | Kerr/Boyer-Lindquist: BH mass | (0.0:* :: "any real number > 0"*OR* |
| Kerr_KerrSchild__boost_v | 0.0 | Kerr/Kerr-Schild: boost velocity of black hole in z direction | (-1:1) :: "any real number with absolute value < 1"*OR* |
| Kerr_KerrSchild__epsilon | 1.e-16 | Kerr/Kerr-Schild: numerical fudge | 0.0:* :: "any real number >= 0.0"*OR* |
| Kerr_KerrSchild__power | 4 | Kerr/Kerr-Schild: power (exponent) of numerical fudge | 1:* :: ""*OR* |
| Kerr_KerrSchild__parabolic | "no" | Kerr/Kerr-Schild: use a parabolic singularity-avoiding term |  |
| Kerr_KerrSchild__mass | 1.0 | Kerr/Kerr-Schild: BH mass | (0.0:* :: "any real number > 0"*OR* |
| multi_BH__nBH | 0 | multi-BH: number of black holes 0-4 | 0:4 :: "any integer in the range [0,4]"*OR* |
| multi_BH__Hubble | 0.0 | multi-BH: Hubble constant = +/- sqrt{Lambda/3} | *:* :: "any real number"*OR* |
| multi_BH__mass1 | 0.0 | multi-BH: mass of black hole number 1 | 0.0: :: "any real number >= 0"*OR* |
| multi_BH__x1 | 0.0 | multi-BH: x coord of black hole number 1 | *:* :: "any real number"*OR* |
| multi_BH__y1 | 0.0 | multi-BH: y coord of black hole number 1 | *:* :: "any real number"*OR* |
| multi_BH__z1 | 0.0 | multi-BH: z coord of black hole number 1 | *:* :: "any real number"*OR* |
| multi_BH__mass2 | 0.0 | multi-BH: mass of black hole number 2 | 0.0: :: "any real number >= 0"*OR* |
| multi_BH__x2 | 0.0 | multi-BH: x coord of black hole number 2 | *:* :: "any real number"*OR* |
| multi_BH__y2 | 0.0 | multi-BH: y coord of black hole number 2 | *:* :: "any real number"*OR* |
| multi_BH__z2 | 0.0 | multi-BH: z coord of black hole number 2 | *:* :: "any real number"*OR* |
| multi_BH__mass3 | 0.0 | multi-BH: mass of black hole number 3 | 0.0: :: "any real number >= 0"*OR* |
| multi_BH__x3 | 0.0 | multi-BH: x coord of black hole number 3 | *:* :: "any real number"*OR* |
| multi_BH__y3 | 0.0 | multi-BH: y coord of black hole number 3 | *:* :: "any real number"*OR* |
| multi_BH__z3 | 0.0 | multi-BH: z coord of black hole number 3 | *:* :: "any real number"*OR* |
| multi_BH__mass4 | 0.0 | multi-BH: mass of black hole number 4 | 0.0:* :: "any real number >= 0"*OR* |
| multi_BH__x4 | 0.0 | multi-BH: x coord of black hole number 4 | *:* :: "any real number"*OR* |
| multi_BH__y4 | 0.0 | multi-BH: y coord of black hole number 4 | *:* :: "any real number"*OR* |
| multi_BH__z4 | 0.0 | multi-BH: z coord of black hole number 4 | *:* :: "any real number"*OR* |
| Alvi__mass1 | 1.0 | Alvi: mass of BH number 1 | 0.0:* :: "any real number >= 0"*OR* |
| Alvi__mass2 | 1.0 | Alvi: mass of BH number 2 | 0.0:* :: "any real number >= 0"*OR* |
| Alvi__separation | 20.0 | Alvi: spatial separation of the black holes | 0.0:* :: "must be greater than m1+m2 + 2 sqrt(m1 m2)"*OR* |
| Thorne_fakebinary__atype | "constant" | Thorne-fakebinary: binary type |   "constant"   :: ""*OR*  "quadrupole" :: ""*OR* |
| Thorne_fakebinary__retarded | "no" | Thorne-fakebinary: use retarded time? |  |
| Thorne_fakebinary__epsilon | 1.e-16 | Thorne-fakebinary: numerical fudge | 0.0:* :: "any real number >= 0.0"*OR* |
| Thorne_fakebinary__separation | 5.0 | Thorne-fakebinary: initial separation | (0.0:* :: "any real number > 0"*OR* |
| Thorne_fakebinary__Omega0 | 1.0 | Thorne-fakebinary: initial angular frequency | (0.0:* :: "any real number > 0"*OR* |
| Thorne_fakebinary__mass | 1.0 | Thorne-fakebinary: mass | (0.0:* :: "any real number > 0"*OR* |
| Lemaitre__kappa | -0.5 | Lemaitre: multiplicative factor in equation of state | *:* :: "any real number"*OR* |
| Lemaitre__Lambda | 1.0 | Lemaitre: cosmological constant | *:* :: "any real number"*OR* |
| Lemaitre__epsilon0 | 1.0 | Lemaitre: density of the universe at time t=0 | 0.0:* :: "any real number >= 0"*OR* |
| Lemaitre__R0 | 1.0 | Lemaitre: scale factor (radius) of the universe at time t=0 | (0.0:* :: "any positive real number"*OR* |
| Robertson_Walker__rho | 0.1 | Robertson-Walker: density parameter | ##0.0:* :: "any real number >= 0"*OR*## |
| Robertson_Walker__k | 0 | Robertson-Walker: geometry type parameter | ##-1:1:1 :: "one of the values -1, 0, 1"*OR*## |
| de_Sitter__scale | 0.1 | de Sitter: multiplicative scale factor | (0.0:* :: "any positive real number"*OR* |
| de_Sitter_Lambda__scale | 0.1 | de Sitter+Lambda: multiplicative scale factor | (0.0:* :: "any positive real number"*OR* |
| Bianchi_I__scale | 0.1 | Bianchi I: multiplicative scale factor | (0.0:* :: "any positive real number"*OR* |
| Goedel__scale | 0.1 | Goedel: multiplicative scale factor | (0.0:* :: "any positive real number"*OR* |
| Bertotti__Lambda | -1.0 | Bertotti: cosmological constant | *:* :: "any real number"*OR* |
| Kasner_like__q | 0.66666666666666666666 | Kasner-like: q parameter | *:* :: "any real number"*OR* |
| Kasner_generalized__p1 | 0.1 | Kasner-generalized: x exponent parameter | -1.0:1.0 :: "any real number in the range [-1,1]"*OR* |
| Kasner_generalized__p2 | 0.1 | Kasner-generalized: y exponent parameter | -1.0:1.0 :: "any real number in the range [-1,1]"*OR* |
| Gowdy_wave__amplitude | 0.0 | Gowdy-wave: amplitude parameter | *:* :: "any real number"*OR* |
| boost_rotation_symmetric__scale | 1.0 | boost-rotation symmetric: length scale | 0.0:* :: "Positive please"*OR* |
| bowl__shape | "Gaussian" | bowl: what shape of bowl should we use? |   "Gaussian" :: "Gaussian bowl"*OR*  "Fermi"    :: "Fermi-function bowl"*OR* |
| bowl__evolve | "false" | bowl: are we evolving the metric? |  |
| bowl__strength | 0.5 | bowl: deformation strength | 0.0:* :: "any real number >= 0"*OR* |
| bowl__center | 2.5 | bowl: deformation center | (0.0:* :: "any positive real number"*OR* |
| bowl__sigma | 1.0 | bowl: width of deformation | (0.0:* :: "any positive real number"*OR* |
| bowl__x_scale | 1.0 | bowl: scale for x coordinate | (0.0:* :: "any positive real number"*OR* |
| bowl__y_scale | 1.0 | bowl: scale for y coordinate | (0.0:* :: "any positive real number"*OR* |
| bowl__z_scale | 1.0 | bowl: scale for z coordinate | (0.0:* :: "any positive real number"*OR* |
| bowl__t0 | 1.0 | bowl: center of Fermi step in time | *:* :: "any real number"*OR* |
| bowl__sigma_t | 1.0 | bowl: width of Fermi step in time | (0.0:* :: "any positive real number"*OR* |
| constant_density_star__mass | 1.0 | constant density star: mass of star | (0.0:* :: "any positive real number"*OR* |
| constant_density_star__radius | 1.0 | constant density star: radius of star | (0.0:* :: "any positive real number"*OR* |


### Hydro_InitExcision

Cactus Code Thorn Hydro_InitExcision
Author(s)    : Andrea Nerozzi
               Frank Löffler
Maintainer(s): Frank Löffler
Licence      : GPLv2+
--------------------------------------------------------------------------

1. Purpose

Setup initial excision masks specified by parameters for testing.

2. History

This thorn was developed within the Whisky code under the name
Whisky_InitExcision, was made public by the group of Whisky
developers and was then included in the EinsteinToolkit using its
present name, because it is independend of any hydro evolution code,
as long as that uses HydroBase.



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| hydro_initexcision_old_mask | 0 | Also set the old 'emask'? |   0:1 :: "0 (default) for no, 1 for yes"*OR* |
| hydro_initexcision_flip | 0 | Exchange excised and normal region |   0:1 :: "0 (default) for no, 1 for yes"*OR* |
| hydro_initexcision_fraction | 0.25 | Fraction of excised region |   0:1      :: "Represents the fraction of excised region (0..1)"*OR* |
| hydro_initexcision_coordinate_length | 0 | Length of excised region |   0:  :: "Length of excised region, has different meanings for the types"*OR* |
| hydro_initexcision_position_x | 0.0 | x-coordinate |   : :: "anything real is ok"*OR* |
| hydro_initexcision_position_y | 0.0 | y-coordinate |   : :: "anything real is ok"*OR* |
| hydro_initexcision_position_z | 0.0 | z-coordinate |   : :: "anything real is ok"*OR* |
| hydro_initexcision_min_points | 0 | minimal number of points of excision |  0: :: "minimal number of points of excision, currently only partly implemented"*OR* |


### Meudon_Bin_BH

Cactus Code Thorn ID_Bin_BH
Author(s)    : Erik Schnetter <schnetter@cct.lsu.edu>
Maintainer(s): Erik Schnetter <schnetter@cct.lsu.edu>
Licence      : GPL
--------------------------------------------------------------------------

1. Purpose

Import LORENE Bin_BH binary black hole initial data.

This thorn is one of three closely related thorns ID_Bin_BH,
ID_Bin_NS, and ID_Mag_NS which all import LORENE initial data.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| filename | "" | Input file name |   "" :: ""*OR* |


### IDBrillData

Cactus Code Thorn IDBrillData
Author(s)    : Carsten Gundlach
               Miguel Alcubierre
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Initializes Brill wave data.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| solver | "sor" | Which elliptic solver to use |   "sor"    ::  "Use SOR solver"*OR*  "petsc"  ::  "Use PETSc solver"*OR*  "bam"    ::  "Use bam solver"*OR* |
| bound | "robin" | Which boundary condition to use |   "const" :: "constant boundary: set const_v0"*OR*  "robin" :: "Robin boundary: set robin_falloff, robin_inf"*OR* |
| robin_falloff | 1 | Fall-off of Robin BC |   0: :: "any positive integer value"*OR* |
| const_v0 | 1.0 | Value of constant BC |   : :: "anything goes"*OR* |
| robin_inf | 1.0 | Value at infinity of Robin BC |   : :: "anything goes"*OR* |
| thresh | 0.00001 | How far (absolute norm) to go |   0.0: :: "Positive number please"*OR* |
| exp_a | 0.0 | Exp Brill wave: Amplitude |   : :: "Anything"*OR* |
| exp_b | 2 | Exp Brill wave: used in exponent in rho: rho^(2+b) |   : :: "Anything"*OR* |
| exp_rho0 | 0.0 | Exp Brill wave: radius of torus in rho |   0:* :: "Positive"*OR* |
| exp_sigmaz | 1.0 | Exp Brill wave: sigma in z |   (0:* :: "Positive"*OR* |
| eppley_a | 0.0 | Eppley Brill wave: Amplitude |   : :: "Anything"*OR* |
| eppley_b | 2 | Eppley Brill wave: used in exponent in rho: rho^b |   : :: "Anything"*OR* |
| eppley_c | 0.0 | Eppley Brill wave: (r^2 - r0^2)^(c/2) |   0:* :: "Positive" *OR* |
| eppley_sigmarho | 1.0 | Eppley Brill wave: sigma in rho |   : :: "Anything"*OR* |
| eppley_sigmar | 1.0 | Eppley Brill wave: sigma in r |   : :: "Anything"*OR* |
| gundlach_a | 0.0 | Gundlach Brill wave: Amplitude |   : :: "Anything"*OR* |
| gundlach_b | 2 | Gundlach Brill wave: used in exponent in rho: rho^b |   : :: "Anything"*OR* |
| gundlach_sigmarho | 1.0 | Gundlach Brill wave: sigma in rho |   : :: "Anything"*OR* |
| gundlach_r0 | 0.0 | Gundlach Brill wave: radius of torus in r |   0:* :: "Positive" *OR* |
| gundlach_c | 1.0 | Gundlach Brill wave: (r^2 - r0^2)^(c/2) |   : :: "Anything"*OR* |
| brill3d_d | 0.0 | 3D Brill wave:  d rho^m cos^2(n (phi + phi0)) |   : :: "Anything"*OR* |
| brill3d_e | 1.0 | 3D Brill wave:  d rho^m cos^2(n (phi + phi0)) |   : :: "Anything"*OR* |
| brill3d_m | 2.0 | 3D Brill wave:  d rho^m cos^2(n (phi + phi0)) |   : :: "Anything"*OR* |
| brill3d_n | 2.0 | 3D Brill wave:  d rho^m cos^2(n (phi + phi0)) |   : :: "Anything"*OR* |
| brill3d_phi0 | 0.0 | 3D Brill wave:  d rho^m cos^2(n (phi + phi0)) |   : :: "Anything"*OR* |
| rhofudge | 0.00001 | delta rho for axis fudge |   0: :: "Positive please"*OR* |
| sor_maxit | 10000 | Maximum number of iterations | 0:* :: "Positive"*OR* |


## CTThorns

### CT_Analytic

| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | 0 | verbose |   *:* :: ""*OR* |
| other_timelevels | 1 | Number of active timelevels for non-evolved grid functions |   0:3 :: ""*OR* |
| kx | 0 | Wavelength parameter along x |   *:* :: ""*OR* |
| ky | 0 | Wavelength parameter along y |   *:* :: ""*OR* |
| kz | 0 | Wavelength parameter along z |   *:* :: ""*OR* |
| ampG | 0 | Coefficient of the gaussian term in the exact solution |   *:* :: ""*OR* |
| ampS | 0 | Coefficient of the sine term in the exact solution |   *:* :: ""*OR* |
| ampC | 0 | Constant coefficient in the exact solution |   *:* :: ""*OR* |
| ampI | 0 | Multiplication factor between initial guess and exact solution |   *:* :: ""*OR* |
| ampC1 | 0 | Initial value for testc1 |   *:* :: ""*OR* |
| ampSg | 0 | Coefficient of the 1/r term in the exact solution |   *:* :: ""*OR* |
| ampV | 0 | Coefficient of the vector part in the exact solution |   *:* :: ""*OR* |
| ampVG | 0 | Coefficient of the vector part in the exact solution (gaussian term) |   *:* :: ""*OR* |
| sigma | 1 | Width of transition function in extrinsic curvature |   *:* :: ""*OR* |
| l | 0 | Location of transition function in extrinsic curvature |   *:* :: ""*OR* |
| phasex | 0 | Phase in the initial data for psi along x |   *:* :: ""*OR* |
| phasey | 0 | Phase in the initial data for psi along y |   *:* :: ""*OR* |
| phasez | 0 | Phase in the initial data for psi along z |   *:* :: ""*OR* |
| Kc | 0 | Coefficient of extrinsic curvature |   *:* :: ""*OR* |
| Ke | 0 | Coefficient of extrinsic curvature in exact solution |   *:* :: ""*OR* |
| massa | 0 | mass of first black hole |   *:* :: ""*OR* |
| massb | 0 | mass of second black hole |   *:* :: ""*OR* |
| xa | 0 | x-coordinate of first black hole for BY initial data |   *:* :: ""*OR* |
| ya | 0 | y-coordinate of first black hole for BY initial data |   *:* :: ""*OR* |
| za | 0 | z-coordinate of first black hole for BY initial data |   *:* :: ""*OR* |
| xb | 0 | x-coordinate of second black hole for BY initial data |   *:* :: ""*OR* |
| yb | 0 | y-coordinate of second black hole for BY initial data |   *:* :: ""*OR* |
| zb | 0 | z-coordinate of second black hole for BY initial data |   *:* :: ""*OR* |
| Pax | 0 | x-component of linear momentum of first black hole for BY initial data |   *:* :: ""*OR* |
| Pay | 0 | y-component of linear momentum of first black hole for BY initial data |   *:* :: ""*OR* |
| Paz | 0 | z-component of linear momentum of first black hole for BY initial data |   *:* :: ""*OR* |
| Pbx | 0 | x-component of linear momentum of second black hole for BY initial data |   *:* :: ""*OR* |
| Pby | 0 | y-component of linear momentum of second black hole for BY initial data |   *:* :: ""*OR* |
| Pbz | 0 | z-component of linear momentum of second black hole for BY initial data |   *:* :: ""*OR* |
| eps | 1.e-6 | Smoothing factor |   *:* :: ""*OR* |
| edgeL | 10 | Coordinate length of cell edge |   *:* :: ""*OR* |
| rBall | 1 | Coordinate radius of ball of density for Poisson's equation |   *:* :: ""*OR* |
| vecA | 1 | Coordinate center of gaussian representing the X^i vector in the CTT decomposition of the constraints |   *:* :: ""*OR* |
| imaxF | 1 | Max number of Fourier modes to include in x direction |   *:* :: ""*OR* |
| jmaxF | 1 | Max number of Fourier modes to include in y direction |   *:* :: ""*OR* |
| kmaxF | 1 | Max number of Fourier modes to include in z direction |   *:* :: ""*OR* |
| tile_size | -1 | Loop tile size |   *:* :: ""*OR* |
| free_data | "exact" | How to set the free data for the extrinsic curvature? |   "exact" :: ""*OR*  "Expanding BH lattice" :: ""*OR*  "Bowen-York" :: ""*OR*  "Poisson" :: ""*OR*  "Lump" :: ""*OR* |
| CT_Analytic_MaxNumEvolvedVars | 0 | Number of evolved variables used by this thorn |   0:0 :: "Number of evolved variables used by this thorn"*OR* |
| CT_Analytic_MaxNumArrayEvolvedVars | 0 | Number of Array evolved variables used by this thorn |   0:0 :: "Number of Array evolved variables used by this thorn"*OR* |
| timelevels | 3 | Number of active timelevels |   0:3 :: ""*OR* |
| rhs_timelevels | 1 | Number of active RHS timelevels |   0:3 :: ""*OR* |
| CT_Analytic_Poisson_Calc_calc_every | 1 | CT_Analytic_Poisson_Calc_calc_every |   *:* :: ""*OR* |
| CT_Analytic_Exact_Calc_calc_every | 1 | CT_Analytic_Exact_Calc_calc_every |   *:* :: ""*OR* |
| CT_Analytic_ExpandingLattice_Calc_calc_every | 1 | CT_Analytic_ExpandingLattice_Calc_calc_every |   *:* :: ""*OR* |
| CT_Analytic_BY_Calc_calc_every | 1 | CT_Analytic_BY_Calc_calc_every |   *:* :: ""*OR* |
| CT_Analytic_Lump_Calc_calc_every | 1 | CT_Analytic_Lump_Calc_calc_every |   *:* :: ""*OR* |
| CT_Analytic_ExactBoundary_calc_every | 1 | CT_Analytic_ExactBoundary_calc_every |   *:* :: ""*OR* |
| CT_Analytic_LumpBoundary_calc_every | 1 | CT_Analytic_LumpBoundary_calc_every |   *:* :: ""*OR* |
| CT_Analytic_Poisson_Calc_calc_offset | 0 | CT_Analytic_Poisson_Calc_calc_offset |   *:* :: ""*OR* |
| CT_Analytic_Exact_Calc_calc_offset | 0 | CT_Analytic_Exact_Calc_calc_offset |   *:* :: ""*OR* |
| CT_Analytic_ExpandingLattice_Calc_calc_offset | 0 | CT_Analytic_ExpandingLattice_Calc_calc_offset |   *:* :: ""*OR* |
| CT_Analytic_BY_Calc_calc_offset | 0 | CT_Analytic_BY_Calc_calc_offset |   *:* :: ""*OR* |
| CT_Analytic_Lump_Calc_calc_offset | 0 | CT_Analytic_Lump_Calc_calc_offset |   *:* :: ""*OR* |
| CT_Analytic_ExactBoundary_calc_offset | 0 | CT_Analytic_ExactBoundary_calc_offset |   *:* :: ""*OR* |
| CT_Analytic_LumpBoundary_calc_offset | 0 | CT_Analytic_LumpBoundary_calc_offset |   *:* :: ""*OR* |


### CT_MultiLevel

Cactus Code Thorn CT_MultiLevel
Author(s)    : Eloisa Bentivegna <eloisa.bentivegna@unict.it>
Maintainer(s): Eloisa Bentivegna <eloisa.bentivegna@unict.it>
Copyright    : Eloisa Bentivegna, 2013-2016
Licence      : GPLv3+
--------------------------------------------------------------------------

1. Purpose

This thorn implements a multigrid solver for systems of elliptic PDEs.
It uses Carpet to manage the different grids and the transfer of data
between them. The solver is generic -- the system of equations to solve
is defined at runtime by setting its coefficients via external grid
functions. 

Some examples are discussed in gr-qc/1305.5576.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| mode | "generic" | Which equation should we solve? |   "generic" :: "Generic elliptic operator, to be defined via the coefficients"*OR*  "constraints" :: "The GR constraints"*OR* |
| model | "None" | Model used to populate the auxiliary functions |   "Bowen-York"                       :: "Bowen-York extrinsic curvature for multiple punctures"*OR*  "Expanding BH lattice"             :: "An expanding black-hole lattice"*OR*  "Lump"                             :: "Generic compact source in Tmunu"*OR*  "Inhomogeneous Helmholtz"          :: "Inhomogeneous Helmholtz equation"*OR*  "None"                             :: "No auxiliaries needed"*OR* |
| cycle_type | "V | How should be cycle over the refinement levels? |   "V cycle"   :: "A V cycle"*OR*  "FMG cycle" :: "A FMG cycle"*OR* |
| verbose | "no" | Output debugging information? |   "no" :: "no"*OR*  "yes" :: "yes"*OR* |
| veryverbose | "no" | Output more debugging information? |   "no" :: "no"*OR*  "yes" :: "yes"*OR* |
| output_norms | "no" | Output the norms of psi and residual, and those of their errors? |   "no" :: "no"*OR*  "yes" :: "yes"*OR* |
| compare_to_exact | "no" | Output a file with the difference between the solution at each iteration and the exact solution, if known |   "no" :: "no"*OR*  "yes" :: "yes"*OR* |
| output_walk | "no" | Output a file with the parameter-space walk followed by the algorithm? |   "no" :: "no"*OR*  "yes" :: "yes"*OR* |
| fill_ADM | "no" | Should the equation solution be used to fill the ADM variables? |   "no" :: "no"*OR*  "yes" :: "yes"*OR* |
| boundary_conditions | "none" | Which boundary conditions to apply to psi |   "Robin"        :: "Robin"*OR*  "TwoPunctures" :: "Dirichlet BCs from TwoPunctures"*OR*  "none"         :: "This thorn will apply no boundary conditions"*OR* |
| exact_offset | 0.0 | Offset between exact solution and grid function pointed by exact_solution_gfname |     *:*  :: "Any real number"*OR* |
| fd_order | 2 | Order of FD |   2:4:2   :: "Order of differencing"*OR* |
| number_of_equations | 1 | How many equations are to be solved concurrently? |   1:10    :: "A positive integer smaller than or equal to 10"*OR* |
| number_of_auxiliaries | 0 | How many auxiliary functions do we need? |   0:      :: "A non-negative integer"*OR* |
| nrelsteps_up | 2 | How many times should we relax each level inside the upward leg of a cycle? |   0:      :: "Any non-negative integer"*OR* |
| nrelsteps_down | 2 | How many times should we relax each level inside the downward leg of a cycle? |   0:      :: "Any non-negative integer"*OR* |
| nrelsteps_bottom | 2 | How many times should we relax each level at the bottom of a cycle? |   0:      :: "Any non-negative integer"*OR* |
| nrelsteps_top | 2 | How many times should we relax each level at the top of a cycle? |   0:      :: "Any non-negative integer"*OR* |
| integral_refinement | 1 | How much to refine the grid via interpolation before calculating integrals |   1:*   :: "Any integer greater than zero"*OR* |
| tol | 1e-06 | Maximum residual tolerated |   0:* ::  "Any non-negative real"*OR* |
| eps | 1e-06 | Regularization factor at the punctures |   0:* ::  "Any non-negative real"*OR* |
| omega | 1 | Overrelaxation factor |   0:2 ::  "Real larger than zero and smaller than 2"*OR* |
| reset_psi | "no" | Reset psi after each relaxation step? How? |   "no"                    :: "Do not reset"*OR*  "to value"              :: "Reset to the value specified by reset_value"*OR*  "through integrability" :: "Reset so that the integrability condition is satisfied"*OR* |
| reset_every | 1 | How often should we reset psi? |   1:*   :: "Any positive integer"*OR* |
| reset_x | 0 | x-coordinate of point of reference for variable resetting |   : ::  "Any real number (contained in the domain!)"*OR* |
| reset_y | 0 | y-coordinate of point of reference for variable resetting |   : ::  "Any real number (contained in the domain!)"*OR* |
| reset_z | 0 | z-coordinate of point of reference for variable resetting |   : ::  "Any real number (contained in the domain!)"*OR* |
| enforce_int | 0 | Enforce the integral compatibility condition? |   0:1:1 :: "True or false"*OR* |
| topMGlevel | 0 | Finest level that covers the entire domain |   0:      :: "Any non-negative integer (< Carpet::reflevels!)"*OR* |
| fill_Aij | "Analytic | Where does the final Aij come from? |   "Solver"       :: "Aij is solved for as well"*OR*  "Analytic Xi"  :: "Aij comes from differentiating an analytic Xi"*OR*  "Analytic Aij" :: "Aij comes from an exact solution"*OR* |


## CactusConnect

### HTTPDExtra

Cactus Code Thorn HTTPDExtra
Author(s)    : Gabrielle Allen
               Tom Goodale
               Thomas Radke
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn registers additional HTML pages with the web server provided by
thorn HTTPD.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| viewport_refresh_seconds | -1 | Viewport web page refresh time seconds |     -1:      :: "-1 for no refresh, 0 for immediate refresh"*OR* |


### HTTPD

Cactus Code Thorn HTTPD
Author(s)    : Gabrielle Allen
               Tom Goodale
               Thomas Radke
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This is a prototype C version of a web server thorn.  

[Note that it has not been audited against buffer overruns or other
security problems, so please be aware that it is possible that someone
could exploit this server to access your machine if you run the server
on an open network.]

Usage:
-------------------

Interface:
---------

The current interface for this webserver is contained in

http_Request.h

(Note that this will change once we have an agreed common protocol for
other thorns to talk to web-serving thorns.)  There are also utility
interface for parameter steering and HTTP authentication, which are
described in the files in the doc directory.

The primary mechanism is to

HTTP_RegisterPage(path, function, data)

where 'path' is the URI you want to register, e.g.

/parameters

'function' is a function of the form

int foo(const cGH *cctkGH, httpRequest *request, void *data)

which will be called when the page is accessed, with 'data' being the
same data pointer that was passed into the registration routine.

Note that a request of the form '/foo/bar/' will look for a page of
the called '/foo/var/index.html' first, followed by '/foo/bar',
followed by '/foo' followed by '/'.

The httpRequest structure contains various pieces of information -

request->body               The body of the http request.  This is
                            empty for a GET.

request->body_length        The length of the body.

request->method             The http method.
request->uri                The full URI of the request.
request->residual           The URI remaining after the registered URI 
                            is removed from it.

request->http_major_version The major version of the HTTP protocol
                            used.

request->http_minor_version The minor version of the HTTP protocol
                            used.

request->n_arguments        The number of arguments passed to the
                            request.

All other members of the structure should be considered opaque.


To get details of arguments or HTTP header lines, the functions

const char *HTTP_ArgumentValue(const httpRequest *request, 
                               const char *arg);

const char *HTTP_HeaderValue(const httpRequest *request, 
                             const char *header);

may be used.  Given the name of a header field or an argument, these
functions will return the corresponding value, or NULL if the argument 
or header does not exist.

There is also a function

const httpArg *HTTP_ArgumentWalk(httpRequest *request, int first);

which will walk through all the arguments passed to a page.  If
'first' is true it will start with the first argument, otherwise it
will start with the next argument after the last one queried.
Currently this is the only way to get arguments which are passed in
more than once.  The httpArg structure contains two public variables

arg                      The name of the argument.
value                    The value of the argument.


Writing to the browser:
----------------------

This is done with the 

HTTP_Write(httpRequest *request, const char *buffer, size_t count);

function.  This takes three arguments - the request, a buffer, and the
length of the buffer.

The first line sent to the browser should be


HTTP_Write(request, "HTTP/1.0 200 OK\r\n",19)

(or an error code, e.g. "404 Not Found").  This should be followed by
any HTTP headers, such as "Content-Type", followed by "\r\n\r\n" and
then the content of your page, or at least that's what the standard
says 8-).  You may make as many calls to this function as you like to
get the data to the browser.

Examples:
---------

For examples of how to use the interface, please look at the Content.c 
file.

Structure of the thorn:
----------------------

The thorn is split into three parts - basic web serving, utilities,
and content provision

Web serving:
-----------
The web server capabilities are held in

Sockets.c           - basic socket utilities;  these should be
                      replaced by or moved to the Socket thorn.

Server.c            - Stuff to register and serve pages.

http.c              - Parsing of HTTP requests.

Startup.c           - normal scheduled stuff.

The above files should not need to be touched apart from debugging
purposes, to add more HTTP methods, or otherwise to enhance the
interaction with the network and protocols.

Utilities:
_________

The web server provided utilities to help content provision -

Steer.c             - parameter steering interface
                    - see doc/Steering.txt

Authorisation.c     - HTTP authentication
                    - see doc/Auth.txt

Content Provision:
-----------------

The content provision is currently  in 

Headers.c

Content.c

Groups.c

Parameters.c

This makes use of the interfaces in http_Request.h described above to
provide content.  In principle this could be in another thorn, but
that should wait until we have an agreed set of interfaces to allow
web-server thorns to be interchangeable, at least at compile time, but 
hopefully at run-time.



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| port | 5555 | HTTP port number to use (can be overridden by shell variable HTTPD_PORT) |     1:65535 :: "Any valid port"*OR* |
| hunt | "yes" | Should the server hunt for a port if the specified one is taken ? |  |
| steering_frequency | 1 | How many iterations between steering events |  0:  :: "Any number, 0 means don't steer"*OR* |
| provide_pages | "yes" | Should the server provide any pages ? |  |
| pause | "no" | Pause ? |  |
| user | "anon" | The username for Cactus Control  |   ".+" :: "Any name of one or more characters"*OR* |
| password | "anon" | The password for Cactus Control |   ".*" :: "Any password"*OR* |
| encryption_scheme | "none" | How the password is encrypted |   "none"  :: "Not encrypted"*OR*  "crypt" :: "crypt(3) (standard UNIX passwd format)"*OR* |
| use_pthreads | "yes" | Use a threaded implementation if possible ? |  |
| verbose | "no" | Print information about HTTP requests |  |
| timeout_seconds | 0 | Polling timeout seconds |     0:      :: "Any whole number"*OR* |
| timeout_useconds | 0 | Polling timeout micro-seconds |     0:      :: "Any whole number"*OR* |
| queue_length |  | Listen queue length |     1:      :: "Any positive number"*OR* |
| refresh_seconds | -1 | Page refresh time seconds |     -1:      :: "-1 for no refresh, 0 for immediate refresh"*OR* |
| terminate | "no" | Kill the simulation ? |  |
| single_step | "no" | Do one step then pause ? |  |
| until_it_active | "no" | Use until_it parameter ? |  |
| until_it | 0 | Pause at this iteration |  : :: "Any iteration"*OR* |
| until_time_active | "no" | Use until_time parameter ? |  |
| until_time | 0 | Pause after this simulation time |  : :: "Any time"*OR* |
| until_expression_active | "no" | Use until_expression parameter ? |  |
| until_expression | "iteration+40 | Pause if this expression evaluates to true. |  ".*" :: "Any expression"*OR* |


### Socket

Cactus Code Thorn Socket
Author(s)    : Tom Goodale
               Thomas Radke
               John Shalf
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn provides platform-independent socket calls.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


## CactusWave

### WaveToyF77

Cactus Code Thorn WaveToyF77
Author(s)    : Tom Goodale
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn solves the linear wave equation




| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| bound | "none" | Type of boundary condition to use |   "none"      :: "Apply no boundary condition"*OR*  "flat"      :: "Flat (von Neumann, n grad phi = 0) boundary condition"*OR*  "static"    :: "Static (Dirichlet, dphi/dt=0) boundary condition"*OR*  "radiation" :: "Radiation boundary condition"*OR*  "robin"     :: "Robin (phi(r) = C/r) boundary condition"*OR*  "zero"      :: "Zero (Dirichlet, phi=0) boundary condition"*OR* |


### IDScalarWave

Cactus Code Thorn IDScalarWave
Author(s)    : Tom Goodale
               Gabrielle Allen
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorns sets initial data for the scalar wave equation evolver



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| initial_data | "gaussian" | Type of initial data |   "plane"      :: "Plane wave"*OR*  "gaussian"   :: "Gaussian wave"*OR*  "box"        :: "Box wave"*OR*  "none"       :: "No initial data, zero phi"*OR* |
| radius | 0.0 | The radius of the gaussian wave |  0:* :: "Positive"*OR* |
| sigma | 0.1 | The sigma for the gaussian wave |  0:* :: "Positive"*OR* |
| kx | 4.0 | The wave number in the x-direction |  *:* :: "No restriction"*OR* |
| ky | 0.0 | The wave number in the y-direction |  *:* :: "No restriction"*OR* |
| kz | 0.0 | The wave number in the z-direction |  *:* :: "No restriction"*OR* |
| amplitude | 1.0 | The amplitude of the waves |   *:* :: "No restriction"*OR* |


### WaveToyF90

Cactus Code Thorn WaveToyF90
Author(s)    : Tom Goodale
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn solves the linear wave equation




| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| bound | "none" | Type of boundary condition to use |   "none"      :: "Apply no boundary condition"*OR*  "flat"      :: "Flat (von Neumann, n grad phi = 0) boundary condition"*OR*  "static"    :: "Static (Dirichlet, dphi/dt=0) boundary condition"*OR*  "radiation" :: "Radiation boundary condition"*OR*  "robin"     :: "Robin (phi(r) = C/r) boundary condition"*OR*  "zero"      :: "Zero (Dirichlet, phi=0) boundary condition"*OR* |


### IDScalarWaveElliptic

Cactus Code Thorn IDScalarWaveElliptic
Author(s)    : unknown
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

not documented


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| radius | 1.0 | Radius of uniformly charged sphere |  0:* :: "Anything"*OR* |
| charge | 1.0 | Charge of uniformly charged sphere |  0:* :: "Anything"*OR* |
| output_tmp | "no" | Output intermediate variables |  |
| solver | "sor" | Name of elliptic solver | .* :: "Anything"*OR* |
| sor_maxit | 100 | Maximum number of iterations for SOR solve |  1:* :: "Positive"*OR* |


### WaveToyC

Cactus Code Thorn WaveToyC
Author(s)    : Gabrielle Allen
               Tom Goodale
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn solves the linear wave equation.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| bound | "none" | Type of boundary condition to use |   "none"      :: "Apply no boundary condition"*OR*  "flat"      :: "Flat (von Neumann, n grad phi = 0) boundary condition"*OR*  "static"    :: "Static (Dirichlet, dphi/dt=0) boundary condition"*OR*  "radiation" :: "Radiation boundary condition"*OR*  "robin"     :: "Robin (phi(r) = C/r) boundary condition"*OR*  "zero"      :: "Zero (Dirichlet, phi=0) boundary condition"*OR* |


### IDScalarWaveC

Cactus Code Thorn IDScalarWaveC
Author(s)    : Tom Goodale
               Gabrielle Allen
               Werner Benger
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn sets initial data for the scalar wave equation evolver.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| initial_data | "gaussian" | Type of initial data |   "plane"      :: "Plane wave"*OR*  "gaussian"   :: "Gaussian wave"*OR*  "box"        :: "Box wave"*OR*  "none"       :: "No initial data, zero phi"*OR* |
| radius | 0.0 | The radius of the gaussian wave |  0:* :: "Positive"*OR* |
| sigma | 0.1 | The sigma for the gaussian wave |  0:* :: "Positive"*OR* |
| kx | 4.0 | The wave number in the x-direction |  *:* :: "No restriction"*OR* |
| ky | 0.0 | The wave number in the y-direction |  *:* :: "No restriction"*OR* |
| kz | 0.0 | The wave number in the z-direction |  *:* :: "No restriction"*OR* |
| amplitude | 1.0 | The amplitude of the waves |   *:* :: "No restriction"*OR* |


### WaveBinarySource

Cactus Code Thorn WaveBinarySource
Author(s)    : Gerd Lanfermann
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Provides a source term to the scalar field evolution
for two rotating binary charges.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| binary_verbose | "no" | Rotating binary source verbose |   "yes"  :: "Info on charge location/extension on first iteration"*OR*  "debug":: "Info on charge location/extension on all iterations"*OR*  "no"   :: "no output"*OR* |
| binary_size | 0.5 | Radial extension of the binary source |   0.0: :: "Some positive value"*OR* |
| binary_omega | 2.0 | Frequency of the circular binary orbit |  0.0: :: "Some positive value"*OR* |
| binary_charge | 0.1 | Charge of source |  : :: "No restriction"*OR* |
| binary_radius | 2.0 | Radius of the circular binary orbit |  0.0: :: "Some positive value"*OR* |


### WaveToyFreeF90

Cactus Code Thorn WaveToyFreeF90
Author(s)    : Tom Goodale
               Erik Schnetter
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn solves the linear wave equation, using free form (as
opposed to fixed form) Fortran 90.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| bound | "none" | Type of boundary condition to use |   "none"      :: "Apply no boundary condition"*OR*  "flat"      :: "Flat (von Neumann, n grad phi = 0) boundary condition"*OR*  "static"    :: "Static (Dirichlet, dphi/dt=0) boundary condition"*OR*  "radiation" :: "Radiation boundary condition"*OR*  "robin"     :: "Robin (phi(r) = C/r) boundary condition"*OR*  "zero"      :: "Zero (Dirichlet, phi=0) boundary condition"*OR* |


### WaveToyExtra

Cactus Code Thorn WaveToyExtra
Author(s)    : Cactus team
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Additional functionality for the WaveToy example

- custom boundary conditions


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| custom_bound | "" | Custom boundary condition to use |  .* :: "Any valid boundary condition"*OR* |
| custom_options | "" | Table string for custom boundary condition |  .* :: "Any valid table string"*OR* |


### IDScalarWaveCXX

Cactus Code Thorn IDScalarWaveCXX
Author(s)    : Tom Goodale
               Gabrielle Allen
               Werner Benger
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorns sets initial data for the scalar wave equation evolver



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| initial_data | "gaussian" | Type of initial data |   "plane"      :: "Plane wave"*OR*  "gaussian"   :: "Gaussian wave"*OR*  "box"        :: "Box wave"*OR*  "none"       :: "No initial data, zero phi"*OR* |
| radius | 0.0 | The radius of the gaussian wave |  0:* :: "Positive"*OR* |
| sigma | 0.1 | The sigma for the gaussian wave |  0:* :: "Positive"*OR* |
| kx | 4.0 | The wave number in the x-direction |  *:* :: "No restriction"*OR* |
| ky | 0.0 | The wave number in the y-direction |  *:* :: "No restriction"*OR* |
| kz | 0.0 | The wave number in the z-direction |  *:* :: "No restriction"*OR* |
| amplitude | 1.0 | The amplitude of the waves |   *:* :: "No restriction"*OR* |


### WaveToyCXX

Cactus Code Thorn WaveToyCXX
Author(s)    : unknown
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

not documented


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| bound | "none" | Type of boundary condition to use |   "none"      :: "Apply no boundary condition"*OR*  "flat"      :: "Flat (von Neumann, n grad phi = 0) boundary condition"*OR*  "static"    :: "Static (Dirichlet, dphi/dt=0) boundary condition"*OR*  "radiation" :: "Radiation boundary condition"*OR*  "robin"     :: "Robin (phi(r) = C/r) boundary condition"*OR*  "zero"      :: "Zero (Dirichlet, phi=0) boundary condition"*OR* |


## EinsteinExact

### EinsteinExact_Test

| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### ModifiedSchwarzschildBL

| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| initial_data |  | initial_data |   "ModifiedSchwarzschildBL" :: ""*OR* |
| initial_lapse |  | initial_lapse |   "ModifiedSchwarzschildBL" :: ""*OR* |
| initial_shift |  | initial_shift |   "ModifiedSchwarzschildBL" :: ""*OR* |
| initial_dtlapse |  | initial_dtlapse |   "ModifiedSchwarzschildBL" :: ""*OR* |
| initial_dtshift |  | initial_dtshift |   "ModifiedSchwarzschildBL" :: ""*OR* |
| evolution_method |  | evolution_method |   "ModifiedSchwarzschildBL" :: ""*OR* |
| verbose | 0 | verbose |   *:* :: ""*OR* |
| other_timelevels | 1 | Number of active timelevels for non-evolved grid functions |   0:3 :: ""*OR* |
| timeoffset | 0 | timeoffset |   *:* :: ""*OR* |
| positionx | 0 | positionx |   *:* :: ""*OR* |
| positiony | 0 | positiony |   *:* :: ""*OR* |
| positionz | 0 | positionz |   *:* :: ""*OR* |
| theta | 0 | theta |   *:* :: ""*OR* |
| phi | 0 | phi |   *:* :: ""*OR* |
| psi | 0 | psi |   *:* :: ""*OR* |
| lapsefactor | 1 | lapsefactor |   *:* :: ""*OR* |
| boostx | 0 | boostx |   *:* :: ""*OR* |
| boosty | 0 | boosty |   *:* :: ""*OR* |
| boostz | 0 | boostz |   *:* :: ""*OR* |
| shiftaddx | 0 | shiftaddx |   *:* :: ""*OR* |
| shiftaddy | 0 | shiftaddy |   *:* :: ""*OR* |
| shiftaddz | 0 | shiftaddz |   *:* :: ""*OR* |
| M | 0 | M |   *:* :: ""*OR* |
| epsilon | 0 | epsilon |   *:* :: ""*OR* |
| tile_size | -1 | Loop tile size |   *:* :: ""*OR* |
| exact_method | "none" | exact_method |   "none" :: ""*OR*  "ModifiedSchwarzschildBL" :: ""*OR* |
| ModifiedSchwarzschildBL_MaxNumEvolvedVars | 0 | Number of evolved variables used by this thorn |   0:0 :: "Number of evolved variables used by this thorn"*OR* |
| ModifiedSchwarzschildBL_MaxNumArrayEvolvedVars | 0 | Number of Array evolved variables used by this thorn |   0:0 :: "Number of Array evolved variables used by this thorn"*OR* |
| timelevels | 3 | Number of active timelevels |   0:3 :: ""*OR* |
| rhs_timelevels | 1 | Number of active RHS timelevels |   0:3 :: ""*OR* |
| ModifiedSchwarzschildBL_initial_calc_every | 1 | ModifiedSchwarzschildBL_initial_calc_every |   *:* :: ""*OR* |
| ModifiedSchwarzschildBL_always_calc_every | 1 | ModifiedSchwarzschildBL_always_calc_every |   *:* :: ""*OR* |
| ModifiedSchwarzschildBL_exact_calc_every | 1 | ModifiedSchwarzschildBL_exact_calc_every |   *:* :: ""*OR* |
| ModifiedSchwarzschildBL_initial_calc_offset | 0 | ModifiedSchwarzschildBL_initial_calc_offset |   *:* :: ""*OR* |
| ModifiedSchwarzschildBL_always_calc_offset | 0 | ModifiedSchwarzschildBL_always_calc_offset |   *:* :: ""*OR* |
| ModifiedSchwarzschildBL_exact_calc_offset | 0 | ModifiedSchwarzschildBL_exact_calc_offset |   *:* :: ""*OR* |


### GaugeWave

| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| initial_data |  | initial_data |   "GaugeWave" :: ""*OR* |
| initial_lapse |  | initial_lapse |   "GaugeWave" :: ""*OR* |
| initial_shift |  | initial_shift |   "GaugeWave" :: ""*OR* |
| initial_dtlapse |  | initial_dtlapse |   "GaugeWave" :: ""*OR* |
| initial_dtshift |  | initial_dtshift |   "GaugeWave" :: ""*OR* |
| evolution_method |  | evolution_method |   "GaugeWave" :: ""*OR* |
| verbose | 0 | verbose |   *:* :: ""*OR* |
| other_timelevels | 1 | Number of active timelevels for non-evolved grid functions |   0:3 :: ""*OR* |
| timeoffset | 0 | timeoffset |   *:* :: ""*OR* |
| positionx | 0 | positionx |   *:* :: ""*OR* |
| positiony | 0 | positiony |   *:* :: ""*OR* |
| positionz | 0 | positionz |   *:* :: ""*OR* |
| theta | 0 | theta |   *:* :: ""*OR* |
| phi | 0 | phi |   *:* :: ""*OR* |
| psi | 0 | psi |   *:* :: ""*OR* |
| lapsefactor | 1 | lapsefactor |   *:* :: ""*OR* |
| boostx | 0 | boostx |   *:* :: ""*OR* |
| boosty | 0 | boosty |   *:* :: ""*OR* |
| boostz | 0 | boostz |   *:* :: ""*OR* |
| shiftaddx | 0 | shiftaddx |   *:* :: ""*OR* |
| shiftaddy | 0 | shiftaddy |   *:* :: ""*OR* |
| shiftaddz | 0 | shiftaddz |   *:* :: ""*OR* |
| amp | 0 | amp |   *:* :: ""*OR* |
| period | 0 | period |   *:* :: ""*OR* |
| tile_size | -1 | Loop tile size |   *:* :: ""*OR* |
| exact_method | "none" | exact_method |   "none" :: ""*OR*  "GaugeWave" :: ""*OR* |
| GaugeWave_MaxNumEvolvedVars | 0 | Number of evolved variables used by this thorn |   0:0 :: "Number of evolved variables used by this thorn"*OR* |
| GaugeWave_MaxNumArrayEvolvedVars | 0 | Number of Array evolved variables used by this thorn |   0:0 :: "Number of Array evolved variables used by this thorn"*OR* |
| timelevels | 3 | Number of active timelevels |   0:3 :: ""*OR* |
| rhs_timelevels | 1 | Number of active RHS timelevels |   0:3 :: ""*OR* |
| GaugeWave_initial_calc_every | 1 | GaugeWave_initial_calc_every |   *:* :: ""*OR* |
| GaugeWave_always_calc_every | 1 | GaugeWave_always_calc_every |   *:* :: ""*OR* |
| GaugeWave_exact_calc_every | 1 | GaugeWave_exact_calc_every |   *:* :: ""*OR* |
| GaugeWave_initial_calc_offset | 0 | GaugeWave_initial_calc_offset |   *:* :: ""*OR* |
| GaugeWave_always_calc_offset | 0 | GaugeWave_always_calc_offset |   *:* :: ""*OR* |
| GaugeWave_exact_calc_offset | 0 | GaugeWave_exact_calc_offset |   *:* :: ""*OR* |


### tests

### Minkowski

| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| initial_data |  | initial_data |   "Minkowski" :: ""*OR* |
| initial_lapse |  | initial_lapse |   "Minkowski" :: ""*OR* |
| initial_shift |  | initial_shift |   "Minkowski" :: ""*OR* |
| initial_dtlapse |  | initial_dtlapse |   "Minkowski" :: ""*OR* |
| initial_dtshift |  | initial_dtshift |   "Minkowski" :: ""*OR* |
| evolution_method |  | evolution_method |   "Minkowski" :: ""*OR* |
| verbose | 0 | verbose |   *:* :: ""*OR* |
| other_timelevels | 1 | Number of active timelevels for non-evolved grid functions |   0:3 :: ""*OR* |
| timeoffset | 0 | timeoffset |   *:* :: ""*OR* |
| positionx | 0 | positionx |   *:* :: ""*OR* |
| positiony | 0 | positiony |   *:* :: ""*OR* |
| positionz | 0 | positionz |   *:* :: ""*OR* |
| theta | 0 | theta |   *:* :: ""*OR* |
| phi | 0 | phi |   *:* :: ""*OR* |
| psi | 0 | psi |   *:* :: ""*OR* |
| lapsefactor | 1 | lapsefactor |   *:* :: ""*OR* |
| boostx | 0 | boostx |   *:* :: ""*OR* |
| boosty | 0 | boosty |   *:* :: ""*OR* |
| boostz | 0 | boostz |   *:* :: ""*OR* |
| shiftaddx | 0 | shiftaddx |   *:* :: ""*OR* |
| shiftaddy | 0 | shiftaddy |   *:* :: ""*OR* |
| shiftaddz | 0 | shiftaddz |   *:* :: ""*OR* |
| tile_size | -1 | Loop tile size |   *:* :: ""*OR* |
| exact_method | "none" | exact_method |   "none" :: ""*OR*  "Minkowski" :: ""*OR* |
| Minkowski_MaxNumEvolvedVars | 0 | Number of evolved variables used by this thorn |   0:0 :: "Number of evolved variables used by this thorn"*OR* |
| Minkowski_MaxNumArrayEvolvedVars | 0 | Number of Array evolved variables used by this thorn |   0:0 :: "Number of Array evolved variables used by this thorn"*OR* |
| timelevels | 3 | Number of active timelevels |   0:3 :: ""*OR* |
| rhs_timelevels | 1 | Number of active RHS timelevels |   0:3 :: ""*OR* |
| Minkowski_initial_calc_every | 1 | Minkowski_initial_calc_every |   *:* :: ""*OR* |
| Minkowski_always_calc_every | 1 | Minkowski_always_calc_every |   *:* :: ""*OR* |
| Minkowski_exact_calc_every | 1 | Minkowski_exact_calc_every |   *:* :: ""*OR* |
| Minkowski_initial_calc_offset | 0 | Minkowski_initial_calc_offset |   *:* :: ""*OR* |
| Minkowski_always_calc_offset | 0 | Minkowski_always_calc_offset |   *:* :: ""*OR* |
| Minkowski_exact_calc_offset | 0 | Minkowski_exact_calc_offset |   *:* :: ""*OR* |


### ShiftedGaugeWave

| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| initial_data |  | initial_data |   "ShiftedGaugeWave" :: ""*OR* |
| initial_lapse |  | initial_lapse |   "ShiftedGaugeWave" :: ""*OR* |
| initial_shift |  | initial_shift |   "ShiftedGaugeWave" :: ""*OR* |
| initial_dtlapse |  | initial_dtlapse |   "ShiftedGaugeWave" :: ""*OR* |
| initial_dtshift |  | initial_dtshift |   "ShiftedGaugeWave" :: ""*OR* |
| evolution_method |  | evolution_method |   "ShiftedGaugeWave" :: ""*OR* |
| verbose | 0 | verbose |   *:* :: ""*OR* |
| other_timelevels | 1 | Number of active timelevels for non-evolved grid functions |   0:3 :: ""*OR* |
| timeoffset | 0 | timeoffset |   *:* :: ""*OR* |
| positionx | 0 | positionx |   *:* :: ""*OR* |
| positiony | 0 | positiony |   *:* :: ""*OR* |
| positionz | 0 | positionz |   *:* :: ""*OR* |
| theta | 0 | theta |   *:* :: ""*OR* |
| phi | 0 | phi |   *:* :: ""*OR* |
| psi | 0 | psi |   *:* :: ""*OR* |
| lapsefactor | 1 | lapsefactor |   *:* :: ""*OR* |
| boostx | 0 | boostx |   *:* :: ""*OR* |
| boosty | 0 | boosty |   *:* :: ""*OR* |
| boostz | 0 | boostz |   *:* :: ""*OR* |
| shiftaddx | 0 | shiftaddx |   *:* :: ""*OR* |
| shiftaddy | 0 | shiftaddy |   *:* :: ""*OR* |
| shiftaddz | 0 | shiftaddz |   *:* :: ""*OR* |
| amp | 0 | amp |   *:* :: ""*OR* |
| period | 0 | period |   *:* :: ""*OR* |
| tile_size | -1 | Loop tile size |   *:* :: ""*OR* |
| exact_method | "none" | exact_method |   "none" :: ""*OR*  "ShiftedGaugeWave" :: ""*OR* |
| ShiftedGaugeWave_MaxNumEvolvedVars | 0 | Number of evolved variables used by this thorn |   0:0 :: "Number of evolved variables used by this thorn"*OR* |
| ShiftedGaugeWave_MaxNumArrayEvolvedVars | 0 | Number of Array evolved variables used by this thorn |   0:0 :: "Number of Array evolved variables used by this thorn"*OR* |
| timelevels | 3 | Number of active timelevels |   0:3 :: ""*OR* |
| rhs_timelevels | 1 | Number of active RHS timelevels |   0:3 :: ""*OR* |
| ShiftedGaugeWave_initial_calc_every | 1 | ShiftedGaugeWave_initial_calc_every |   *:* :: ""*OR* |
| ShiftedGaugeWave_always_calc_every | 1 | ShiftedGaugeWave_always_calc_every |   *:* :: ""*OR* |
| ShiftedGaugeWave_exact_calc_every | 1 | ShiftedGaugeWave_exact_calc_every |   *:* :: ""*OR* |
| ShiftedGaugeWave_initial_calc_offset | 0 | ShiftedGaugeWave_initial_calc_offset |   *:* :: ""*OR* |
| ShiftedGaugeWave_always_calc_offset | 0 | ShiftedGaugeWave_always_calc_offset |   *:* :: ""*OR* |
| ShiftedGaugeWave_exact_calc_offset | 0 | ShiftedGaugeWave_exact_calc_offset |   *:* :: ""*OR* |


### doc

### KerrSchild

| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| initial_data |  | initial_data |   "KerrSchild" :: ""*OR* |
| initial_lapse |  | initial_lapse |   "KerrSchild" :: ""*OR* |
| initial_shift |  | initial_shift |   "KerrSchild" :: ""*OR* |
| initial_dtlapse |  | initial_dtlapse |   "KerrSchild" :: ""*OR* |
| initial_dtshift |  | initial_dtshift |   "KerrSchild" :: ""*OR* |
| evolution_method |  | evolution_method |   "KerrSchild" :: ""*OR* |
| verbose | 0 | verbose |   *:* :: ""*OR* |
| other_timelevels | 1 | Number of active timelevels for non-evolved grid functions |   0:3 :: ""*OR* |
| timeoffset | 0 | timeoffset |   *:* :: ""*OR* |
| positionx | 0 | positionx |   *:* :: ""*OR* |
| positiony | 0 | positiony |   *:* :: ""*OR* |
| positionz | 0 | positionz |   *:* :: ""*OR* |
| theta | 0 | theta |   *:* :: ""*OR* |
| phi | 0 | phi |   *:* :: ""*OR* |
| psi | 0 | psi |   *:* :: ""*OR* |
| lapsefactor | 1 | lapsefactor |   *:* :: ""*OR* |
| boostx | 0 | boostx |   *:* :: ""*OR* |
| boosty | 0 | boosty |   *:* :: ""*OR* |
| boostz | 0 | boostz |   *:* :: ""*OR* |
| shiftaddx | 0 | shiftaddx |   *:* :: ""*OR* |
| shiftaddy | 0 | shiftaddy |   *:* :: ""*OR* |
| shiftaddz | 0 | shiftaddz |   *:* :: ""*OR* |
| M | 0 | M |   *:* :: ""*OR* |
| a | 0 | a |   *:* :: ""*OR* |
| tile_size | -1 | Loop tile size |   *:* :: ""*OR* |
| exact_method | "none" | exact_method |   "none" :: ""*OR*  "KerrSchild" :: ""*OR* |
| KerrSchild_MaxNumEvolvedVars | 0 | Number of evolved variables used by this thorn |   0:0 :: "Number of evolved variables used by this thorn"*OR* |
| KerrSchild_MaxNumArrayEvolvedVars | 0 | Number of Array evolved variables used by this thorn |   0:0 :: "Number of Array evolved variables used by this thorn"*OR* |
| timelevels | 3 | Number of active timelevels |   0:3 :: ""*OR* |
| rhs_timelevels | 1 | Number of active RHS timelevels |   0:3 :: ""*OR* |
| KerrSchild_initial_calc_every | 1 | KerrSchild_initial_calc_every |   *:* :: ""*OR* |
| KerrSchild_always_calc_every | 1 | KerrSchild_always_calc_every |   *:* :: ""*OR* |
| KerrSchild_exact_calc_every | 1 | KerrSchild_exact_calc_every |   *:* :: ""*OR* |
| KerrSchild_initial_calc_offset | 0 | KerrSchild_initial_calc_offset |   *:* :: ""*OR* |
| KerrSchild_always_calc_offset | 0 | KerrSchild_always_calc_offset |   *:* :: ""*OR* |
| KerrSchild_exact_calc_offset | 0 | KerrSchild_exact_calc_offset |   *:* :: ""*OR* |


### Vaidya2

| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| initial_data |  | initial_data |   "Vaidya2" :: ""*OR* |
| initial_lapse |  | initial_lapse |   "Vaidya2" :: ""*OR* |
| initial_shift |  | initial_shift |   "Vaidya2" :: ""*OR* |
| initial_dtlapse |  | initial_dtlapse |   "Vaidya2" :: ""*OR* |
| initial_dtshift |  | initial_dtshift |   "Vaidya2" :: ""*OR* |
| evolution_method |  | evolution_method |   "Vaidya2" :: ""*OR* |
| verbose | 0 | verbose |   *:* :: ""*OR* |
| other_timelevels | 1 | Number of active timelevels for non-evolved grid functions |   0:3 :: ""*OR* |
| timeoffset | 0 | timeoffset |   *:* :: ""*OR* |
| positionx | 0 | positionx |   *:* :: ""*OR* |
| positiony | 0 | positiony |   *:* :: ""*OR* |
| positionz | 0 | positionz |   *:* :: ""*OR* |
| theta | 0 | theta |   *:* :: ""*OR* |
| phi | 0 | phi |   *:* :: ""*OR* |
| psi | 0 | psi |   *:* :: ""*OR* |
| lapsefactor | 1 | lapsefactor |   *:* :: ""*OR* |
| boostx | 0 | boostx |   *:* :: ""*OR* |
| boosty | 0 | boosty |   *:* :: ""*OR* |
| boostz | 0 | boostz |   *:* :: ""*OR* |
| shiftaddx | 0 | shiftaddx |   *:* :: ""*OR* |
| shiftaddy | 0 | shiftaddy |   *:* :: ""*OR* |
| shiftaddz | 0 | shiftaddz |   *:* :: ""*OR* |
| M | 0 | M |   *:* :: ""*OR* |
| dM | 0 | dM |   *:* :: ""*OR* |
| tile_size | -1 | Loop tile size |   *:* :: ""*OR* |
| exact_method | "none" | exact_method |   "none" :: ""*OR*  "Vaidya2" :: ""*OR* |
| Vaidya2_MaxNumEvolvedVars | 0 | Number of evolved variables used by this thorn |   0:0 :: "Number of evolved variables used by this thorn"*OR* |
| Vaidya2_MaxNumArrayEvolvedVars | 0 | Number of Array evolved variables used by this thorn |   0:0 :: "Number of Array evolved variables used by this thorn"*OR* |
| timelevels | 3 | Number of active timelevels |   0:3 :: ""*OR* |
| rhs_timelevels | 1 | Number of active RHS timelevels |   0:3 :: ""*OR* |
| Vaidya2_initial_calc_every | 1 | Vaidya2_initial_calc_every |   *:* :: ""*OR* |
| Vaidya2_always_calc_every | 1 | Vaidya2_always_calc_every |   *:* :: ""*OR* |
| Vaidya2_exact_calc_every | 1 | Vaidya2_exact_calc_every |   *:* :: ""*OR* |
| Vaidya2_initial_calc_offset | 0 | Vaidya2_initial_calc_offset |   *:* :: ""*OR* |
| Vaidya2_always_calc_offset | 0 | Vaidya2_always_calc_offset |   *:* :: ""*OR* |
| Vaidya2_exact_calc_offset | 0 | Vaidya2_exact_calc_offset |   *:* :: ""*OR* |


### m

## McLachlan

### ML_CCZ4_Test

| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### ML_CCZ4_Helper

| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| ML_CCZ4_MaxNumConstrainedVars | 20 | Number of constrained variables used by this thorn |   20:20 :: "Number of constrained variables used by this thorn"*OR* |


### ML_WaveToy_Test

| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### ML_WaveToy

| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | 0 | verbose |   *:* :: ""*OR* |
| other_timelevels | 1 | Number of active timelevels for non-evolved grid functions |   0:4 :: ""*OR* |
| amplitude | 1 | Amplitude of initial Gaussian |   *:* :: ""*OR* |
| width | 1 | Width of initial Gaussian |   *:* :: ""*OR* |
| tile_size | -1 | Loop tile size |   *:* :: ""*OR* |
| initial_data | "Gaussian" | initial_data |   "Gaussian" :: ""*OR*  "Standing" :: ""*OR* |
| ML_WaveToy_MaxNumEvolvedVars | 2 | Number of evolved variables used by this thorn |   2:2 :: "Number of evolved variables used by this thorn"*OR* |
| ML_WaveToy_MaxNumArrayEvolvedVars | 0 | Number of Array evolved variables used by this thorn |   0:0 :: "Number of Array evolved variables used by this thorn"*OR* |
| timelevels | 4 | Number of active timelevels |   0:4 :: ""*OR* |
| rhs_timelevels | 1 | Number of active RHS timelevels |   0:4 :: ""*OR* |
| WT_Gaussian_calc_every | 1 | WT_Gaussian_calc_every |   *:* :: ""*OR* |
| WT_Standing_calc_every | 1 | WT_Standing_calc_every |   *:* :: ""*OR* |
| WT_RHS_calc_every | 1 | WT_RHS_calc_every |   *:* :: ""*OR* |
| WT_Dirichlet_calc_every | 1 | WT_Dirichlet_calc_every |   *:* :: ""*OR* |
| WT_Energy_calc_every | 1 | WT_Energy_calc_every |   *:* :: ""*OR* |
| WT_EnergyBoundary_calc_every | 1 | WT_EnergyBoundary_calc_every |   *:* :: ""*OR* |
| WT_Gaussian_calc_offset | 0 | WT_Gaussian_calc_offset |   *:* :: ""*OR* |
| WT_Standing_calc_offset | 0 | WT_Standing_calc_offset |   *:* :: ""*OR* |
| WT_RHS_calc_offset | 0 | WT_RHS_calc_offset |   *:* :: ""*OR* |
| WT_Dirichlet_calc_offset | 0 | WT_Dirichlet_calc_offset |   *:* :: ""*OR* |
| WT_Energy_calc_offset | 0 | WT_Energy_calc_offset |   *:* :: ""*OR* |
| WT_EnergyBoundary_calc_offset | 0 | WT_EnergyBoundary_calc_offset |   *:* :: ""*OR* |
| u_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| rho_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| WT_u_bound | "none" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| WT_rho_bound | "none" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| u_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| rho_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| WT_u_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| WT_rho_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| u_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| rho_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| WT_u_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| WT_rho_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| u_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| rho_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| WT_u_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| WT_rho_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |


### ML_BSSN_Helper

| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| ML_BSSN_MaxNumConstrainedVars | 20 | Number of constrained variables used by this thorn |   20:20 :: "Number of constrained variables used by this thorn"*OR* |


### ML_WaveToy_CL

| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | 0 | verbose |   *:* :: ""*OR* |
| other_timelevels | 1 | Number of active timelevels for non-evolved grid functions |   0:4 :: ""*OR* |
| amplitude | 1 | Amplitude of initial Gaussian |   *:* :: ""*OR* |
| width | 1 | Width of initial Gaussian |   *:* :: ""*OR* |
| tile_size | -1 | Loop tile size |   *:* :: ""*OR* |
| initial_data | "Gaussian" | initial_data |   "Gaussian" :: ""*OR*  "Standing" :: ""*OR* |
| ML_WaveToy_CL_MaxNumEvolvedVars | 2 | Number of evolved variables used by this thorn |   2:2 :: "Number of evolved variables used by this thorn"*OR* |
| ML_WaveToy_CL_MaxNumArrayEvolvedVars | 0 | Number of Array evolved variables used by this thorn |   0:0 :: "Number of Array evolved variables used by this thorn"*OR* |
| timelevels | 4 | Number of active timelevels |   0:4 :: ""*OR* |
| rhs_timelevels | 1 | Number of active RHS timelevels |   0:4 :: ""*OR* |
| WT_CL_Gaussian_calc_every | 1 | WT_CL_Gaussian_calc_every |   *:* :: ""*OR* |
| WT_CL_Standing_calc_every | 1 | WT_CL_Standing_calc_every |   *:* :: ""*OR* |
| WT_CL_RHS_calc_every | 1 | WT_CL_RHS_calc_every |   *:* :: ""*OR* |
| WT_CL_Dirichlet_calc_every | 1 | WT_CL_Dirichlet_calc_every |   *:* :: ""*OR* |
| WT_CL_Energy_calc_every | 1 | WT_CL_Energy_calc_every |   *:* :: ""*OR* |
| WT_CL_EnergyBoundary_calc_every | 1 | WT_CL_EnergyBoundary_calc_every |   *:* :: ""*OR* |
| WT_CL_Gaussian_calc_offset | 0 | WT_CL_Gaussian_calc_offset |   *:* :: ""*OR* |
| WT_CL_Standing_calc_offset | 0 | WT_CL_Standing_calc_offset |   *:* :: ""*OR* |
| WT_CL_RHS_calc_offset | 0 | WT_CL_RHS_calc_offset |   *:* :: ""*OR* |
| WT_CL_Dirichlet_calc_offset | 0 | WT_CL_Dirichlet_calc_offset |   *:* :: ""*OR* |
| WT_CL_Energy_calc_offset | 0 | WT_CL_Energy_calc_offset |   *:* :: ""*OR* |
| WT_CL_EnergyBoundary_calc_offset | 0 | WT_CL_EnergyBoundary_calc_offset |   *:* :: ""*OR* |
| u_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| rho_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| WT_u_bound | "none" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| WT_rho_bound | "none" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| u_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| rho_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| WT_u_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| WT_rho_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| u_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| rho_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| WT_u_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| WT_rho_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| u_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| rho_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| WT_u_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| WT_rho_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |


### doc

### ML_BSSN

| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| evolution_method |  | evolution_method |   "ML_BSSN" :: ""*OR* |
| lapse_evolution_method |  | lapse_evolution_method |   "ML_BSSN" :: ""*OR* |
| shift_evolution_method |  | shift_evolution_method |   "ML_BSSN" :: ""*OR* |
| dtlapse_evolution_method |  | dtlapse_evolution_method |   "ML_BSSN" :: ""*OR* |
| dtshift_evolution_method |  | dtshift_evolution_method |   "ML_BSSN" :: ""*OR* |
| verbose | 0 | verbose |   *:* :: ""*OR* |
| other_timelevels | 1 | Number of active timelevels for non-evolved grid functions |   0:4 :: ""*OR* |
| harmonicF | 1 | d/dt alpha = - f alpha^n K   (harmonic: f=1, 1+log: f=2) |   *:* :: ""*OR* |
| alphaDriver | 0 | d/dt alpha = ... - alphaDriver (alpha - 1)   (use 1/M (?)) |   *:* :: ""*OR* |
| shiftGammaCoeff | 0 | d/dt beta^i = C Xt^i   (use C=0.75/M) |   *:* :: ""*OR* |
| betaDriver | 0 | d/dt beta^i = ... - betaDriver alpha^shiftAlphaPower beta^i   (use 1/M (?)) |   *:* :: ""*OR* |
| shiftAlphaPower | 0 | d/dt beta^i = ... - betaDriver alpha^shiftAlphaPower beta^i   (use 0 (?)) |   *:* :: ""*OR* |
| spatialBetaDriverRadius | 1000000000000 | Radius at which the betaDriver starts to be reduced |   (0:* :: "positive"*OR* |
| spatialShiftGammaCoeffRadius | 1000000000000 | Radius at which shiftGammaCoeff starts to be reduced |   (0:* :: "positive"*OR* |
| minimumLapse | 0 | Enforced minimum of the lapse function |   0:* :: "non-negative"*OR* |
| epsDiss | 0 | Dissipation strength |   0:* :: "non-negative"*OR* |
| LapseACoeff | -1. | (OUTDATED) Evolve time derivative of lapse A? (now evolveA) |   0. :: "off"*OR*  1. :: "on"*OR*  -1. :: "default"*OR* |
| ShiftBCoeff | -1. | (OUTDATED) Evolve time derivative of shift B^i? (now evolveB) |   0. :: "off"*OR*  1. :: "on"*OR*  -1. :: "default"*OR* |
| LapseAdvectionCoeff | -1. | (OUTDATED) Advect lapse? (now advectLapse) |   0. :: "off"*OR*  1. :: "on"*OR*  -1. :: "default"*OR* |
| ShiftAdvectionCoeff | -1. | (OUTDATED) Advect shift? (now advectShift) |   0. :: "off"*OR*  1. :: "on"*OR*  -1. :: "default"*OR* |
| fdOrder | 4 | Finite differencing order |   2 :: ""*OR*  4 :: ""*OR*  6 :: ""*OR*  8 :: ""*OR* |
| conformalMethod | 0 | Treatment of conformal factor |   0 :: "phi method"*OR*  1 :: "W method"*OR* |
| evolveA | 0 | Evolve time derivative of lapse A? (former LapseACoeff) |   0 :: "off"*OR*  1 :: "on"*OR* |
| evolveB | 1 | Evolve time derivative of shift B^i? (former ShiftBCoeff) |   0 :: "off"*OR*  1 :: "on"*OR* |
| harmonicN | 2 | d/dt alpha = - f alpha^n K  (harmonic: n=2, 1+log: n=1) |   *:* :: ""*OR* |
| shiftFormulation | 0 | shift formulation |   0 :: "Gamma driver"*OR*  1 :: "harmonic"*OR* |
| useSpatialBetaDriver | 0 | Enable spatially varying betaDriver |   0 :: "off"*OR*  1 :: "on"*OR* |
| useSpatialShiftGammaCoeff | 0 | Enable spatially varying shiftGammaCoeff |   0 :: "off"*OR*  1 :: "on"*OR* |
| advectLapse | 1 | Advect lapse? (former LapseAdvectionCoeff) |   0 :: "off"*OR*  1 :: "on"*OR* |
| advectShift | 1 | Advect shift? (former ShiftAdvectionCoeff) |   0 :: "off"*OR*  1 :: "on"*OR* |
| fixAdvectionTerms | 0 | Modify driver and advection terms to work better? |   0 :: "off"*OR*  1 :: "on"*OR* |
| tile_size | -1 | Loop tile size |   *:* :: ""*OR* |
| initial_boundary_condition | "scalar" | Boundary condition for initial condition for some of the BSSN variables |   "scalar" :: "not recommended; use ML_BSSN_Helper's value 'extrapolate-gammas' instead"*OR* |
| rhs_boundary_condition | "scalar" | Boundary condition for BSSN RHS and some of the ADMBase variables |   "scalar" :: "not recommended; use ML_BSSN_Helper's option 'NewRad' instead"*OR* |
| rhs_evaluation | "splitBy" | Whether and how the RHS routine should be split to improve performance |   "combined" :: "use a single routine (probably slow)"*OR*  "splitBy" :: "split into 3 routines via Kranc"*OR* |
| my_initial_data | "default" | (OUTDATED) |   "ADMBase" :: "from ADMBase"*OR*  "default" :: "do nothing"*OR* |
| my_initial_boundary_condition | "default" | (OUTDATED) |   "none" :: "none"*OR*  "default" :: "do nothing"*OR* |
| my_rhs_boundary_condition | "default" | (OUTDATED) |   "none" :: "none"*OR*  "static" :: "static"*OR*  "default" :: "do nothing"*OR* |
| my_boundary_condition | "default" | (OUTDATED) |   "none" :: "none"*OR*  "Minkowski" :: "Minkowski"*OR*  "default" :: "do nothing"*OR* |
| dt_lapse_shift_method | "default" | (OUTDATED) Treatment of ADMBase dtlapse and dtshift |   "correct" :: "(unused)"*OR*  "noLapseShiftAdvection" :: "(unused)"*OR*  "default" :: "do nothing"*OR* |
| apply_dissipation | "default" | (OUTDATED) Whether to apply dissipation to the RHSs |   "always" :: "yes"*OR*  "never" :: "no"*OR*  "default" :: "do nothing"*OR* |
| ML_BSSN_MaxNumEvolvedVars | 25 | Number of evolved variables used by this thorn |   25:25 :: "Number of evolved variables used by this thorn"*OR* |
| ML_BSSN_MaxNumArrayEvolvedVars | 0 | Number of Array evolved variables used by this thorn |   0:0 :: "Number of Array evolved variables used by this thorn"*OR* |
| timelevels | 3 | Number of active timelevels |   0:4 :: ""*OR* |
| rhs_timelevels | 1 | Number of active RHS timelevels |   0:4 :: ""*OR* |
| ML_BSSN_InitialADMBase1Everywhere_calc_every | 1 | ML_BSSN_InitialADMBase1Everywhere_calc_every |   *:* :: ""*OR* |
| ML_BSSN_InitialADMBase2Interior_calc_every | 1 | ML_BSSN_InitialADMBase2Interior_calc_every |   *:* :: ""*OR* |
| ML_BSSN_InitialADMBase2BoundaryScalar_calc_every | 1 | ML_BSSN_InitialADMBase2BoundaryScalar_calc_every |   *:* :: ""*OR* |
| ML_BSSN_EnforceEverywhere_calc_every | 1 | ML_BSSN_EnforceEverywhere_calc_every |   *:* :: ""*OR* |
| ML_BSSN_ADMBaseEverywhere_calc_every | 1 | ML_BSSN_ADMBaseEverywhere_calc_every |   *:* :: ""*OR* |
| ML_BSSN_ADMBaseInterior_calc_every | 1 | ML_BSSN_ADMBaseInterior_calc_every |   *:* :: ""*OR* |
| ML_BSSN_ADMBaseBoundaryScalar_calc_every | 1 | ML_BSSN_ADMBaseBoundaryScalar_calc_every |   *:* :: ""*OR* |
| ML_BSSN_EvolutionInterior_calc_every | 1 | ML_BSSN_EvolutionInterior_calc_every |   *:* :: ""*OR* |
| ML_BSSN_EvolutionInteriorSplitBy1_calc_every | 1 | ML_BSSN_EvolutionInteriorSplitBy1_calc_every |   *:* :: ""*OR* |
| ML_BSSN_EvolutionInteriorSplitBy2_calc_every | 1 | ML_BSSN_EvolutionInteriorSplitBy2_calc_every |   *:* :: ""*OR* |
| ML_BSSN_EvolutionInteriorSplitBy3_calc_every | 1 | ML_BSSN_EvolutionInteriorSplitBy3_calc_every |   *:* :: ""*OR* |
| ML_BSSN_EvolutionBoundaryScalar_calc_every | 1 | ML_BSSN_EvolutionBoundaryScalar_calc_every |   *:* :: ""*OR* |
| ML_BSSN_EvolutionAnalysisInit_calc_every | 1 | ML_BSSN_EvolutionAnalysisInit_calc_every |   *:* :: ""*OR* |
| ML_BSSN_EvolutionAnalysisInterior_calc_every | 1 | ML_BSSN_EvolutionAnalysisInterior_calc_every |   *:* :: ""*OR* |
| ML_BSSN_ConstraintsEverywhere_calc_every | 1 | ML_BSSN_ConstraintsEverywhere_calc_every |   *:* :: ""*OR* |
| ML_BSSN_ConstraintsInterior_calc_every | 1 | ML_BSSN_ConstraintsInterior_calc_every |   *:* :: ""*OR* |
| ML_BSSN_InitialADMBase1Everywhere_calc_offset | 0 | ML_BSSN_InitialADMBase1Everywhere_calc_offset |   *:* :: ""*OR* |
| ML_BSSN_InitialADMBase2Interior_calc_offset | 0 | ML_BSSN_InitialADMBase2Interior_calc_offset |   *:* :: ""*OR* |
| ML_BSSN_InitialADMBase2BoundaryScalar_calc_offset | 0 | ML_BSSN_InitialADMBase2BoundaryScalar_calc_offset |   *:* :: ""*OR* |
| ML_BSSN_EnforceEverywhere_calc_offset | 0 | ML_BSSN_EnforceEverywhere_calc_offset |   *:* :: ""*OR* |
| ML_BSSN_ADMBaseEverywhere_calc_offset | 0 | ML_BSSN_ADMBaseEverywhere_calc_offset |   *:* :: ""*OR* |
| ML_BSSN_ADMBaseInterior_calc_offset | 0 | ML_BSSN_ADMBaseInterior_calc_offset |   *:* :: ""*OR* |
| ML_BSSN_ADMBaseBoundaryScalar_calc_offset | 0 | ML_BSSN_ADMBaseBoundaryScalar_calc_offset |   *:* :: ""*OR* |
| ML_BSSN_EvolutionInterior_calc_offset | 0 | ML_BSSN_EvolutionInterior_calc_offset |   *:* :: ""*OR* |
| ML_BSSN_EvolutionInteriorSplitBy1_calc_offset | 0 | ML_BSSN_EvolutionInteriorSplitBy1_calc_offset |   *:* :: ""*OR* |
| ML_BSSN_EvolutionInteriorSplitBy2_calc_offset | 0 | ML_BSSN_EvolutionInteriorSplitBy2_calc_offset |   *:* :: ""*OR* |
| ML_BSSN_EvolutionInteriorSplitBy3_calc_offset | 0 | ML_BSSN_EvolutionInteriorSplitBy3_calc_offset |   *:* :: ""*OR* |
| ML_BSSN_EvolutionBoundaryScalar_calc_offset | 0 | ML_BSSN_EvolutionBoundaryScalar_calc_offset |   *:* :: ""*OR* |
| ML_BSSN_EvolutionAnalysisInit_calc_offset | 0 | ML_BSSN_EvolutionAnalysisInit_calc_offset |   *:* :: ""*OR* |
| ML_BSSN_EvolutionAnalysisInterior_calc_offset | 0 | ML_BSSN_EvolutionAnalysisInterior_calc_offset |   *:* :: ""*OR* |
| ML_BSSN_ConstraintsEverywhere_calc_offset | 0 | ML_BSSN_ConstraintsEverywhere_calc_offset |   *:* :: ""*OR* |
| ML_BSSN_ConstraintsInterior_calc_offset | 0 | ML_BSSN_ConstraintsInterior_calc_offset |   *:* :: ""*OR* |
| phi_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| gt11_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| gt12_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| gt13_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| gt22_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| gt23_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| gt33_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| Xt1_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| Xt2_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| Xt3_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| trK_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| At11_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| At12_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| At13_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| At22_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| At23_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| At33_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| alpha_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| A_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| beta1_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| beta2_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| beta3_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| B1_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| B2_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| B3_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| ML_log_confac_bound | "none" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| ML_metric_bound | "none" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| ML_Gamma_bound | "none" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| ML_trace_curv_bound | "none" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| ML_curv_bound | "none" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| ML_lapse_bound | "none" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| ML_dtlapse_bound | "none" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| ML_shift_bound | "none" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| ML_dtshift_bound | "none" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| phi_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| gt11_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| gt12_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| gt13_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| gt22_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| gt23_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| gt33_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| Xt1_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| Xt2_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| Xt3_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| trK_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| At11_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| At12_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| At13_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| At22_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| At23_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| At33_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| alpha_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| A_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| beta1_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| beta2_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| beta3_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| B1_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| B2_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| B3_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| ML_log_confac_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| ML_metric_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| ML_Gamma_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| ML_trace_curv_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| ML_curv_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| ML_lapse_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| ML_dtlapse_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| ML_shift_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| ML_dtshift_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| phi_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| gt11_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| gt12_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| gt13_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| gt22_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| gt23_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| gt33_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| Xt1_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| Xt2_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| Xt3_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| trK_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| At11_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| At12_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| At13_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| At22_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| At23_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| At33_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| alpha_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| A_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| beta1_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| beta2_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| beta3_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| B1_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| B2_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| B3_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| ML_log_confac_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| ML_metric_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| ML_Gamma_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| ML_trace_curv_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| ML_curv_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| ML_lapse_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| ML_dtlapse_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| ML_shift_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| ML_dtshift_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| phi_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| gt11_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| gt12_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| gt13_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| gt22_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| gt23_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| gt33_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| Xt1_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| Xt2_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| Xt3_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| trK_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| At11_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| At12_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| At13_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| At22_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| At23_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| At33_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| alpha_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| A_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| beta1_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| beta2_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| beta3_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| B1_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| B2_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| B3_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| ML_log_confac_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| ML_metric_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| ML_Gamma_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| ML_trace_curv_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| ML_curv_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| ML_lapse_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| ML_dtlapse_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| ML_shift_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| ML_dtshift_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |


### ML_ADMConstraints

| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | 0 | verbose |   *:* :: ""*OR* |
| other_timelevels | 1 | Number of active timelevels for non-evolved grid functions |   0:3 :: ""*OR* |
| tile_size | -1 | Loop tile size |   *:* :: ""*OR* |
| ML_ADMConstraints_MaxNumEvolvedVars | 0 | Number of evolved variables used by this thorn |   0:0 :: "Number of evolved variables used by this thorn"*OR* |
| ML_ADMConstraints_MaxNumArrayEvolvedVars | 0 | Number of Array evolved variables used by this thorn |   0:0 :: "Number of Array evolved variables used by this thorn"*OR* |
| timelevels | 3 | Number of active timelevels |   0:3 :: ""*OR* |
| rhs_timelevels | 1 | Number of active RHS timelevels |   0:3 :: ""*OR* |
| ML_ADMConstraints_evaluate_calc_every | 1 | ML_ADMConstraints_evaluate_calc_every |   *:* :: ""*OR* |
| ML_ADMConstraints_evaluate_calc_offset | 0 | ML_ADMConstraints_evaluate_calc_offset |   *:* :: ""*OR* |


### ML_CCZ4

| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| evolution_method |  | evolution_method |   "ML_CCZ4" :: ""*OR* |
| lapse_evolution_method |  | lapse_evolution_method |   "ML_CCZ4" :: ""*OR* |
| shift_evolution_method |  | shift_evolution_method |   "ML_CCZ4" :: ""*OR* |
| dtlapse_evolution_method |  | dtlapse_evolution_method |   "ML_CCZ4" :: ""*OR* |
| dtshift_evolution_method |  | dtshift_evolution_method |   "ML_CCZ4" :: ""*OR* |
| verbose | 0 | verbose |   *:* :: ""*OR* |
| other_timelevels | 1 | Number of active timelevels for non-evolved grid functions |   0:4 :: ""*OR* |
| GammaShift | 0.5 | CCZ4 dovariant shift term in Gamma |   *:* :: ""*OR* |
| dampk1 | 0 | CCZ4 damping term 1 for Theta and Z |   *:* :: ""*OR* |
| dampk2 | 0 | CCZ4 damping term 2 for Theta and Z |   *:* :: ""*OR* |
| harmonicF | 1 | d/dt alpha = - f alpha^n K   (harmonic: f=1, 1+log: f=2) |   *:* :: ""*OR* |
| alphaDriver | 0 | d/dt alpha = ... - alphaDriver (alpha - 1)   (use 1/M (?)) |   *:* :: ""*OR* |
| shiftGammaCoeff | 0 | d/dt beta^i = C Xt^i   (use C=0.75/M) |   *:* :: ""*OR* |
| betaDriver | 0 | d/dt beta^i = ... - betaDriver alpha^shiftAlphaPower beta^i   (use 1/M (?)) |   *:* :: ""*OR* |
| shiftAlphaPower | 0 | d/dt beta^i = ... - betaDriver alpha^shiftAlphaPower beta^i   (use 0 (?)) |   *:* :: ""*OR* |
| spatialBetaDriverRadius | 1000000000000 | Radius at which the betaDriver starts to be reduced |   (0:* :: "positive"*OR* |
| spatialShiftGammaCoeffRadius | 1000000000000 | Radius at which shiftGammaCoeff starts to be reduced |   (0:* :: "positive"*OR* |
| minimumLapse | 0 | Enforced minimum of the lapse function |   0:* :: "non-negative"*OR* |
| epsDiss | 0 | Dissipation strength |   0:* :: "non-negative"*OR* |
| LapseACoeff | -1. | (OUTDATED) Evolve time derivative of lapse A? (now evolveA) |   0. :: "off"*OR*  1. :: "on"*OR*  -1. :: "default"*OR* |
| ShiftBCoeff | -1. | (OUTDATED) Evolve time derivative of shift B^i? (now evolveB) |   0. :: "off"*OR*  1. :: "on"*OR*  -1. :: "default"*OR* |
| LapseAdvectionCoeff | -1. | (OUTDATED) Advect lapse? (now advectLapse) |   0. :: "off"*OR*  1. :: "on"*OR*  -1. :: "default"*OR* |
| ShiftAdvectionCoeff | -1. | (OUTDATED) Advect shift? (now advectShift) |   0. :: "off"*OR*  1. :: "on"*OR*  -1. :: "default"*OR* |
| fdOrder | 4 | Finite differencing order |   2 :: ""*OR*  4 :: ""*OR*  6 :: ""*OR*  8 :: ""*OR* |
| conformalMethod | 1 | Treatment of conformal factor |   1 :: "W method"*OR* |
| evolveA | 0 | Evolve time derivative of lapse A? (former LapseACoeff) |   0 :: "off"*OR*  1 :: "on"*OR* |
| evolveB | 1 | Evolve time derivative of shift B^i? (former ShiftBCoeff) |   0 :: "off"*OR*  1 :: "on"*OR* |
| harmonicN | 2 | d/dt alpha = - f alpha^n K  (harmonic: n=2, 1+log: n=1) |   *:* :: ""*OR* |
| shiftFormulation | 0 | shift formulation |   0 :: "Gamma driver"*OR*  1 :: "harmonic"*OR* |
| useSpatialBetaDriver | 0 | Enable spatially varying betaDriver |   0 :: "off"*OR*  1 :: "on"*OR* |
| useSpatialShiftGammaCoeff | 0 | Enable spatially varying shiftGammaCoeff |   0 :: "off"*OR*  1 :: "on"*OR* |
| advectLapse | 1 | Advect lapse? (former LapseAdvectionCoeff) |   0 :: "off"*OR*  1 :: "on"*OR* |
| advectShift | 1 | Advect shift? (former ShiftAdvectionCoeff) |   0 :: "off"*OR*  1 :: "on"*OR* |
| fixAdvectionTerms | 0 | Modify driver and advection terms to work better? |   0 :: "off"*OR*  1 :: "on"*OR* |
| tile_size | -1 | Loop tile size |   *:* :: ""*OR* |
| initial_boundary_condition | "scalar" | Boundary condition for initial condition for some of the BSSN variables |   "scalar" :: "not recommended; use ML_BSSN_Helper's value 'extrapolate-gammas' instead"*OR* |
| rhs_boundary_condition | "scalar" | Boundary condition for BSSN RHS and some of the ADMBase variables |   "scalar" :: "not recommended; use ML_BSSN_Helper's option 'NewRad' instead"*OR* |
| rhs_evaluation | "splitBy" | Whether and how the RHS routine should be split to improve performance |   "combined" :: "use a single routine (probably slow)"*OR*  "splitBy" :: "split into 3 routines via Kranc"*OR* |
| my_initial_data | "default" | (OUTDATED) |   "ADMBase" :: "from ADMBase"*OR*  "default" :: "do nothing"*OR* |
| my_initial_boundary_condition | "default" | (OUTDATED) |   "none" :: "none"*OR*  "default" :: "do nothing"*OR* |
| my_rhs_boundary_condition | "default" | (OUTDATED) |   "none" :: "none"*OR*  "static" :: "static"*OR*  "default" :: "do nothing"*OR* |
| my_boundary_condition | "default" | (OUTDATED) |   "none" :: "none"*OR*  "Minkowski" :: "Minkowski"*OR*  "default" :: "do nothing"*OR* |
| dt_lapse_shift_method | "default" | (OUTDATED) Treatment of ADMBase dtlapse and dtshift |   "correct" :: "(unused)"*OR*  "noLapseShiftAdvection" :: "(unused)"*OR*  "default" :: "do nothing"*OR* |
| apply_dissipation | "default" | (OUTDATED) Whether to apply dissipation to the RHSs |   "always" :: "yes"*OR*  "never" :: "no"*OR*  "default" :: "do nothing"*OR* |
| ML_CCZ4_MaxNumEvolvedVars | 26 | Number of evolved variables used by this thorn |   26:26 :: "Number of evolved variables used by this thorn"*OR* |
| ML_CCZ4_MaxNumArrayEvolvedVars | 0 | Number of Array evolved variables used by this thorn |   0:0 :: "Number of Array evolved variables used by this thorn"*OR* |
| timelevels | 3 | Number of active timelevels |   0:4 :: ""*OR* |
| rhs_timelevels | 1 | Number of active RHS timelevels |   0:4 :: ""*OR* |
| ML_CCZ4_InitialADMBase1Everywhere_calc_every | 1 | ML_CCZ4_InitialADMBase1Everywhere_calc_every |   *:* :: ""*OR* |
| ML_CCZ4_InitialADMBase2Interior_calc_every | 1 | ML_CCZ4_InitialADMBase2Interior_calc_every |   *:* :: ""*OR* |
| ML_CCZ4_InitialADMBase2BoundaryScalar_calc_every | 1 | ML_CCZ4_InitialADMBase2BoundaryScalar_calc_every |   *:* :: ""*OR* |
| ML_CCZ4_EnforceEverywhere_calc_every | 1 | ML_CCZ4_EnforceEverywhere_calc_every |   *:* :: ""*OR* |
| ML_CCZ4_ADMBaseEverywhere_calc_every | 1 | ML_CCZ4_ADMBaseEverywhere_calc_every |   *:* :: ""*OR* |
| ML_CCZ4_ADMBaseInterior_calc_every | 1 | ML_CCZ4_ADMBaseInterior_calc_every |   *:* :: ""*OR* |
| ML_CCZ4_ADMBaseBoundaryScalar_calc_every | 1 | ML_CCZ4_ADMBaseBoundaryScalar_calc_every |   *:* :: ""*OR* |
| ML_CCZ4_EvolutionInterior_calc_every | 1 | ML_CCZ4_EvolutionInterior_calc_every |   *:* :: ""*OR* |
| ML_CCZ4_EvolutionInteriorSplitBy1_calc_every | 1 | ML_CCZ4_EvolutionInteriorSplitBy1_calc_every |   *:* :: ""*OR* |
| ML_CCZ4_EvolutionInteriorSplitBy2_calc_every | 1 | ML_CCZ4_EvolutionInteriorSplitBy2_calc_every |   *:* :: ""*OR* |
| ML_CCZ4_EvolutionInteriorSplitBy3_calc_every | 1 | ML_CCZ4_EvolutionInteriorSplitBy3_calc_every |   *:* :: ""*OR* |
| ML_CCZ4_EvolutionBoundaryScalar_calc_every | 1 | ML_CCZ4_EvolutionBoundaryScalar_calc_every |   *:* :: ""*OR* |
| ML_CCZ4_EvolutionAnalysisInit_calc_every | 1 | ML_CCZ4_EvolutionAnalysisInit_calc_every |   *:* :: ""*OR* |
| ML_CCZ4_EvolutionAnalysisInterior_calc_every | 1 | ML_CCZ4_EvolutionAnalysisInterior_calc_every |   *:* :: ""*OR* |
| ML_CCZ4_ConstraintsEverywhere_calc_every | 1 | ML_CCZ4_ConstraintsEverywhere_calc_every |   *:* :: ""*OR* |
| ML_CCZ4_ConstraintsInterior_calc_every | 1 | ML_CCZ4_ConstraintsInterior_calc_every |   *:* :: ""*OR* |
| ML_CCZ4_InitialADMBase1Everywhere_calc_offset | 0 | ML_CCZ4_InitialADMBase1Everywhere_calc_offset |   *:* :: ""*OR* |
| ML_CCZ4_InitialADMBase2Interior_calc_offset | 0 | ML_CCZ4_InitialADMBase2Interior_calc_offset |   *:* :: ""*OR* |
| ML_CCZ4_InitialADMBase2BoundaryScalar_calc_offset | 0 | ML_CCZ4_InitialADMBase2BoundaryScalar_calc_offset |   *:* :: ""*OR* |
| ML_CCZ4_EnforceEverywhere_calc_offset | 0 | ML_CCZ4_EnforceEverywhere_calc_offset |   *:* :: ""*OR* |
| ML_CCZ4_ADMBaseEverywhere_calc_offset | 0 | ML_CCZ4_ADMBaseEverywhere_calc_offset |   *:* :: ""*OR* |
| ML_CCZ4_ADMBaseInterior_calc_offset | 0 | ML_CCZ4_ADMBaseInterior_calc_offset |   *:* :: ""*OR* |
| ML_CCZ4_ADMBaseBoundaryScalar_calc_offset | 0 | ML_CCZ4_ADMBaseBoundaryScalar_calc_offset |   *:* :: ""*OR* |
| ML_CCZ4_EvolutionInterior_calc_offset | 0 | ML_CCZ4_EvolutionInterior_calc_offset |   *:* :: ""*OR* |
| ML_CCZ4_EvolutionInteriorSplitBy1_calc_offset | 0 | ML_CCZ4_EvolutionInteriorSplitBy1_calc_offset |   *:* :: ""*OR* |
| ML_CCZ4_EvolutionInteriorSplitBy2_calc_offset | 0 | ML_CCZ4_EvolutionInteriorSplitBy2_calc_offset |   *:* :: ""*OR* |
| ML_CCZ4_EvolutionInteriorSplitBy3_calc_offset | 0 | ML_CCZ4_EvolutionInteriorSplitBy3_calc_offset |   *:* :: ""*OR* |
| ML_CCZ4_EvolutionBoundaryScalar_calc_offset | 0 | ML_CCZ4_EvolutionBoundaryScalar_calc_offset |   *:* :: ""*OR* |
| ML_CCZ4_EvolutionAnalysisInit_calc_offset | 0 | ML_CCZ4_EvolutionAnalysisInit_calc_offset |   *:* :: ""*OR* |
| ML_CCZ4_EvolutionAnalysisInterior_calc_offset | 0 | ML_CCZ4_EvolutionAnalysisInterior_calc_offset |   *:* :: ""*OR* |
| ML_CCZ4_ConstraintsEverywhere_calc_offset | 0 | ML_CCZ4_ConstraintsEverywhere_calc_offset |   *:* :: ""*OR* |
| ML_CCZ4_ConstraintsInterior_calc_offset | 0 | ML_CCZ4_ConstraintsInterior_calc_offset |   *:* :: ""*OR* |
| phi_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| gt11_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| gt12_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| gt13_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| gt22_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| gt23_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| gt33_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| Xt1_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| Xt2_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| Xt3_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| trK_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| At11_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| At12_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| At13_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| At22_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| At23_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| At33_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| Theta_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| alpha_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| A_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| beta1_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| beta2_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| beta3_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| B1_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| B2_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| B3_bound | "skip" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| ML_log_confac_bound | "none" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| ML_metric_bound | "none" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| ML_Gamma_bound | "none" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| ML_trace_curv_bound | "none" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| ML_curv_bound | "none" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| ML_Theta_bound | "none" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| ML_lapse_bound | "none" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| ML_dtlapse_bound | "none" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| ML_shift_bound | "none" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| ML_dtshift_bound | "none" | Boundary condition to implement |   "flat" :: "Flat boundary condition"*OR*  "none" :: "No boundary condition"*OR*  "static" :: "Boundaries held fixed"*OR*  "radiative" :: "Radiation boundary condition"*OR*  "scalar" :: "Dirichlet boundary condition"*OR*  "newrad" :: "Improved radiative boundary condition"*OR*  "skip" :: "skip boundary condition code"*OR* |
| phi_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| gt11_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| gt12_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| gt13_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| gt22_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| gt23_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| gt33_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| Xt1_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| Xt2_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| Xt3_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| trK_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| At11_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| At12_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| At13_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| At22_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| At23_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| At33_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| Theta_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| alpha_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| A_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| beta1_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| beta2_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| beta3_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| B1_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| B2_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| B3_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| ML_log_confac_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| ML_metric_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| ML_Gamma_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| ML_trace_curv_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| ML_curv_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| ML_Theta_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| ML_lapse_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| ML_dtlapse_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| ML_shift_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| ML_dtshift_bound_speed | 1. | characteristic speed at boundary |   0:* :: "outgoing characteristic speed > 0"*OR* |
| phi_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| gt11_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| gt12_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| gt13_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| gt22_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| gt23_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| gt33_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| Xt1_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| Xt2_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| Xt3_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| trK_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| At11_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| At12_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| At13_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| At22_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| At23_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| At33_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| Theta_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| alpha_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| A_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| beta1_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| beta2_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| beta3_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| B1_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| B2_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| B3_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| ML_log_confac_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| ML_metric_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| ML_Gamma_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| ML_trace_curv_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| ML_curv_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| ML_Theta_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| ML_lapse_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| ML_dtlapse_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| ML_shift_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| ML_dtshift_bound_limit | 0. | limit value for r -> infinity |   *:* :: "value of limit value is unrestricted"*OR* |
| phi_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| gt11_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| gt12_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| gt13_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| gt22_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| gt23_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| gt33_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| Xt1_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| Xt2_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| Xt3_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| trK_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| At11_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| At12_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| At13_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| At22_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| At23_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| At33_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| Theta_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| alpha_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| A_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| beta1_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| beta2_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| beta3_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| B1_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| B2_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| B3_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| ML_log_confac_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| ML_metric_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| ML_Gamma_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| ML_trace_curv_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| ML_curv_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| ML_Theta_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| ML_lapse_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| ML_dtlapse_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| ML_shift_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |
| ML_dtshift_bound_scalar | 0. | Dirichlet boundary value |   *:* :: "unrestricted"*OR* |


### m

### ML_BSSN_Test

| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### par

### ML_ADMQuantities

| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | 0 | verbose |   *:* :: ""*OR* |
| other_timelevels | 1 | Number of active timelevels for non-evolved grid functions |   0:3 :: ""*OR* |
| conformalMethod | 0 | Treatment of conformal factor |   0 :: "phi method"*OR*  1 :: "W method"*OR* |
| tile_size | -1 | Loop tile size |   *:* :: ""*OR* |
| ML_ADMQuantities_MaxNumEvolvedVars | 0 | Number of evolved variables used by this thorn |   0:0 :: "Number of evolved variables used by this thorn"*OR* |
| ML_ADMQuantities_MaxNumArrayEvolvedVars | 0 | Number of Array evolved variables used by this thorn |   0:0 :: "Number of Array evolved variables used by this thorn"*OR* |
| timelevels | 3 | Number of active timelevels |   0:3 :: ""*OR* |
| rhs_timelevels | 1 | Number of active RHS timelevels |   0:3 :: ""*OR* |
| ML_ADMQuantities_calculate_calc_every | 1 | ML_ADMQuantities_calculate_calc_every |   *:* :: ""*OR* |
| ML_ADMQuantities_calculate_calc_offset | 0 | ML_ADMQuantities_calculate_calc_offset |   *:* :: ""*OR* |


## PITTNullCode

### SphericalHarmonicRecon

CVS info   : $Header:$

Cactus Code Thorn SphericalHarmonicRecon
Thorn Author(s)     : Yosef Zlochower <yosef@astro.rit.edu>
Thorn Maintainer(s) : Yosef Zlochower <yosef@astro.rit.edu>
--------------------------------------------------------------------------

Purpose of the thorn:

This code is available under the  GPL General Public License, 
version 2, or later (see GPLv2 file included).

The code in this arrangement is a results of many years of work by
many different authors. Major contributors to this code include
(in alphabetical order)

M.C. Babiuc
N. T. Bishop
R. Gomez
R. A. Isaacson
L. Lehner
P. Papadopoulos
C. Reisswig
B. Szilagyi
J. Welling
J. Winicour
Y. Zlochower


If you use this code, then we request that you cite the following papers:

Title: Characteristic Extraction Tool for Gravitational Waveforms
 Authors: M.C. Babiuc, B. Szilagyi, J. Winicour, Y. Zlochower
 arXiv:1011.4223, Phys Rev D 84, 044057,
 doi: http://dx.doi.org/10.1103/PhysRevD.84.044057

Title: Cauchy-characteristic matching
 Authors: N. Bishop, R. Isaacson, R. Gomez, L. Lehner, B. Szilagyi, J.
 Winicour arXiv: gr-qc/9801070
 In "Black Holes, Gravitational Radiation and the Universe"
 eds. B. Iyer and B. Bhawal (Kluwer, Dordrecht, Boston,1999)

This code is maintained by

M. C.Babiuc <babiuc@marshall.edu>
B. Szilagyi <bela.szilagyi@jpl.nasa.gov>
C. Reisswig <reisswig@tapir.caltech.edu>
Y. Zlochower <yosef@astro.rit.edu>

Please contact the maintainers of the code if you require
write access.



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| order | 4 | order used in time interpolation / differentiation |   2 :: "two or four"*OR*  4 :: "two or four"*OR* |
| r_extract | 50.0 | the radius of the worldtube |   (0:* :: "positive"*OR* |
| metric_data_filename | "metric_Decomp.h5" | filename for the cauchy metric data |  ".*"	:: "the Cauchy metric data"*OR* |
| time_derivative_in_file | "no" | is the time derivative of the metric in the cauchy file |  |
| override_extraction_parameters | no | never set this |  |
| override_spin | 0 | spin: never set this |  *:* :: "anything, but you probably want 0"*OR* |
| override_nn | 1 | number of Chabyshev coefficition: never use this |   1:* :: "positive: must actualy match the extraction run"*OR* |
| override_na | 1 | number of angular coefficition: never use this |   1:* :: "positive: must actualy match the extraction run"*OR* |
| override_Rin | 1 | inner radius of extraction zone: ... |   (0:* :: "positive"*OR* |
| override_Rout | 1 | outer radius of extraction zone: ... |   (0:* :: "positive"*OR* |


### NullConstr

CVS info   : $Header$

Cactus Code Thorn NullConstr
Thorn Author(s)     : Bela Szilagyi <bela.szilagyi@jpl.nasa.gov>
                    : N T Bishop <bishont@unisa.ac.za>
                    : Chi Wai Lai <laicw@astro.unisa.ac.za>
Thorn Maintainer(s) : Bela Szilagyi <bela.szilagyi@jpl.nasa.gov>
                    : N T Bishop <bishont@unisa.ac.za>
                    : Chi Wai Lai <laicw@astro.unisa.ac.za>
--------------------------------------------------------------------------

Purpose of the thorn:

This code is available under the  GPL General Public License, 
version 2, or later (see GPLv2 file included).

The code in this arrangement is a results of many years of work by
many different authors. Major contributors to this code include
(in alphabetical order)

M.C. Babiuc
N. T. Bishop
R. Gomez
R. A. Isaacson
L. Lehner
P. Papadopoulos
C. Reisswig
B. Szilagyi
J. Welling
J. Winicour
Y. Zlochower


If you use this code, then we request that you cite the following papers:

Title: Characteristic Extraction Tool for Gravitational Waveforms
 Authors: M.C. Babiuc, B. Szilagyi, J. Winicour, Y. Zlochower
 arXiv:1011.4223, Phys Rev D 84, 044057, 
 doi: http://dx.doi.org/10.1103/PhysRevD.84.044057

Title: Cauchy-characteristic matching
 Authors: N. Bishop, R. Isaacson, R. Gomez, L. Lehner, B. Szilagyi, J.
 Winicour arXiv: gr-qc/9801070
 In "Black Holes, Gravitational Radiation and the Universe"
 eds. B. Iyer and B. Bhawal (Kluwer, Dordrecht, Boston,1999)

This code is maintained by

M. C.Babiuc <babiuc@marshall.edu>
B. Szilagyi <bela.szilagyi@jpl.nasa.gov>
C. Reisswig <reisswig@tapir.caltech.edu>
Y. Zlochower <yosef@astro.rit.edu>

Please contact the maintainers of the code if you require
write access.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### NullGrid

Purpose of the thorn:

This thorn demonstrates the use of the Cactus interpolation API.
This code is available under the  GPL General Public License, 
version 2, or later (see GPLv2 file included).

The code in this arrangement is a results of many years of work by
many different authors. Major contributors to this code include
(in alphabetical order)

M.C. Babiuc
N. T. Bishop
R. Gomez
R. A. Isaacson
L. Lehner
P. Papadopoulos
C. Reisswig
B. Szilagyi
J. Welling
J. Winicour
Y. Zlochower


If you use this code, then we request that you cite the following papers:

Title: Characteristic Extraction Tool for Gravitational Waveforms
 Authors: M.C. Babiuc, B. Szilagyi, J. Winicour, Y. Zlochower
 arXiv:1011.4223, Phys Rev D 84, 044057,
 doi: http://dx.doi.org/10.1103/PhysRevD.84.044057

Title: Cauchy-characteristic matching
 Authors: N. Bishop, R. Isaacson, R. Gomez, L. Lehner, B. Szilagyi, J.
 Winicour arXiv: gr-qc/9801070
 In "Black Holes, Gravitational Radiation and the Universe"
 eds. B. Iyer and B. Bhawal (Kluwer, Dordrecht, Boston,1999)

This code is maintained by

M. C.Babiuc <babiuc@marshall.edu>
B. Szilagyi <bela.szilagyi@jpl.nasa.gov>
C. Reisswig <reisswig@tapir.caltech.edu>
Y. Zlochower <yosef@astro.rit.edu>

Please contact the maintainers of the code if you require
write access.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | 0 | Should this thorn be verbose or not |    0:1  :: "0 or 1"*OR* |
| null_xin | 41 | Innermost compactified radial point | 0.0:*::  "positive real number"*OR*}0.5*OR**OR*CCTK_REAL null_rwt "Compactification Parameter"*OR*{*OR*0.0:*::  "positive real number"*OR*}1.0*OR**OR*CCTK_INT N_radial_pts "radial gridsize"*OR*{*OR*  4:*:: "pos"*OR*}21*OR**OR*####################################*OR*### ANGULAR GRID*OR*####################################*OR**OR*int N_ang_pts_inside_eq "nr of points inside equator, spanning the diameter of the unit circle"*OR*{*OR*  2:*	:: "larger than one"*OR* |


### NullPsiInt

Cactus Code Thorn NullPsi4IO
Author(s)    : Maria Babiuc <babiuc@marshall.edu>
Maintainer(s): Maria Babiuc <babiuc@marshall.edu>
Licence      : N
--------------------------------------------------------------------------

1. Purpose

not documented
This code is available under the  GPL General Public License, 
version 2, or later (see GPLv2 file included).

The code in this arrangement is a results of many years of work by
many different authors. Major contributors to this code include
(in alphabetical order)

M.C. Babiuc
N. T. Bishop
R. Gomez
R. A. Isaacson
L. Lehner
P. Papadopoulos
C. Reisswig
B. Szilagyi
J. Welling
J. Winicour
Y. Zlochower


If you use this code, then we request that you cite the following papers:

Title: Characteristic Extraction Tool for Gravitational Waveforms
 Authors: M.C. Babiuc, B. Szilagyi, J. Winicour, Y. Zlochower
 arXiv:1011.4223, Phys Rev D 84, 044057,
 doi: http://dx.doi.org/10.1103/PhysRevD.84.044057

Title: Cauchy-characteristic matching
 Authors: N. Bishop, R. Isaacson, R. Gomez, L. Lehner, B. Szilagyi, J.
 Winicour arXiv: gr-qc/9801070
 In "Black Holes, Gravitational Radiation and the Universe"
 eds. B. Iyer and B. Bhawal (Kluwer, Dordrecht, Boston,1999)

This code is maintained by

M. C.Babiuc <babiuc@marshall.edu>
B. Szilagyi <bela.szilagyi@jpl.nasa.gov>
C. Reisswig <reisswig@tapir.caltech.edu>
Y. Zlochower <yosef@astro.rit.edu>

Please contact the maintainers of the code if you require
write access.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### NullDecomp

CVS info   : $Header$

Cactus Code Thorn NullDecomp
Thorn Author(s)     : Yosef Zlochower <yosef@phys.utb.edu>
Thorn Maintainer(s) : Yosef Zlochower <yosef@phys.utb.edu>
--------------------------------------------------------------------------

Purpose of the thorn:

This code is available under the  GPL General Public License, 
version 2, or later (see GPLv2 file included).

The code in this arrangement is a results of many years of work by
many different authors. Major contributors to this code include
(in alphabetical order)

M.C. Babiuc
N. T. Bishop
R. Gomez
R. A. Isaacson
L. Lehner
P. Papadopoulos
C. Reisswig
B. Szilagyi
J. Welling
J. Winicour
Y. Zlochower


If you use this code, then we request that you cite the following papers:

Title: Characteristic Extraction Tool for Gravitational Waveforms
 Authors: M.C. Babiuc, B. Szilagyi, J. Winicour, Y. Zlochower
 arXiv:1011.4223, Phys Rev D 84, 044057, 
 doi: http://dx.doi.org/10.1103/PhysRevD.84.044057

Title: Cauchy-characteristic matching
 Authors: N. Bishop, R. Isaacson, R. Gomez, L. Lehner, B. Szilagyi, J.
 Winicour arXiv: gr-qc/9801070
 In "Black Holes, Gravitational Radiation and the Universe"
 eds. B. Iyer and B. Bhawal (Kluwer, Dordrecht, Boston,1999)

This code is maintained by

M. C.Babiuc <babiuc@marshall.edu>
B. Szilagyi <bela.szilagyi@jpl.nasa.gov>
C. Reisswig <reisswig@tapir.caltech.edu>
Y. Zlochower <yosef@astro.rit.edu>

Please contact the maintainers of the code if you require
write access.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| l_max | 9 | Decompose into spherical harmonics \ell = spin up to \ell = l_max |   2: :: "2 or greater "*OR* |
| use_rsYlm | "no" | use the rsYlm's rather than the standard sYlm's |  |
| store_ylms | "yes" | should the Ylm's be stored rather than recomputed each time? |  |


### NullNews

This code is available under the  GPL General Public License, 
version 2, or later (see GPLv2 file included).

The code in this arrangement is a results of many years of work by
many different authors. Major contributors to this code include
(in alphabetical order)

M.C. Babiuc
N. T. Bishop
R. Gomez
R. A. Isaacson
L. Lehner
P. Papadopoulos
C. Reisswig
B. Szilagyi
J. Welling
J. Winicour
Y. Zlochower


If you use this code, then we request that you cite the following papers:

Title: Characteristic Extraction Tool for Gravitational Waveforms
 Authors: M.C. Babiuc, B. Szilagyi, J. Winicour, Y. Zlochower
 arXiv:1011.4223, Phys Rev D 84, 044057,
 doi: http://dx.doi.org/10.1103/PhysRevD.84.044057

Title: Cauchy-characteristic matching
 Authors: N. Bishop, R. Isaacson, R. Gomez, L. Lehner, B. Szilagyi, J.
 Winicour arXiv: gr-qc/9801070
 In "Black Holes, Gravitational Radiation and the Universe"
 eds. B. Iyer and B. Bhawal (Kluwer, Dordrecht, Boston,1999)

This code is maintained by

M. C.Babiuc <babiuc@marshall.edu>
B. Szilagyi <bela.szilagyi@jpl.nasa.gov>
C. Reisswig <reisswig@tapir.caltech.edu>
Y. Zlochower <yosef@astro.rit.edu>

Please contact the maintainers of the code if you require
write access.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| linearized_news | "no" | shall we calculate only the linearized news |  |
| first_order_scheme | "yes" | should angular derviatives be reduced to first order? |  |
| filter_scri_fields | "no" | filter null code fields before constructing News |  |
| filter_omega | "no" | filter omega and comega |  |
| filter_news | "no" | filter news (prior to intorpolation) |  |
| write_spherical_harmonics | "no" | Write the spherical harmonics of the News to a file? |  |
| NewsSpinWeight | 2 | What weight for the News when writing the spherical harmonics? |   0:2 :: "Small range"*OR* |
| DEBUG_skip_BondiNews | "no" | should the bondi news algorithm be skipped? |  |
| debug_output | "no" | Decompose also J and omega at Scri for debugging purposes |  |
| interp_to_constant_uBondi | 5 | Interpolate quantities at Scri to constant Bondi time |    1:* :: "anything >= 1"*OR* |
| use_linearized_omega | 0.0 | This switch is useful when the code is operating in the linear regime |   *:* :: ""*OR* |
| news_interp_order | 3 | Order for news interpolation |   1:6   :: "Choose between first, second, third, and fourth-order"*OR* |
| Jl_deriv_order | 2 | What order shall we use for the l derivative of J at scri? |  1:4 :: "Choose between first, second, third and fourth"*OR* |
| mask_Psi4 | "no" | should Psi4 be masked over used points? |  |
| mask_NewsB | "no" | should NewsB be masked over used points? |  |
| Ylm_at_Scri | "no" | This switch is introduced to check convergence for the spherical decomposition |  |
| compute_lin_strain | no | Compute linearized strain? |  |
| linearized_inertial_frame | no | Interpolation to inertial angular coordinates is linearized |  |


### SphericalHarmonicReconGen

Cactus Code Thorn SphericalHarmonicReconGen
Thorn Author(s)     : Christian Reisswig <reisswig@tapir.caltech.edu>
Thorn Maintainer(s) : Christian Reisswig <reisswig@tapir.caltech.edu>
Licence      : lGPL
--------------------------------------------------------------------------

Purpose of the thorn: 

Reads in ADM metric boundary data from a file. The files must contain scalar spherical harmonic modes up to some lmax
for the lapse, shift, and 3-metric decomposed on some finite-radius coordinate sphere S^2.
Gnuplot compatible ASCII files are supported (this includes Cactus'/Llama's Worldtube output), as well as
some SpEC specific ASCII files, and SpEC HDF5 output.



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| format | "ASCII" | Boundary data file format |   ASCII      :: "standard ASCII format: lm-modes and spheres are stored in a 2d array (gnuplot style) for a given timestep"*OR*  DAT        :: "optimized ASCII format: all variables and lm-modes are stored in one row for a given timestep"*OR*  DAT-v2     :: "optimized ASCII format: all variables and lm-modes are stored in one row for a given timestep. m-modes are in reverse order."*OR*  SpEC-H5    :: "SpEC-HDF5 format: Data is stored in HDF5 files grouped according to SpEC output."*OR*  SpEC-H5-v2 :: "SpEC-HDF5 format (v2): Data is stored in HDF5 files grouped according to new SpEC output."*OR* |
| disable_auto_time_offset | no | Should the automatically determined time-offset be diabled? |  |
| adjust_timestep | no | Should the time-step be adjusted? Ignored if time_interpolate==false. |  |
| sphere_number | 0 | the sphere number (if multiple spheres are present in one file) that corresponds to the worldtube radius NullSHRExtract::cr |   0:* :: "positive"*OR* |
| lmaxInFile | 0 | maximum l-mode contained in file (only needed for DAT format) |   0:* :: "anything positive"*OR* |
| time_derivative_in_file | "no" | is the time derivative of the metric in the cauchy file |  |
| time_fd_order | 4 | the order of accuracy of time derivative if not contained in file |    2  :: "second-order"*OR*   4  :: "fourth-order"*OR* |
| time_interpolate | no | Interpolate coefficients to different dt? |  |
| initial_time | 0.0 | time from the worldtube data that we use as first characteristic timestep (this is a relative time, i.e. we always count the time from 0, independent of the actual initial time t_0 found in the file) |   *:* :: "anything"*OR* |
| initial_relaxation_time | 0.0 | A time intervall for which we just use the initial set of metric data in order to relax the characteristic slice towards our initial data |   0:* :: "A psoitive timescale"*OR* |
| verbose | no | turn verbosity on/off |  |
| cached_timesteps | 0 | Number of timesteps that shall be read in advance and cached in memory for faster access. |   0:* :: "anything positive"*OR* |
| sYlm_convention | Goldberg67 | convention of sYlm's used for decomposed input worldtube data. |    Goldberg67             :: "Convention used in the Goldberg 1967 paper."*OR*   Condon-Shortley-Phase  :: "Definition that differs by a factor (-1)^m wrt to the Goldberg67 definition."*OR* |
| dt_tolerance | 1e-9 | A minimum tolerance for which timesteps are considered being identical |   0:* :: "Small and positive"*OR* |
| file | "" | The boundary data file. If this string is empty, we assume that the user provided files for each quantity individually (parameters below) |  ""	:: "a valid filename"*OR* |
| path | "" | the directory that contains the input files, leave blank if filenames contain directory paths already |  ""	:: "a path name"*OR* |
| column_iteration | -1 | the column of the iteration (counting from 1) |   -1  :: "-1 if not avalaible"*OR*  1:* :: "column number"*OR* |
| column_time | 1 | the column of the time (counting from 1) |   -1  :: "-1 if not avalaible"*OR*  1:* :: "column number"*OR* |
| column_radius | -1 | the column of the radius (counting from 1) |   -1  :: "-1 if not avalaible"*OR*  1:* :: "column number"*OR* |
| column_lmax | -1 | the column of number of l-modes (counting from 1) |   -1  :: "-1 if not avalaible"*OR*  1:* :: "column number"*OR* |
| column_n_variables | -1 | the column of number of variables (counting from 1) |   -1  :: "-1 if not avalaible"*OR*  1:* :: "column number"*OR* |
| column_data | 3 | the column where the data begins (only needed for DAT format) |   2:* :: "anything greater than 2"*OR* |


### NullInterp

CVS info   : $Header$

Cactus Code Thorn NullInterp
Thorn Author(s)     : Maria Cristina Babiuc <maria@einstein.phyast.pitt.edu>
                    : Bela Szilagyi <bela.szilagyi@jpl.nasa.gov>
                    : Yosef Zlochower <yosef@einstein.phyast.pitt.edu>
Thorn Maintainer(s) : Maria Cristina Babiuc <maria@einstein.phyast.pitt.edu>
                    : Bela Szilagyi <bela.szilagyi@jpl.nasa.gov>
--------------------------------------------------------------------------

Purpose of the thorn:

This code is available under the  GPL General Public License, 
version 2, or later (see GPLv2 file included).

The code in this arrangement is a results of many years of work by
many different authors. Major contributors to this code include
(in alphabetical order)

M.C. Babiuc
N. T. Bishop
R. Gomez
R. A. Isaacson
L. Lehner
P. Papadopoulos
C. Reisswig
B. Szilagyi
J. Welling
J. Winicour
Y. Zlochower


If you use this code, then we request that you cite the following papers:

Title: Characteristic Extraction Tool for Gravitational Waveforms
 Authors: M.C. Babiuc, B. Szilagyi, J. Winicour, Y. Zlochower
 arXiv:1011.4223, Phys Rev D 84, 044057,
 doi: http://dx.doi.org/10.1103/PhysRevD.84.044057

Title: Cauchy-characteristic matching
 Authors: N. Bishop, R. Isaacson, R. Gomez, L. Lehner, B. Szilagyi, J.
 Winicour arXiv: gr-qc/9801070
 In "Black Holes, Gravitational Radiation and the Universe"
 eds. B. Iyer and B. Bhawal (Kluwer, Dordrecht, Boston,1999)

This code is maintained by

M. C.Babiuc <babiuc@marshall.edu>
B. Szilagyi <bela.szilagyi@jpl.nasa.gov>
C. Reisswig <reisswig@tapir.caltech.edu>
Y. Zlochower <yosef@astro.rit.edu>

Please contact the maintainers of the code if you require
write access.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| interpolation_order | 3 | Order for interpolation |   1:6	:: "Choose between first, second, third, and fourth-order"*OR* |
| deriv_accuracy | 2 | Order of derivative operators |   2	:: "2nd order accurate derivatives"*OR*  4	:: "4th order accurate derivatives"  *OR* |
| poison_test | "no" | should invalid interpolation input points be poisoned? |  |
| skip_interpolation | "no" | should the interpolation be skipped? |  |
| extended_guard_zone | "no" | should the set of guard points be extended to contain all inactive points? |  |
| poison_value | 1.0e+10 | use this value to mark illegal interpolation input points |  *:*	:: "any value, should be something like a NaN"*OR* |
| mask_testinterp_tolerance | 1.0e-10 | tolerance level when detecting interpolation values from illegal input points |  *:*	:: "any value, should be something like eps"*OR* |


### SphericalHarmonicDecomp

CVS info   : $Header:$

Cactus Code Thorn SphericalHarmonicDecomp
Thorn Author(s)     : Yosef Zlochower <yosef@astro.rit.edu>
Thorn Maintainer(s) : Yosef Zlochower <yosef@astro.rit.edu>
--------------------------------------------------------------------------

Purpose of the thorn:

This code is available under the  GPL General Public License, 
version 2, or later (see GPLv2 file included).

The code in this arrangement is a results of many years of work by
many different authors. Major contributors to this code include
(in alphabetical order)

M.C. Babiuc
N. T. Bishop
R. Gomez
R. A. Isaacson
L. Lehner
P. Papadopoulos
C. Reisswig
B. Szilagyi
J. Welling
J. Winicour
Y. Zlochower


If you use this code, then we request that you cite the following papers:

Title: Characteristic Extraction Tool for Gravitational Waveforms
 Authors: M.C. Babiuc, B. Szilagyi, J. Winicour, Y. Zlochower
 arXiv:1011.4223, Phys Rev D 84, 044057,
 doi: http://dx.doi.org/10.1103/PhysRevD.84.044057

Title: Cauchy-characteristic matching
 Authors: N. Bishop, R. Isaacson, R. Gomez, L. Lehner, B. Szilagyi, J.
 Winicour arXiv: gr-qc/9801070
 In "Black Holes, Gravitational Radiation and the Universe"
 eds. B. Iyer and B. Bhawal (Kluwer, Dordrecht, Boston,1999)

This code is maintained by

M. C.Babiuc <babiuc@marshall.edu>
B. Szilagyi <bela.szilagyi@jpl.nasa.gov>
C. Reisswig <reisswig@tapir.caltech.edu>
Y. Zlochower <yosef@astro.rit.edu>

Please contact the maintainers of the code if you require
write access.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| out_dir | "" | output directory |   "^$" :: "Empty stting -> use IO::out_dir"*OR*  ".+" :: "directory name"*OR* |
| max_spin | 2 | maximum absolute spin of fields |   0:* :: "positive only"*OR* |
| num_l_modes | 7 | number of l modes ... not lmax |   1:* :: "1 or larger"*OR* |
| num_m_modes | 15 | number of m modes ... = 2*mmax + 1 | #  1:*:2 :: "must be odd and 1 or larger"*OR*# |
| num_n_modes | 15 | number of n modes |   1:* :: " 1 or larger"*OR* |
| num_mu_points | 15 | number of points in mu direction |   1:* :: ""*OR* |
| num_phi_points | 31 | number of points in phi directions |   1:* :: ""*OR* |
| num_x_points | 31 | number of points in phi directions |   1:* :: ""*OR* |
| Rin | 10.0 | inner radius for decomp |   0:* :: "positive"*OR* |
| Rout | 100.0 | outer radius for decomp |   (0:* :: "positive > Rin"*OR* |
| num_radii | 100 | number of radii |   1:99 :: ""*OR*}1*OR**OR*CCTK_INT output_m_max "don't dump m modes larger than this"*OR*{*OR*  0:* :: "positive or zero"*OR* |
| output_l_max | 100 | don't dump l modes larger than this |   0:* :: "positive, but if l_max < |s| then no output generated"*OR* |
| sync_file_each_time | "yes" | not yet activated |  |
| do_test | "no" | to test or not to test |  |
| extract_spacetime_metric_every | 0 |  |   0:* :: "0 or positive. 0 means never"*OR* |


### NullExact

CVS info   : $Header$

Cactus Code Thorn NullExact
Thorn Author(s)     : Bela Szilagyi <bela.szilagyi@jpl.nasa.gov>
                    : N T Bishop <N T Bishop <bishont@unisa.ac.za>>
                    : Chi Wai Lai <Kevin <laicw@astro.unisa.ac.za>>
Thorn Maintainer(s) : Bela Szilagyi <bela.szilagyi@jpl.nasa.gov>
                    : N T Bishop <N T Bishop <bishont@unisa.ac.za>>
                    : Chi Wai Lai <Kevin <laicw@astro.unisa.ac.za>>
--------------------------------------------------------------------------

Purpose of the thorn:

This code is available under the  GPL General Public License, 
version 2, or later (see GPLv2 file included).

The code in this arrangement is a results of many years of work by
many different authors. Major contributors to this code include
(in alphabetical order)

M.C. Babiuc
N. T. Bishop
R. Gomez
R. A. Isaacson
L. Lehner
P. Papadopoulos
C. Reisswig
B. Szilagyi
J. Welling
J. Winicour
Y. Zlochower


If you use this code, then we request that you cite the following papers:

Title: Characteristic Extraction Tool for Gravitational Waveforms
 Authors: M.C. Babiuc, B. Szilagyi, J. Winicour, Y. Zlochower
 arXiv:1011.4223, Phys Rev D 84, 044057, 
 doi: http://dx.doi.org/10.1103/PhysRevD.84.044057

Title: Cauchy-characteristic matching
 Authors: N. Bishop, R. Isaacson, R. Gomez, L. Lehner, B. Szilagyi, J.
 Winicour arXiv: gr-qc/9801070
 In "Black Holes, Gravitational Radiation and the Universe"
 eds. B. Iyer and B. Bhawal (Kluwer, Dordrecht, Boston,1999)

This code is maintained by

M. C.Babiuc <babiuc@marshall.edu>
B. Szilagyi <bela.szilagyi@jpl.nasa.gov>
C. Reisswig <reisswig@tapir.caltech.edu>
Y. Zlochower <yosef@astro.rit.edu>

Please contact the maintainers of the code if you require
write access.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### NullSHRExtract

Cactus Code Thorn NullExtract
Author(s)    : Maria Babiuc-Hamilton <babiuc@marshall.edu>
Maintainer(s): Maria Babiuc-Hamilton <babiuc@marshall.edu>
Licence      : N
--------------------------------------------------------------------------

1. Purpose

not documented
This code is available under the  GPL General Public License, 
version 2, or later (see GPLv2 file included).

The code in this arrangement is a results of many years of work by
many different authors. Major contributors to this code include
(in alphabetical order)

M.C. Babiuc
N. T. Bishop
R. Gomez
R. A. Isaacson
L. Lehner
P. Papadopoulos
C. Reisswig
B. Szilagyi
J. Welling
J. Winicour
Y. Zlochower


If you use this code, then we request that you cite the following papers:

Title: Characteristic Extraction Tool for Gravitational Waveforms
 Authors: M.C. Babiuc, B. Szilagyi, J. Winicour, Y. Zlochower
 arXiv:1011.4223, Phys Rev D 84, 044057,
 doi: http://dx.doi.org/10.1103/PhysRevD.84.044057

Title: Cauchy-characteristic matching
 Authors: N. Bishop, R. Isaacson, R. Gomez, L. Lehner, B. Szilagyi, J.
 Winicour arXiv: gr-qc/9801070
 In "Black Holes, Gravitational Radiation and the Universe"
 eds. B. Iyer and B. Bhawal (Kluwer, Dordrecht, Boston,1999)

This code is maintained by

M. C.Babiuc <babiuc@marshall.edu>
B. Szilagyi <bela.szilagyi@jpl.nasa.gov>
C. Reisswig <reisswig@tapir.caltech.edu>
Y. Zlochower <yosef@astro.rit.edu>

Please contact the maintainers of the code if you require
write access.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| cr | 2 | radius of the worltube |   (0.0:*::  "strictly positive real number"*OR*}0.5*OR**OR*CCTK_REAL mass "mass parameter"*OR*{*OR*  (0.0:*::  "strictly positive real number"*OR*}0.1*OR**OR*int l_max "l_max in Ylm"*OR*{*OR*   0:*	:: "positive"*OR* |
| MinimumDistanceTo2Bp1 | 0.0 | minimum distance from the extraction world-tube to the B+1 point |   0:*  :: "positive. Should be O(1)"*OR* |
| fcoef | 0.01 | Coefficient of the time dependence for Schwazchild time metric |  *:*    :: "anything"*OR*}0.0*OR**OR*CCTK_REAL Afact "Factor in front of the sine time dependence for Schwazchild time metric"*OR*{*OR* *:*    :: "anything"*OR*}0.0*OR**OR*CCTK_REAL elld_min_coef "definition of elld_min = elld_min_coef * dt**elld_min_pow"*OR*{*OR*  0:*   :: "larger than zero"*OR* |
| elld_min_pow | 2 | definition of elld_min = elld_min_coef * dt**elld_min_pow |   0:*   :: "larger than zero"*OR* |
| halt_on_negative_elld | "no" | Should the code quit if the denominator of the null vector changes sign? |  |
| rl_min_coef | 0.01 | definition of rl_min = rl_min_coef * dt**rl_min_pow |   0:*   :: "larger than zero"*OR* |
| rl_min_pow | 2 | definition of rl_min = rl_min_coef * dt**rl_min_pow |   0:*   :: "larger than zero"*OR* |
| halt_on_negative_rl | "no" | Should the code quit if r_{lambda} goes negative? |  |
| WT_spherical_harmonics | "no" | Output the spherical harmonics of the Worldtube to a file? |  |
| WT_spherical_harmonics_on_nullgrid | "no" | Output the spherical harmonics on the nullgrid just off the Worldtube to a file? |  |


### NullEvolve

CVS info   : $Header$

Cactus Code Thorn NullEvolve
Thorn Author(s)     : Y.Z. <yosef@raven.phyast.pitt.edu>
Thorn Maintainer(s) : Y.Z. <yosef@raven.phyast.pitt.edu>
--------------------------------------------------------------------------

Purpose of the thorn:

Characteristic evolution


TODO:
add dissipation to J:
J_{ur} += eps * f(r) * eth_ethb_J_r
This code is available under the  GPL General Public License, 
version 2, or later (see GPLv2 file included).

The code in this arrangement is a results of many years of work by
many different authors. Major contributors to this code include
(in alphabetical order)

M.C. Babiuc
N. T. Bishop
R. Gomez
R. A. Isaacson
L. Lehner
P. Papadopoulos
C. Reisswig
B. Szilagyi
J. Welling
J. Winicour
Y. Zlochower


If you use this code, then we request that you cite the following papers:

Title: Characteristic Extraction Tool for Gravitational Waveforms
 Authors: M.C. Babiuc, B. Szilagyi, J. Winicour, Y. Zlochower
 arXiv:1011.4223, Phys Rev D 84, 044057,
 doi: http://dx.doi.org/10.1103/PhysRevD.84.044057

Title: Cauchy-characteristic matching
 Authors: N. Bishop, R. Isaacson, R. Gomez, L. Lehner, B. Szilagyi, J.
 Winicour arXiv: gr-qc/9801070
 In "Black Holes, Gravitational Radiation and the Universe"
 eds. B. Iyer and B. Bhawal (Kluwer, Dordrecht, Boston,1999)

This code is maintained by

M. C.Babiuc <babiuc@marshall.edu>
B. Szilagyi <bela.szilagyi@jpl.nasa.gov>
C. Reisswig <reisswig@tapir.caltech.edu>
Y. Zlochower <yosef@astro.rit.edu>

Please contact the maintainers of the code if you require
write access.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| first_order_scheme | "yes" | Should the first order (angular) scheme be used? |  |
| boundary_data | "flat" | Choose boundary data type |   "whitehole"	::"White hole boundary data"*OR*  "flat"	::"flat boundary data"*OR*  "randomJ" 	::"random number boundary data"*OR* |
| initial_J_data | "vanishing_J_scri" | What kind of initial data for J shall we pick? |   "vanishing_J"		:: "vanishing J"*OR*  "vanishing_J_scri"	:: "vanishing J at scri"*OR*  "rotating_pulse"	:: "rotating pulse"*OR*  "compact_J"		:: "compact suport J"*OR*  "smooth_J" 	        :: "smoothly vanishing J"*OR*  "extracted_J"		:: "J as given by extraction"*OR*  "polynomial_J"	:: "J as third power polynomial to vanish J_xx at scri"*OR*  "fitted_linearized_J" :: "linearized fitted J based on some previous run"*OR* |
| first_order_dissip | "no" | Should the first order agnular variables be dissipated? | # |
| use_rsYlm | 0.0 | do we use real spherical harmonics for compact pulse initial data | }"yes"*OR**OR*####################################################################################*OR*## DEBUGGING*OR*####################################################################################*OR**OR*restricted:*OR**OR*CCTK_REAL Diagnostics_Coord_x "the coordinate x at which diagnostics are to be measured" STEERABLE=ALWAYS*OR*{*OR*  0:1  :: "will test only for x >= NullGrid::null_xin."*OR* |
| DEBUG_skip_evolution | "no" | Should the evolution be turned off? | }"no"*OR**OR*BOOLEAN DEBUG_skip_J_update "Should the update of J be turned off?" *OR*{*OR*}"no"*OR**OR*BOOLEAN DEBUG_skip_NU_update "Should the update of NU be turned off?" *OR*{*OR*}"no"*OR**OR*BOOLEAN DEBUG_skip_CK_update "Should the update of CK be turned off?" *OR*{*OR*}"no"*OR**OR*BOOLEAN DEBUG_skip_B_update "Should the update of B be turned off?" *OR*{*OR*}"no"*OR**OR*BOOLEAN DEBUG_skip_CB_update "Should the update of CB be turned off?" *OR*{*OR*}"no"*OR**OR*BOOLEAN DEBUG_skip_Q_update "Should the update of Q be turned off?" *OR*{*OR*}"no"*OR**OR*BOOLEAN DEBUG_skip_U_update "Should the update of U be turned off?" *OR*{*OR*}"no"*OR**OR*BOOLEAN DEBUG_skip_W_update "Should the update of W be turned off?" *OR*{*OR*}"no"*OR**OR*BOOLEAN debug_verbose "should debugging messages be printed?"*OR*{*OR* |
| old_J_xderiv | "no" | should we compute the x derivative of J with the old values? |  |


### NullVars

CVS info   : $Header$

Cactus Code Thorn NullVars
Thorn Author(s)     : Y.Z. <yosef@raven.phyast.pitt.edu>
Thorn Maintainer(s) : Y.Z. <yosef@raven.phyast.pitt.edu>
--------------------------------------------------------------------------

Purpose of the thorn:

This code is available under the  GPL General Public License, 
version 2, or later (see GPLv2 file included).

The code in this arrangement is a results of many years of work by
many different authors. Major contributors to this code include
(in alphabetical order)

M.C. Babiuc
N. T. Bishop
R. Gomez
R. A. Isaacson
L. Lehner
P. Papadopoulos
C. Reisswig
B. Szilagyi
J. Welling
J. Winicour
Y. Zlochower


If you use this code, then we request that you cite the following papers:

Title: Characteristic Extraction Tool for Gravitational Waveforms
 Authors: M.C. Babiuc, B. Szilagyi, J. Winicour, Y. Zlochower
 arXiv:1011.4223, Phys Rev D 84, 044057,
 doi: http://dx.doi.org/10.1103/PhysRevD.84.044057

Title: Cauchy-characteristic matching
 Authors: N. Bishop, R. Isaacson, R. Gomez, L. Lehner, B. Szilagyi, J.
 Winicour arXiv: gr-qc/9801070
 In "Black Holes, Gravitational Radiation and the Universe"
 eds. B. Iyer and B. Bhawal (Kluwer, Dordrecht, Boston,1999)

This code is maintained by

M. C.Babiuc <babiuc@marshall.edu>
B. Szilagyi <bela.szilagyi@jpl.nasa.gov>
C. Reisswig <reisswig@tapir.caltech.edu>
Y. Zlochower <yosef@astro.rit.edu>

Please contact the maintainers of the code if you require
write access.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


## CactusPUGHIO

### IOHDF5

Cactus Code Thorn IOHDF5
Author(s)    : Thomas Radke
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn does output of arbitrary Cactus variables in HDF5 file format.

It also provides checkpointing/recovery functionality -
you can create HDF5 checkpoint files and recover from them.


2. Additional information

Please refer to the documentation.tex file in the doc/ subdirectory.

Fore more information on HDF5 see http://hdf.ncsa.uiuc.edu/HDF5/.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| out_every | -1 | How often to do IOHDF5 output, overrides IO::out_every |    1:* :: "Every so many iterations"*OR*   0:  :: "Disable IOHDF5 output"*OR*  -1:  :: "Default to IO::out_every"*OR* |
| out_dir | "" | Output directory for IOHDF5 files, overrides IO::out_dir |   ".+" :: "A valid directory name"*OR*  "^$" :: "An empty string to choose the default from IO::out_dir"*OR* |
| out_vars | "" | Variables to output in HDF5 file format |   ".+" :: "Space-separated list of fully qualified variable/group names"*OR*  "^$" :: "An empty string to output nothing"*OR* |
| checkpoint | "no" | Do checkpointing with HDF5 |  |
| checkpoint_next | "no" | Checkpoint at next iteration |  |


### IOHDF5Util

Cactus Code Thorn IOHDF5Util
Author(s)    : Thomas Radke
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn serves as a utility thorn for other HDF5 I/O thorns such as
IOStreamedHDF5 and IOHDF5.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| compression_level | 0 | Compression level to use for writing HDF5 data |   0:9 :: "Higher numbers compress better, a value of zero disables compression"*OR* |


## CactusUtils

### Formaline

Cactus Code Thorn Formaline
Thorn Author(s)     : Erik Schnetter <schnetter@cct.lsu.edu>
Thorn Maintainer(s) : Erik Schnetter <schnetter@cct.lsu.edu>
--------------------------------------------------------------------------

Purpose of the thorn:

Send meta information about a run to a server, so that it is kept
there forever.  The information sent is e.g. the parameter file, date,
time, machine, and user id of the run, location of the output data,
number of iterations, an efficiency summary, etc.

Meaning and pronunciation.  Formaline (or: formalin) is a solution of
formaldehyde (methanal) in water.  The chemical formula of
formaldehyde is H_2CO.  Formaline is commonly used to preserve
biological specimen.  It is pronounced "fOrmaleen".



How to get a stored source tree out of a git repository:
git checkout GIT-COMMIT-ID



TODO:

put unique job IDs into all output files
        done for Carpet output
        but not yet after restarting

BSD tar: read files from file: -I filename
tar: don't use -z; use tar and gzip
AIX: tar is GNU tar, but uses -L instead of -T
use GNU tar, and autodetect it properly

follow symbolic links
(maybe: respect hard links)

IOUtil should not depend on anything
(MoL should not depend on NaNChecker)

use a configuration script to amend all thorns' make.code.deps files
to create the tarballs when the thorns are compiled

put arrangement name into thorn source name
use subdirectories for arrangements
use strings instead of chars for data

announce: maybe use <array> for parameter arrays
          maybe use <dateTime.iso8601>20100302T00:00:00</dateTime.iso8601>
                for dates and times

put the output files into the build directory instead of the scratch
directory

output grid variables
register as output method
implement reductions
implement missing data types
output only if value has changed

update the source tarballs not only when the thorn library changes,
but also when a *.ccl or make.* file changes.
do not necessarily update them all when the bindings change.

fork (or spawn a thread) before announcing, so that the simulation
does not have to wait
        make sure to kill the old spawn before a new is created

make max_warn_level and output_info steerable

register with Portal/Announce as information provider
[Tom/Ian want to make Announce accept a table instead of XMLRPC]

prevent connecting to the wrong simulation, if a simulation is
outdated, and a new simulation uses the same hostname/port
combination: the portal can store the job id, and if there is a new
port id for the same hostname/port combination, deactivate the old
link.

rebuild the flesh tarball when the ThornList changes

announce in the background, maybe with a larger timeout


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | no | Produce screen output |  |
| collect_metadata | yes | Collect metadata and output it in various ways |  |
| update_interval | 3600.0 | Update interval for the meta information (in seconds) |   0:* :: ""*OR* |
| publish_level | 1 | Importance level for meta data to be published via the Publish API |   0:10 :: "the lower the level the more important the meta data to be published"*OR* |
| timeout | 10 | Timeout for sending meta information to a server (in seconds) |   1:* :: "a positive value"*OR* |
| metadata_lifetime | 100 | Lifetime of meta information sent to a server (in hours) |   0   :: "zero to store the metadata forever"*OR*  1:* :: "a positive value to limit the lifetime to so many hours"*OR* |
| steered_parameters_log_exclusion_list | "CarpetRegrid::coordinates" | Parameters for which runtime changes should not be logged |   "" :: "whitespace-separated list of parameter names"*OR* |
| nr_of_parameter_changes_to_be_logged | -1 | number of parameter changes to be logged |   -1: :: "log all changes"*OR*   0: :: "no logs please"*OR*   1:*:: "log this many changes for a given parameter, then stop logging"*OR* |
| create_id_files | yes | Create files from the build and job ids |  |
| announce_to_portal | no | Announce meta information to the portal |  |
| portal_username | "" | User name on the portal |   "" :: ""*OR* |
| send_as_rdf | no | Send meta as RDF to a server |  |
| store_into_file | yes | Store meta information in a file |  |
| store_into_json_file | yes | Store meta information in a file in JSON format |  |
| storage_filename | "formaline-jar.txt" | File name for meta information |   "" :: ""*OR* |
| storage_json_filename | "formaline-jar.json" | File name for meta information in JSON format |   "" :: ""*OR* |
| use_relay_host | yes | Use a relay host for connecting to the portal |  |
| relay_host | "" | Relay host for connecting to the portal |   ".+" :: "host name"*OR*  ""   :: "try to determine a relay host automatically"*OR* |
| output_source | yes | Output a tarball with the cactus source tree |  |
| output_source_subdirectory | "cactus-source" | Subdirectory, relative to IO::out_dir, for the Cactus source tarballs |   "" :: ""*OR* |
| out_vars | "" | Cactus variables that should be output |   "" :: "space separated list of groups or variables"*OR* |
| out_reductions | "minimum | Reduction operators that should be applied to the output variables |   "" :: "space separated list of reduction operators"*OR* |
| max_warn_level | -1 | Only output warnings with at most this level of severity |   -1:* :: ""*OR* |
| output_info | "no" | Output info messages? |  |


### OpenCLRunTime



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "no" | Output detailed device information |  |
| veryverbose | "no" | Output even more detailed information |  |
| disassemble_kernels | "yes" | Disassemble kernels |  |
| disassemble_in_background | "yes" | Disassemble in the background (using fork) |  |
| opencl_options | "-cl-mad-enable | OpenCL compiler options |   "" :: ""*OR* |
| opencl_device_type | "CPU" | Device type |   "CPU"         :: ""*OR*  "GPU"         :: ""*OR*  "accelerator" :: ""*OR*  "any"         :: ""*OR* |
| memory_model | "copy" | Memory model |   "always-mapped" :: "CPU and GPU use the same memory (may violate standard)"*OR*  "copy"          :: "Copy buffers"*OR*  "map"           :: "Map buffers (requires same layout)"*OR* |
| sync_copy_whole_buffer | "no" | Copy whole buffer before/after syncing |  |
| vector_size_x | 0 | Vector size in x direction |   0   :: "use preferred vector size"*OR*  1:* :: ""*OR* |
| vector_size_y | 1 | Vector size in y direction |   1 :: ""*OR* |
| vector_size_z | 1 | Vector size in z direction |   1 :: ""*OR* |
| unroll_size_x | 1 | Unroll size in x direction |   1:* :: ""*OR* |
| unroll_size_y | 1 | Unroll size in y direction |   1:* :: ""*OR* |
| unroll_size_z | 1 | Unroll size in z direction |   1:* :: ""*OR* |
| group_size_x | 1 | Group size in x direction |   1:* :: ""*OR* |
| group_size_y | 1 | Group size in y direction |   1:* :: ""*OR* |
| group_size_z | 1 | Group size in z direction |   1:* :: ""*OR* |
| tile_size_x | 1 | Tile size in x direction |   1:* :: ""*OR* |
| tile_size_y | 1 | Tile size in y direction |   1:* :: ""*OR* |
| tile_size_z | 1 | Tile size in z direction |   1:* :: ""*OR* |


### Trigger

Cactus Code Thorn SystemStatistics
Author(s)    : Frank Loeffler <knarf@cct.lsu.edu>
Maintainer(s): Frank Loeffler <knarf@cct.lsu.edu>
Licence      : LGPLv2.1 or later
--------------------------------------------------------------------------

Purpose of this thorn
---------------------
This thorn can be used to define triggers to output variables due to
relations of (evl. other) variables.

Documentation
-------------
Look into the doc/ directory for more documentation.

Copyright
---------

This thorn is copyright (C) 2003-2012
by Frank Loeffler <knarf@cct.lsu.edu>

This thorn is free software; you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation; either version 2 of the License, or
(at your option) any later version.

This thorn is distributed in the hope that it will be useful, but
WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this thorn (see the file COPYING in this directory);
if not, write to the Free Software Foundation, Inc., 59 Temple
Place, Suite 330, Boston, MA  02111-1307  USA



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| Trigger_Number | 0 | Number of triggers |   0:10 :: "The number of triggers that are defined"*OR* |
| Trigger_Debug | 0 | turnes on debug messages |    0:1 :: "Only 0 (off, default) or 1 (on)"*OR* |


### Nice

Cactus Code Thorn Nice
Author(s)    : Frank Löffler
Maintainer(s): Frank Löffler
Licence      : GPLv2+
--------------------------------------------------------------------------

1. Purpose

This thorn changes the nice level of Cactus processes.



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| Nice_nice | 19 | Nice level | -20:19 :: "The range for the nice is from -20 to 19, but only root can use negative values, default is 19."*OR* |


### NoMPI

Cactus Code Thorn NoMPI
Author(s)    : Erik Schnetter <schnetter@cct.lsu.edu>
Maintainer(s): Erik Schnetter <schnetter@cct.lsu.edu>
Licence      : GPL
--------------------------------------------------------------------------

1. Purpose

Provide a dummy MPI implementation that works only a single process.
This implements only a subset of the MPI standard.

This thorn disables itself when an actual MPI implementation is
present.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### SystemTopology

Cactus Code Thorn SystemTopology
Author(s)    : Erik Schnetter <schnetter@gmail.com>
Maintainer(s): Erik Schnetter <schnetter@gmail.com>
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Report the system topology (nodes, cores, etc.) as reported by MPI and
hwloc.

Set thread affinities.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| set_thread_bindings | "auto" | Set thread bindings (aka thread-CPU affinity) |   "yes"  :: "set bindings"*OR*  "no"   :: "no not modify bindings"*OR*  "auto" :: "depends on system architecture"*OR* |
| thread_layout | "dense" | How to lay out threads over cores |   "dense" :: "Pack threads on as few NUMA nodes/cores as possible"*OR*  "loose" :: "Spread threads over all NUMA nodes/cores"*OR* |


### NaNCatcher

Cactus Code Thorn NaNCatcher
Authors    : Erik Schnetter <schnetter@uni-tuebingen.de>
--------------------------------------------------------------------------

Purpose of the thorn:

Switch the FPU into a mode where it catches nans and infinities.
Whether this is possible, andor whether this code knows how to do
that, is hardware and operating system dependent.



This should work on the following systems:

Systems with a GNU C library (glibc), version 2.2 or later, using
<fenv.h>.

Linux systems with an IA32 (aka i386) processor, when the compiler is
GNU or Intel, using <fpu_control.h>.

PowerPC systems with a GNU compiler, using code taken from the GNU C
library.

In cases where the nan-catcher does not work, you will see a warning
message "NaNCatcher disabled -- no support for your compiler" early at
run time.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### SystemStatistics

Cactus Code Thorn SystemStatistics
Author(s)    : Ian Hinder <ian.hinder@aei.mpg.de>
Maintainer(s): Ian Hinder <ian.hinder@aei.mpg.de>
Licence      : LGPLv2.1 or later
--------------------------------------------------------------------------

1. Purpose

This thorn collects information about the system Cactus is running on
and exposes it in Cactus grid variables.

Copyright
=========

This thorn is copyright (C) 2010-2013 by Ian Hinder
<ian.hinder@aei.mpg.de>

This thorn is free software; you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation; either version 2 of the License, or
(at your option) any later version.

This thorn is distributed in the hope that it will be useful, but
WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this thorn (see the file COPYING in this directory);
if not, write to the Free Software Foundation, Inc., 59 Temple
Place, Suite 330, Boston, MA  02111-1307  USA


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### Vectors

Cactus Code Thorn Vectors
Author(s)    : Erik Schnetter <schnetter@cct.lsu.edu>
Maintainer(s): Erik Schnetter <schnetter@cct.lsu.edu>
Licence      : LGPLv2.1 or later
--------------------------------------------------------------------------

1. Purpose

Provide C macro definitions and a C++ class template that help
vectorisation.



2. Build-time choices

Several choices can be made via configuration options, which can be
set to "yes" or "no":

VECTORISE (default "no"): Vectorise. Otherwise, scalar code is
generated, and the other options have no effect.



VECTORISE_ALIGNED_ARRAYS (default "no", experimental): Assume that all
arrays have an extent in the x direction that is a multiple of the
vector size. This allows aligned load operations e.g. for finite
differencing operators in the y and z directions. (Setting this
produces faster code, but may lead to segfaults if the assumption is
not true.)

VECTORISE_ALWAYS_USE_UNALIGNED_LOADS (default "no", experimental):
Replace all aligned load operations with unaligned load operations.
This may simplify some code where alignment is unknown at compile
time. This should never lead to better code, since the default is to
use aligned load operations iff the alignment is known to permit this
at build time. This options is probably useless.

VECTORISE_ALWAYS_USE_ALIGNED_LOADS (default "no", experimental):
Replace all unaligned load operations by (multiple) aligned load
operations and corresponding vector-gather operations. This may be
beneficial if unaligned load operations are slow, and if vector-gather
operations are fast.

VECTORISE_INLINE (default "no"): Inline functions into the loop body
as much as possible.  This can cause some compilers to run out of
memory for complex codes. (Enabling this may increase code size, which
can degrade performance if the instruction cache is small, but may
lead to increased performance due to omission of function-call
overhead.)

VECTORISE_STREAMING_STORES (default "yes"): Use streaming stores, i.e.
use store operations that bypass the cache. (Disabling this produces
slower code.)

VECTORISE_EMULATE_AVX (default "no", experimental): Emulate AVX
instructions with SSE2 instructions. This produces slower code, but
can be used to test AVX code on systems that don't support AVX.



3. Tests

Correctness testing is run at startup (in CCTK_PARAMCHECK) provided the
Vectors thorn is in the list of ActiveThorns. This aims to test all vector
operations by comparing the results with the same operations done using a
scalar implementation. The number of passed tests is always reported. If any
test fails, the run is aborted immediately.



4. Related software

[1] Vc: portable, zero-overhead SIMD library for C++,
<http://compeng.uni-frankfurt.de/index.php?id=88>

[2] SLEEF - SIMD Library for Evaluating Elementary Functions,
<http://shibatch.sourceforge.net>


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "no" | Verbose output during vector tests |  |


### NaNChecker

Cactus Code Thorn NaNChecker
Author(s)    : Thomas Radke
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn can be used to analyze CCTK variables of real or complex data type
for NaN (Not-a-Number) and (on availability of finite(3)) infinite values.

For details see the NaNChecker section in the Cactus ThornGuide.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| check_every | 0 | How often to check for NaNs |   0   :: "Never (default)"*OR*  1:* :: "Every so many iterations"*OR* |
| check_after | 0 | Start checking for NaNs after so many iterations |   0:* :: "Any valid iteration number"*OR* |
| report_max | -1 | How many NaNs to report for a single variable |   -1   :: "Report all (default)"*OR*   0:* :: "Do not report more than report_max number of NaNs"*OR* |
| check_vars | "" | Groups and/or variables to check for NaNs |   .* :: "List of full group and/or variable names, or 'all' for everything"*OR* |
| check_for | "both" | Check for NaNs and/or infinite numbers (only evaluated if finite(3) is available) |   "NaN"  :: "Check only for NaNs"*OR*  "Inf"  :: "Check only for infinite numbers"*OR*  "both" :: "Check for both NaNs and infinite numbers"*OR* |
| out_NaNmask | "yes" | Dump the NaN grid function mask into an HDF5 file |  |
| action_if_found | "just | What to do if a NaN was found |   "just warn" :: "Just print a level 1 warning"*OR*  "terminate" :: "Warn and terminate Cactus gracefully as soon as possible"*OR*  "abort"     :: "Warn and abort Cactus immediately"*OR* |
| verbose | "standard" | How much information to give |   "all"      :: "All information"*OR*  "standard" :: "Standard information"*OR* |
| ignore_restricted_points | "no" | do not check grid points whose values will be restricted away |  |
| setup_test | "no" | set up grid function with NaNs |  |


### WatchDog

Cactus Code Thorn WatchDog
Author(s)    : David Radice <dradice@caltech.edu>
               Einstein Toolkit maintainers <maintainers@einsteintoolkit.org>
Maintainer(s): Einstein Toolkit maintainers <maintainers@einsteintoolkit.org>
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Kills a simulation if it hangs

2. Usage

Activate the WatchDog thorn.

Original author: David Radice


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| check_every | 3600 | Check that the run is progressing every so many seconds |     1:* :: "Any positive integer"*OR* |


### Accelerator

| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "no" | Output detailed information |  |
| veryverbose | "no" | Output even more detailed information |  |
| copy_back_all_timelevels | "no" | Copy all timelevels back to the host for output |  |
| copy_back_every | 1 | When to copy variables back to the host |   0   :: "never"*OR*  1:* :: "every so many iterations"*OR* |
| copy_back_vars | "all" | Which variables to copy back |   .* :: "list of group or variable names; empty to copy nothing, 'all' to copy all variables"*OR* |
| copy_back_all_written_variables_in_analysis | "yes" | Copy all variables that are written to in analysis back to the host |  |
| only_reads_current_timelevel | "no" | Assume that functions read only the current timelevel |  |
| only_writes_current_timelevel | "no" | Assume that functions write only the current timelevel |  |


### TimerReport

Cactus Code Thorn TimerReport
Author(s)    : Cactus team
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Simplistic additional timer information.  This thorn provides timer
details to screen at different iterations and before checkpoints
depending on parameter settings.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| out_every | 0 | How often to output timer report to screen |   0   :: "No periodic output (default)"*OR*  1:* :: "Every so many iterations"*OR* |
| out_at | -1 | Output timer information at a given iteration |   -1   :: "Do not output at specific iteration (default)"*OR*  0:*  :: "At this iteration"*OR* |
| out_filename | "" | File name for timer reports |   "^$"   :: "empty filename: print to stdout"*OR*  "^.+$" :: "otherwise: print to that file"*OR* |
| before_checkpoint | "no" | Before a checkpoint |  |
| next | "no" | On next iteration |  |
| output_schedule_timers | "yes" | Output the schedule timers in a formatted tabular format |  |
| output_all_timers | "no" | Output one file per processor containing all the Cactus timers |  |
| output_all_timers_together | "no" | Output three files (formats .txt, .csv, and .tsv), containing information about all the Cactus timers (average, minimum, and maximum over all processors) |  |
| output_all_timers_readable | "no" | Output one file containing information about all the Cactus timers (average, minimum, and maximum over all processors), in a format that is readable by humans |  |
| all_timers_clock | "gettimeofday" | Which clock to use for the all timers output |   ".*"   :: "any clock name allowed"*OR* |
| n_top_timers | 0 | How many timers to include in the top timer report |   0   :: "Do not print the report"*OR*  1:* :: "Any number of timers"*OR* |


### TerminationTrigger

Cactus Code Thorn TerminationTrigger
Author(s)    : Christian D. Ott
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn watches the elapsed walltime.  If only n minutes are left
before the some limit set by the user, it triggers termination of the
simulation.  Termination is also triggered if a special file with a
special content exists.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| on_remaining_walltime | 0.0 | When to trigger termination in MINUTES |   0.0    :: "Don't trigger termination"*OR*  (0.0:* :: "So many minutes before your job walltime is over"*OR* |
| max_walltime | 0.0 | Walltime in HOURS allocated for this job |   0.0    :: "Don't trigger termination"*OR*  (0.0:* :: "Should be positive, right"*OR* |
| termination_from_file | "no" | Use termination file; specified by termination_filename |  |
| create_termination_file | "no" | Create an empty termination file at startup |  |
| termination_file | "/tmp/cactus_terminate" | Termination file name (either full path or relative to IO::out_dir) |   "" ::  "Termination file"*OR* |
| check_file_every | 1 | Check termination file every n timesteps |   1:* :: ""*OR* |
| output_remtime_every_minutes | 60.0 | Output remaining wall time every n minutes |   0.0    :: "No output"*OR*  (0.0:* :: "Output"*OR* |
| testsuite | "no" | manually trigger termination |  |


### MemSpeed

Cactus Code Thorn MemSpeed
Author(s)    : Erik Schnetter <eschnetter@perimeterinstitute.ca>
Maintainer(s): Erik Schnetter <eschnetter@perimeterinstitute.ca>
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Determine the speed of the CPU, as well as latencies and bandwidths of
caches and main memory. These provides ideal, but real-world values
against which the performance of other routines can be compared.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "no" | Verbose output |  |
| skip_largemem_benchmarks | "no" | Skip benchmarks that require much memory |  |


## EinsteinAnalysis

### WeylScal4

Cactus Code Thorn WeylScal4
Author(s)    : Tanja Bode
               Ian Hinder
               Roland Haas
               Erik Schnetter
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Calculate the Weyl Scalars for a given metric given the fiducial tetrad of
Baker, Campanelli, and Lousto. PRD 65,044001 (2002) without the final null
rotations.


2. Implementation

The code is generated automatically with Mathematica.  The Mathematica
scripts are in the subdirectory "m", and the Makefile in there
re-generates the code.  This requires Mathematica and Kranc to be installed.

The scripts look for a Kranc installation in the "m" directory, in the Cactus
root directory and in $HOME as well as in the location where they find the
kranc shell-script if it is in the $PATH. If your Kranc (or kranc) directory is
in neither of these places, then you can set the environment variable KRANCPATH
to the location of your Kranc installation.

This thorn was generated using Kranc from http://github.com/ianhinder/Kranc


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | 0 | verbose |   *:* :: ""*OR* |
| other_timelevels | 1 | Number of active timelevels for non-evolved grid functions |   0:3 :: ""*OR* |
| offset | 1.e-15 | offset |   *:* :: ""*OR* |
| xorig | 0 | xorig |   *:* :: ""*OR* |
| yorig | 0 | yorig |   *:* :: ""*OR* |
| zorig | 0 | zorig |   *:* :: ""*OR* |
| fdOrder | 2 | fdOrder |   2 :: ""*OR*  4 :: ""*OR*  6 :: ""*OR*  8 :: ""*OR* |
| tile_size | -1 | Loop tile size |   *:* :: ""*OR* |
| fd_order | "Nth" | fd_order |   "Nth" :: ""*OR*  "2nd" :: ""*OR*  "4th" :: ""*OR* |
| calc_scalars | "psi4" | Which scalars to calculate |   "psi4" :: ""*OR*  "psis" :: ""*OR* |
| calc_invariants | "never" | Compute invariants |   "always" :: ""*OR*  "never" :: ""*OR* |
| WeylScal4_MaxNumEvolvedVars | 0 | Number of evolved variables used by this thorn |   0:0 :: "Number of evolved variables used by this thorn"*OR* |
| WeylScal4_MaxNumArrayEvolvedVars | 0 | Number of Array evolved variables used by this thorn |   0:0 :: "Number of Array evolved variables used by this thorn"*OR* |
| timelevels | 3 | Number of active timelevels |   0:3 :: ""*OR* |
| rhs_timelevels | 1 | Number of active RHS timelevels |   0:3 :: ""*OR* |
| WeylScal4_psi4_calc_Nth_calc_every | 1 | WeylScal4_psi4_calc_Nth_calc_every |   *:* :: ""*OR* |
| WeylScal4_psi4_calc_2nd_calc_every | 1 | WeylScal4_psi4_calc_2nd_calc_every |   *:* :: ""*OR* |
| WeylScal4_psi4_calc_4th_calc_every | 1 | WeylScal4_psi4_calc_4th_calc_every |   *:* :: ""*OR* |
| WeylScal4_psis_calc_Nth_calc_every | 1 | WeylScal4_psis_calc_Nth_calc_every |   *:* :: ""*OR* |
| WeylScal4_psis_calc_2nd_calc_every | 1 | WeylScal4_psis_calc_2nd_calc_every |   *:* :: ""*OR* |
| WeylScal4_psis_calc_4th_calc_every | 1 | WeylScal4_psis_calc_4th_calc_every |   *:* :: ""*OR* |
| WeylScal4_invars_calc_Nth_calc_every | 1 | WeylScal4_invars_calc_Nth_calc_every |   *:* :: ""*OR* |
| WeylScal4_invars_calc_2nd_calc_every | 1 | WeylScal4_invars_calc_2nd_calc_every |   *:* :: ""*OR* |
| WeylScal4_invars_calc_4th_calc_every | 1 | WeylScal4_invars_calc_4th_calc_every |   *:* :: ""*OR* |
| WeylScal4_psi4_calc_Nth_calc_offset | 0 | WeylScal4_psi4_calc_Nth_calc_offset |   *:* :: ""*OR* |
| WeylScal4_psi4_calc_2nd_calc_offset | 0 | WeylScal4_psi4_calc_2nd_calc_offset |   *:* :: ""*OR* |
| WeylScal4_psi4_calc_4th_calc_offset | 0 | WeylScal4_psi4_calc_4th_calc_offset |   *:* :: ""*OR* |
| WeylScal4_psis_calc_Nth_calc_offset | 0 | WeylScal4_psis_calc_Nth_calc_offset |   *:* :: ""*OR* |
| WeylScal4_psis_calc_2nd_calc_offset | 0 | WeylScal4_psis_calc_2nd_calc_offset |   *:* :: ""*OR* |
| WeylScal4_psis_calc_4th_calc_offset | 0 | WeylScal4_psis_calc_4th_calc_offset |   *:* :: ""*OR* |
| WeylScal4_invars_calc_Nth_calc_offset | 0 | WeylScal4_invars_calc_Nth_calc_offset |   *:* :: ""*OR* |
| WeylScal4_invars_calc_2nd_calc_offset | 0 | WeylScal4_invars_calc_2nd_calc_offset |   *:* :: ""*OR* |
| WeylScal4_invars_calc_4th_calc_offset | 0 | WeylScal4_invars_calc_4th_calc_offset |   *:* :: ""*OR* |


### AHFinderDirect

Cactus Code Thorn AHFinderDirect
Authors    : Jonathan Thornburg <jthorn@aei.mpg.de>
Maintainer : Jonathan Thornburg <jthorn@aei.mpg.de>
CVS info   : $Header$
--------------------------------------------------------------------------

Purpose of This Thorn
=====================

This thorn finds an apparent horizon (or more generally, a closed
2-surface with S^2 topology having any desired constant expansion)
given the 3D xyz-grid metric and extrinsic curvature (and optionally
the StaticConformal conformal factor).  It uses a direct method,
writing the apparent horizon equation as an elliptic PDE on
angular-coordinate space.  This is very fast and accurate, but
it does require an intitial guess for the apparent horizon position.


Documentation
=============

The doc/ directory contains detailed documentation for this thorn,
including a discussion of those parameters which you'll likely want
to set (or at least know about) in practice.  For full information
on all the parameters, see the comments in the  param.ccl  file.

The par/ directory contains some sample par files.

src/CODESTYLE documents the general programming conventions used in
this thorn.

src/PORTING.OUTSIDE.CACTUS has some general remarks on what it would
take to port this thorn to a non-Cactus numerical relativity code.

See below in this file for notes on compiling this thorn.


Publications
============
The algorithms used in this thorn are described in the papers
@article{
	Thornburg2003:AH-finding,
	author = "Jonathan Thornburg",
	title  = "A Fast Apparent-Horizon Finder
		  for 3-Dimensional {C}artesian Grids in Numerical Relativity",
	journal = "Classical and Quantum Gravity",
	volume = 21, number = 2,
	year   = "2004", month = "January 21",
	pages  = "743--766",
	doi    = "10.1088/0264-9381/21/2/026",
	url    = "http://stacks.iop.org/0264-9381/21/743",
	eprint = "gr-qc/0306056",
	note   = "gr-qc/0306056",
	}
and
@article{
	Thornburg-1996-horizon-finding,
	author = "Jonathan Thornburg",
	title = "Finding Apparent Horizons in Numerical Relativity",
	journal = "Physical Review D",
	pages = "4899--4918",
	volume = 54, number = 8,
	year = 1996, month = "October 15",
	eprint = "gr-qc/9508014",
	}
As a courtesy, I ask that these papers be cited in any published research
which uses this thorn, or which uses code from this thorn.


Copyright
=========

This thorn is copyright (C) 2001-2004
by Jonathan Thornburg <jthorn@aei.mpg.de>.

This thorn is free software; you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation; either version 2 of the License, or
(at your option) any later version.

This thorn is distributed in the hope that it will be useful, but
WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this thorn (see the file COPYING in this directory);
if not, write to the Free Software Foundation, Inc., 59 Temple
Place, Suite 330, Boston, MA  02111-1307  USA


Other Software Used (Already Contained within AHFinderDirect)
=============================================================

AHFinderDirect's  src/sparse-matrix/  directory contains various
sparse-matrix libraries, which have their own copyrights and licensing terms:

The src/sparse-matrix/umfpack/ directory contains a subset of the files
in UMFPACK version 4.0 (11.Apr.2002).  This code is copyright (C) 2002 by
Timothy A. Davis, and is subect to the UMFPACK License:

    Your use or distribution of UMFPACK or any modified version of
    UMFPACK implies that you agree to this License.

    THIS MATERIAL IS PROVIDED AS IS, WITH ABSOLUTELY NO WARRANTY
    EXPRESSED OR IMPLIED.  ANY USE IS AT YOUR OWN RISK.

    Permission is hereby granted to use or copy this program, provided
    that the Copyright, this License, and the Availability of the original
    version is retained on all copies.  User documentation of any code that
    uses UMFPACK or any modified version of UMFPACK code must cite the
    Copyright, this License, the Availability note, and "Used by permission."
    Permission to modify the code and to distribute modified code is granted,
    provided the Copyright, this License, and the Availability note are
    retained, and a notice that the code was modified is included.  This
    software was developed with support from the National Science Foundation,
    and is provided to you free of charge.


Other Software Required
=======================

This thorn inherits from Grid, ADMBase, StaticConformal, SpaceMask, IO
(all in the CactusBase or CactusEinstein arrangements), and
SphericalSurface (in the AEIThorns arrangement; you can get AEIThorns
by anonymous CVS checkout from cvs_anon@cvs.aei.mpg.de:/numrelcvs, CVS
path AEIThorns/SphericalSurface).

By default this thorn uses various Cactus APIs which are supplied by
other thorns:
* This thorn uses CCTK_InterpGridArrays() for interpolating grid arrays.
  This is supplied by a driver-specific thorn, such as PUGHInterp or
  CarpetInterp.
* This thorn uses CCTK_ReduceLocArrayToArray1D() for interprocessor
  communication in the multiprocessor Newton solver.
	[see src/driver/README.parallel for details]
  This is supplied by a driver-specific thorn; at present PUGHReduce
  it this for the PUGH driver.
* This thorn uses CCTK_InterpLocalUniform() for interpatch and surface
  interpolation, and it needs various interpolation options which at
  present are (only) supported by the AEIThorns/AEILocalInterp local
  interpolator.

This thorn is written in C++, so you'll need a C++ compiler -- in fact
a fairly modern one -- to compile this thorn.  See the "Compilation Notes"
section below for details.

Most of this thorn's relativity code is machine-generated using Maple
(version 7), but you don't need Maple unless you want to modify the
relativity code.

By default, this thorn doesn't use any external libraries.  However,
if HAVE_DENSE_JACOBIAN__LAPACK is defined in src/include/config.h,
then this thorn uses the LAPACK library (which in turn uses the BLAS
library), so you will need to configure your Cactus to use LAPACK.
Instructions on doing this are in the file README.library in this
directory.


Code Notes
==========

This thorn is written in C++, and requires a fairly modern C++ compiler.
In particular:
* Templates are used, but only rather simple ones for containers or other
  low-level stuff templated on the floating-point or integer datatype.
  These templates are always instantiated explicitly.
* bool, mutable, typename are used.
* The new-style casts static_cast<...> and const_cast<...> are used.
  reinterpret_cast<...> and dynamic_cast<...> are *not* used.
* The code will compile ok under either the archaic or the modern
  for-loop declaration scope rules.
* C header files are used in the pre-namespaces form (eg <stdio.h>).
  (In ISO-standard C++ this is deprecated (but still supported), but
  alas none of the more modern forms (<cstdio> and namespace std::)
  seem to be supported on as wide a range of systems as the pre-namespaces
  form.)
* <assert.h> is used fairly heavily for sanity checks.
* To avoid various portability problems, none of the C++ standard
  template library (STL) is used.


Compiler Notes
==============

This thorn should be fully portable across architectures and operating
systems, but C++ compilers may be a problem:

This thorn has been compiled and run successfully using

x86
===
* gcc version 2.95.3 20010125 (prerelease)
* g++ (GCC) 3.2 for x86
* g++ (GCC) 3.2.2 for x86
* g++ (GCC) 3.3 for x86
* Intel(R) C++ Compiler for 32-bit applications, Version 5.0.1   Build 010730D0
* Intel C++ Compiler for Linux, version 6.0
* Intel(R) C++ Compiler for 32-bit applications, Version 7.0   Build 20021021Z
  On platinum.ncsa.uiuc.edu, Denis Pollney reports success configuring Cactus
  with
    LIBS="crypt F90 CEPCF90 intrins mkl mkl_lapack guide"
    LIBDIRS=/usr/local/intel/mkl/lib/32
* Portland Group pgCC 3.3-2
  On platinum.ncsa.uiuc.edu, Denis Pollney reports success configuring Cactus
  with
    LAPACK=yes
    LAPACK_DIR=/usr/local/pgi/linux86

ia64, a.k.a. Itanium
====================
* Intel(R) C++ Itanium(R) Compiler for Itanium(R)-based applications
  Version 7.0 Beta, Build 20020911
  On titan.ncsa.uiuc.edu, Denis Pollney reports success configuring Cactus
  with
    LIBS="crypt F90 CEPCF90 intrins mkl_lapack mkl_itp guide"
    LIBDIRS=/usr/local/intel/mkl/lib/64

Alpha
=====
* g++ (GCC) 3.0
  On lemieux.psc.edu, Denis Pollney reports success configuring Cactus
  with
    LIBS="m c_r dxml for"
* Using the DEC/Compaq/HP/whatever-their-corporate-name-is-this-week
  compiler, you need to specify the  -nopt  compiler option to disable
  automatic template instantiation.

SGI
===
* On modi4.ncsa.uiuc.edu (an old SGI Origin), using
  MIPSpro Compilers: Version 7.3.1.3m
  you need to specify the  -no_auto_include  compiler option to disable
  automatic template instantiation.  In fact, you should probably specify
  this option on any SGI system...

IBM Power4 (AIX)
================
* C for AIX Compiler, Version 6
  On psi19.rzg.mpg.de, Denis Pollney reports success configuring Cactus
  with
    LIBS="f m lapack-essl essl"
    LIBDIRS="/afs/rzg/@sys/lib"

Please let me (Jonathan) know of any other compilers which are ok.

This thorn compiles, but does *NOT* work, using the folloing
systems/compilers; the cure is to switch to a newer compiler version:
x86
===
* gcc version 2.96 20000731 (Red Hat Linux 7.1 2.96-98)
  (This is the system gcc on Red Hat GNU/Linux 7.2 systems)

The code will almost certainly *not* compile using
* any "egcs" version of gcc, or more generally, any version of gcc
  earlier than 2.95
* any version of Microsoft Visual C++ up to and including mid-2002


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### CalcK

CVS info   : $Header$

Cactus Code Thorn CalcK
Thorn Author(s)     : Erik Schnetter <schnetter@aei.mpg.de>
Thorn Maintainer(s) : Erik Schnetter <schnetter@aei.mpg.de>
--------------------------------------------------------------------------

Purpose of the thorn:

Calculate the extrinsic curvature K_ij from several time levels of the
three-metric gamma_ij, lapse alpha, and shift beta^i.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| copy_to_prev | "no" | Copy ADMBase::metric to CalcK::metric_prev |  |
| copy_to_prev2 | "no" | Copy ADMBase::metric to CalcK::metric_prev2 |  |
| copy_to_next | "no" | Copy ADMBase::metric to CalcK::metric_next |  |
| calc_extcurv | "no" | Calculate extrinsic curvature |  |
| extcurv_boundary | "Robin" | Boundary condition for the extrinsic curvature |   ".*" :: "must be a registered boundary condition"*OR* |
| extcurv_boundary_options | "decay_power=2.0" | Options for the boundry condition for the extrinsic curvature |   ".*" :: "must have the options table syntax"*OR* |


### PunctureTracker

Cactus Code Thorn PunctureTracker
Thorn Author(s)     : Michael Koppitz <koppitz@aei.mpg.de>
                      Erik Schnetter <schnetter@cct.lsu.edu>
                      Peter Diener <diener@cct.lsu.edu>
Thorn Maintainer(s) : Erik Schnetter <schnetter@cct.lsu.edu>
--------------------------------------------------------------------------

Purpose of the thorn:

Time integrate the shift at the location of the punctures to follow the
location of the punctures.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "no" | speak up? |  |
| modify_distance | 0.0 | Modify levels when the distance is less than this |   0.0:* :: "zero or positive"*OR* |
| shift_limit | 1.0e10 | Shift components must be less than this in magnitude |   0.0:* :: "zero or positive"*OR* |


### EHFinder

CVS info   : $Header$

Cactus Code Thorn EHFinder
Authors    : Peter Diener <diener@aei.mpg.de>
Maintainer : Peter Diener <diener@aei.mpg.de>
--------------------------------------------------------------------------

Purpose of the thorn:

To find the event horizon of a numerical spacetime by integrating a null
surface backwards in time.

The surface will be parametrized as the 0 value isosurface of a level
set function. This ensures that it is possible to handle cases when the
topology changes (when evolving backwards in time) from one common event
horizon to two (or more) individual event horizons.


Documentation:

The documentation can be found in the Thorn Guide provided with this
Thorn. For additional details see gr-qc/0305039.


History:

This event horizon finder was written by Peter Diener in 2002-2003.


Copyright & Licensing:

This thorn is copyright (C) 2002-2003
by Peter Diener <diener@aei.mpg.de>.

This program is free software; you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation; either version 2 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful, but
WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this thorn (see the file COPYING in this directory);
if not, write to the Free Software Foundation, Inc., 59 Temple
Place, Suite 330, Boston, MA 02111-1307 USA


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| eh_number_level_sets | 1 | How many level set functions should we evolve |   1:10        :: "Between 1 and 10"*OR* |
| eh_metric_type | "numerical" | Do we use numerical or analytic metric information |   "numerical" :: "Read in metric from numerical data"*OR*  "analytic"  :: "Use external analytic metric"*OR* |
| eh_lapse_type | "numerical" | Do we use numerical or analytic metric information |   "numerical" :: "Read in metric from numerical data"*OR*  "analytic"  :: "Use external analytic metric"*OR* |
| eh_shift_type | "numerical" | Do we use numerical or analytic metric information |   "numerical" :: "Read in metric from numerical data"*OR*  "analytic"  :: "Use external analytic metric"*OR* |
| file_type | "one_file" | Are the timesteps in separate files or in one file? |   "one_file" :: "All timesteps are in the same file"*OR*  "sep_time_files" :: "Timesteps are in separete files"*OR* |
| read_conformal_factor_once | "yes" | Should the conformal factor only be read once |  |
| cheat | "no" | Should we cheat and evolve using the last data set for a while? |  |
| cheat_iterations | 0 | For how many iterations should we cheat | 0:*            :: "If you really want to cheat this should be positive"*OR* |
| shell_width | 7.0 | Width of the evolution region in units of the grid spacing | 0.0:            :: "Positive please"*OR* |
| use_inner_excision | "yes" | Should inner excision be used? |  |
| use_outer_excision | "yes" | Should outer excision be used? |  |
| mode | "normal" | Mode of operation |   "normal"  :: "Find event horizons"*OR*  "analysis" :: "Provide storage for f without evolving"*OR*  "generator" :: "Provide storage for f and initialize without evolving"*OR* |
| upwind_type | "characteristic" | Type of upwinding used in evolving the ehfinder equations |   "intrinsic" :: "Use the values of f itself to determine upwind direction"*OR*  "shift"     :: "Use the shift to determine upwind direction"*OR*  "characteristic" :: "Use characteristic information"*OR* |
| surface_direction | "outward" | Should we track outward or inward moving surfaces |   "outward" :: "Track outward moving surfaces. Use for event horizon finding."*OR*  "inward"  :: "Track inward moving surfaces."*OR* |
| re_init_verbose | "no" | Should re-initialization be verbose? |  |
| re_init_undo | "yes" | Should re-initialization be undone at pinch-off |  |
| re_init_int_method | "euler" | Integration method used in re-initialization |   "euler"  :: "Standard euler scheme"*OR*  "rk2"    :: "Second order Runge-Kutta scheme"*OR* |
| re_init_max_iter | 800 | maximum number of iterations in the re-initialization | 0:        :: "Positive please"*OR* |
| pde_differences | "upwind2" | Type of finite diffencing used in pde re-initialization |   "centered" :: "Use 2nd order centered differences except at the boundaries"*OR*  "upwind"   :: "Use 1st order upwinded differences everywhere"*OR*  "upwind2"  :: "Use 2nd order upwinded differences everywhere"*OR* |
| re_initialize_every | 10 | How often to re-initialize the level set function | 0:               :: "If 0 don't re-initialize"*OR* |
| last_iteration_number | 0 | The starting iteration number for the EH_Finder (last iteration number of the simulation) | 0:              :: "Positive please"*OR* |
| saved_iteration_every | 1 | How often was the numerical data saved | 1:               :: "Positive please"*OR* |
| ntheta | 51 | Number of points in the theta direction when finding points on the surface | 1:*:2            :: "Positive and odd please"*OR* |
| nphi | 51 | Number of points in the phi direction when finding points on the surface | 1:*:2            :: "Positive and odd please"*OR* |
| n_array_ghosts | 1 | Number of ghost points in the surface grid array | 1:               :: "Positive please"*OR* |
| maximum_surface_number | 1 | The maximum number of surfaces expected in the data |   1:*            :: "Positive please"*OR* |
| use_user_center | "no" | Should the user prescribed center be used |  |
| center_x | 0.0 | The x-coordinate of the center |   *:*            :: "Anything"*OR* |
| center_y | 0.0 | The y-coordinate of the center |   *:*            :: "Anything"*OR* |
| center_z | 0.0 | The z-coordinate of the center |   *:*            :: "Anything"*OR* |
| evolve_generators | "no" | Should the generators be evolved |  |
| number_of_generators | 1 | How many generators should be evolved |   1:*            :: "Postive please"*OR* |
| number_of_generators_theta | 1 | How many generators in the theta direction |   1:*           :: "Positive please"*OR* |
| number_of_generators_phi | 1 | How many generators in the phi direction |   1:*           :: "Positive please"*OR* |
| generator_distribution | "line" | What initial distribution should be used |   "line"	:: "Put the generators on a line in the xz-plane"*OR*  "2D array"    :: "Put the generators on a surface with spherical topology"*OR* |
| surface_interpolator | "Hermite | What interpolator should be used to locate the surface |   ".+"          :: "A valid interpolator name"*OR* |
| surface_interpolation_order | 2 | What order should be used for the surface interpoation |   1:*           :: "A valid positive interpolation order"*OR* |
| area_interpolator | "Lagrange | What interpolator should be used for the area |   ".+"          :: "A valid interpolator name"*OR* |
| area_interpolation_order | 3 | What order should be used for the area interpoation |   1:*           :: "A valid positive interpolation order"*OR* |
| area_calculation_method | "standard" | How should the areas be calculated |   "standard"    :: "Using a angular coordinate system on the surface"*OR*  "isosurface"  :: "Using an isosurface triangulation"*OR* |
| generator_interpolator | "Lagrange | What interpolator should be used for the generators |   ".+"          :: "A valid interpolator name"*OR* |
| generator_interpolation_order | 3 | What order should be used for the generator interpoation |   1:*           :: "A valid positive interpolation order"*OR* |
| generator_tracking_method | "interpolate_before" | What method should be used for tracking the generators |   "interpolate_before" :: "Interpolate first, then calculate"*OR*  "interpolate_after"  :: "Calculate first, then interpolate"*OR* |
| EHFinder_MaxNumEvolvedVars | 1 | The maximum number of evolved variables used by EHFinder |   1:10		:: "Only evolve the level set functions"*OR* |
| EHFinder_Max_Evolved_Array_Size | 1 | The maximum size of evolved grid arrays used by EHFinder |   1:*		:: "The size of the generator grid arrays"*OR* |
| EHFinder_Num_ArrayEvolved_Vars | 3 | The maximum number of evolved grid arrays used by EHFinder |   0:30		:: "Should be exactly zero or a multiple of three"*OR* |


### AHFinder

Cactus Code Thorn AHFinder
Author(s)    : Miguel Alcubierre
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn looks for apparent horizons (AH) in 3D.  An AH is defined
as a surface where the expansion of outgoing null geodesics is zero.

NOTE:  THIS FILE IS SOMEWHAT OUT OF DATE.  I WILL UPDATE IT WHEN
I HAVE MORE TIME.  PLEASE LOOK AT THE FILE `MinimumAHF_param.h'
FOR A MORE UP TO DATA DESCRIPTION OF THE PARAMETERS.


BRIEF DESCRIPTION
-----------------

This thorn looks for apparent horizons (AH) in 3D using two
different algorithms.  In the default mode, it uses a minimization
algorithm.  It can also use a flow algorithm if one sets the
parameter "ahf_flow" equal to "yes":

ahf_flow = "yes"

The minimization algorithm is VERY slow if one has a lot of terms
in the spherical harmonics expansion (see below), but it is quite
fast for a small number of terms.

The thorn is activated by adding the following line to the parameter
file:

ahfinder::ahf_active      = "yes"

Also, one needs to tell the routine how often we want to
look for AH by using the parameter `mahf_findevery' (its
default value is 1).

The routine defines the grid function `mahfgrid' as:

ahfgrid  =  r  -  f(theta,phi)

So that the surface being considered corresponds to the zero level of
`mahfgrid'.  The function f(theta,phi) is expanded in spherical
harmonics Y(l,m).  Notice, however, that I DO NOT use the standard
normalization factor:

sqrt( (2l + 1) (l - m)! / 4 pi (l + m)! )

I use instead:

sqrt( 2l + 1 )                           m = 0

sqrt( 2 (2l + 1) (l - m)! / (l + m)! )   m != 0

Notice first that I leave out the 4*pi.  This is because I want Y(0,0)
to be the real radius of a sphere.  Also, for M non-zero I have an
extra sqrt(2).  I need this because I use a real basis with sines and
cosines instead of complex exponentials.


The minimization algorithm has three main modes of operation:

* Default mode: In the default mode, the routine tries to
find a minimum of the integral of the square of the expansion.
At an AH, this integral should be zero, and for any other
surface it will be positive.  One must remember that the
routine can easily end up in a local minimum that is not
a horizon.  To try to prevent this, the routine first
looks through a subset of the parameter space to try
to find a good guess for the global minimum.  This, of
course can fail.

* Area minimization mode:  This mode is activated by
setting the parameter:

mahf_minarea = "yes"

In this mode, the routine looks for a local minimum of the surface
area.  For time-symmetric data (and only then), such a minimum will
correspond to an AH.

* Trapped surface finding mode:  This mode is activated by
setting the parameter:

mahf_trapped_surface = "yes"

In this mode, the routine looks for a local minimum of the integral of
the square of the quantity (expansion + trapped_surface_delta).  If
trapped_surface_delta is positive, then the routine will try to find a
surface whose expansion is -(trapped_surface_delta) everywhere on the
surface.  One can monitor the number of interpolated points that have
a negative expansion by setting veryverbose = "yes", which indicates
whether or not a trapped surface has truly been found.

OUTPUT
------

The routine controls its own output.  It produces output of 2 grid
functions (mahfgrid, mahf_exp) on its own (so please don't add them to
the Cactus output).  The reason for this is that the finder might be
called at times that have nothing to do with when Cactus wants to do
output.

The grid function `mahfgrid' was described above.  The grid function
`mahf_exp' is the expansion of outgoing photons on the level sets of
`mahfgrid'.

The finder also produces a series of data files:

1) "mahf_coeff.alm"

This file contains the coefficients of the spherical
harmonics expansion of the surface found.  Notice
that the finder almost always finds a surface, but this
might not be a horizon, so be careful.

The format in which these coefficients are saved is the following:

* Lines beginning with # are comments. These include the time
  at which the finder was called, and a line stating if the
  surface found is a horizon or not.

* Empty lines indicate different calls to the finder (used when
  the finder is called many times during an evolution).

* The coefficients are saved using a loop of the form:

  do l=1,lmax
     do m=-l,l
        write(*,*) a(l,m),l,m
     end do
  end do

  Notice that because our functions are real, I use an expansion
  in sines and cosines and I really only consider positive values
  of m. On output my convention is that coefficients with positive
  m represent the terms:

  P_(l,m) cos(m theta)

  and coefficients with negative m represent the terms:

  P_(l,m) sin(m theta)

2) "mahf.gauss"

  This file contains a map of the gaussian curvature on the surface.
  The format of this file might still change in the near future.
  I need to talk to Werner about this.

* Lines beginning with # are comments.

* The data is written is a loop:
 
  do i=0,ntheta-1
     do j=0,nphi-1
        write gaussian(i,j)
     end do
  end do
 
  theta and phi are subdivided uniformly
  (according to grid type) in the intervals:
 
  octant:    theta=[0,pi/2]  phi=[0,pi/2]
  quadrant:  theta=[0,pi]    phi=[0,pi/2]
  full:      theta=[0,pi]    phi=[0,2 pi]

3) "mahf_area.tl"

   Area of the surface for each time the finder was called.
   Again, remember that the surface might not be a horizon.

4) "mahf_mass.tl"

   Mass of the surface for each time the finder was called.
   This is defined as:

   M = sqrt(A/(16 pi))

   Again, remember that the surface might not be a horizon.

5) "mahf_circ_eq.tl"

   Equatorial circumference of the surface.
   Again, remember that the surface might not be a horizon.

6) "mahf_meri_p1.tl"

   Length of meridian of surface for phi=0.
   Again, remember that the surface might not be a horizon.

7) "mahf_meri_p2.tl"

   Length of meridian of surface surface for phi=pi/2.
   Again, remember that the surface might not be a horizon.

8) "mahf_logfile"

   Log file for the last time the horizon was called.  The
   reason why only the last call is here is that this file
   is very long.


PARAMETERS
----------

Other parameters for the thorn are (see also file MinimumAHF_param.h):


STRINGS:

mahf_logfile     = [yes,no]      Write a log file.  If yes, the
                                 thorn produces the file "mahf_logfile".
                                 (default = "no")

mahf_verbose     = [yes,no]      Write messages to screen.
                                 (default = "yes")

mahf_veryverbose = [yes,no]      Write lots of messages to the screen,
                                 essentially the whole log file.
                                 (default = "no")

mahf_guessverbose = [yes,no]     Write a little info for each trial point
                                 calculated in the guess process.
                                 (default = "no")

mahf_2Doutput    = [yes,no]      2D output of grid functions?
                                 (default = "no")

mahf_3Doutput    = [yes,no]      3D output of grid functions?
                                 (default = "no")

mahf_mask        = [yes,no]      Use mask for definite horizons?
                                 (default = "no")

mahf_weakmask    = [yes,no]      Use mask for possible horizons?
                                 (default = "no")

mahf_phi         = [yes,no]      Expand in phi.  It is useful to switch
                                 this off if one knows in advance that
                                 the horizon is really axisymmetric.
                                 (default = "yes")

mahf_offset      = [yes,no]      Is the center offset from origin?
                                 (default = "no")

mahf_wander      = [yes,no]      Do we allow the center to wander?
                                 (default = "no")

mahf_refx        = [yes,no]      Do we have reflection symmetry x -> -x?
                                 (default = "no")

mahf_refy        = [yes,no]      Do we have reflection symmetry y -> -y?
                                 (default = "no")

mahf_refz        = [yes,no]      Do we have reflection symmetry z -> -z?
                                 (default = "no")

mahf_octant      = [yes,no,high] Does the surface have octant symmetry?
                                 The "high" option forces also reflection
                                 symmetry across the x-y diagonal.
                                 (default = "no")

mahf_areamap     = [yes,no]      Construct an area map?.
                                 (default = "no")

mahf_sloppyguess = [yes,no]      This considers only spheres for the
                                 initial guess. It is much faster.
                                 (default = "no")

mahf_guessold    = [yes,no]      Use horizon found in previous call as
                                 initial guess?  Only relevant for
                                 evolutions.
                                 (default = "no")

mahf_inner       = [yes,no]      Tries to look for inner horizon instead.
                                 (default = "no")

mahf_guess_absmin = [yes,no]     Use the absolute minimum of the surface
                                 integral of the square of the expansion
                                 as the place to start the minimization
                                 process.  This is useful if you have
                                 a very dynamical spacetime, with possibly
                                 many local minima and you don't have any
                                 idea where (or even if) you have an AH.
                                 (default = "no")

mahf_manual_guess = [yes,no]     Use a manually specified guess, via. the
                                 parameters mahf_l0_guess, mahf_l2_guess,
                                 etc. (works only in octant mode right now)
                                 (default = "no")

INTEGERS:

mahf_findafter                   After how many timesteps start looking
                                 for horizons (default 0).

mahf_findevery                   How often to look for horizons (iterations).
                                 (default 1)

mahf_maxiter                     Maximum number of iterations of
                                 Powell's minimization algorithm.
                                 (default 10)

mahf_flowiter                    Maximum number of iterations for flow
                                 (default 200)

mahf_lmax (<20)                  Number of terms in theta expansion
                                 (default 2)

mahf_ntheta                      Number of subdivisions in theta for
                                 surface integrals (default 200).

mahf_nphi                        Number of subdivisions in phi for
                                 surface integrals (default 200).

mahf_nn0                         Number of subdivisions of c0(0) for
                                 initial guess (default 10)

mahf_nn2                         Number of subdivisions of c0(2) for
                                 initial guess (default 10)


REALS:

mahf_findaftertime               After what time start looking for horizons.
                                 A non-zero value of this parameter overrides
                                 the paramter `mahf_findafter' above.
                                 (default 0.0)

mahf_r0                          Radius of initial sphere (0 forces largest
                                 possible sphere)
                                 (default 0.0)

mahf_xc                          x coordinate of center of expansion
                                 (default 0.0)

mahf_yc                          y coordinate of center of expansion
                                 (default 0.0)

mahf_zc                          z coordinate of center of expansion
                                 (default 0.0)

mahf_tol                         Fractional tolerance for minimization
                                 algorithm.  If we decrease by less than
                                 this in one iteration we are done.
                                 (default = 0.1)

trapped_surface_delta            In 'mahf_trapped_surface' mode, this 
                                 determines the expansion of the surface
                                 that one is looking for. Notice that a
                                 positive value of 'mahf_trapped_surface'
                                 will cause the finder to look for a 
                                 surface with expansion equal to MINUS
                                 this value everywhere.
                                 (default = 0.0)

mahf_flowa                       Alpha parameter for flow (default 0.01).

mahf_flowb                       Beta parameter for flow (default 0.01).

mahf_flowh                       Weight of H flow (default 0.0).

mahf_flowc                       Weight of C flow (default 1.0).

mahf_flown                       Weight of N flow (default 0.0).

mahf_flowtol                     Tolerance for flow (default 0.0001).

mahf_maskshrink                  Shrink factor for mask (default 0.9).


IMPORTANT:  Notice that the symmetry parameters refer to the surface
itself, and not to the grid.


2. Dependencies of the thorn

This thorn additionally requires thorns:  GenericAHF, util.
For testing purposes, one also needs:     analyticBH, exact.


3. Thorn distribution

This thorn is available to everyone.


4. Additional information



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| ahf_active | "no" | Activate AHFinder? |  |
| ahf_persists | "no" | Do the finder grid functions stay around? |  |
| ahf_ReportAlways | "no" | Report for all surfaces found (yes) or just for apparent horizons (no) |  |
| ahf_find3 | "no" | Searching for 3 surfaces? |  |
| ahf_trapped_surface | "no" | Minimize (expansion + delta) to find trapped surface? |  |
| ahf_findevery | 1 | How often to look for horizons | 1: :: "Set to 1 for searching each iteration"*OR* |
| ahf_findafter | 0 | After how many iterations look for horizons | 0: :: "Any positive integer"*OR* |
| ahf_findaftertime | 0.0 | After how much time look for horizons | 0.0: :: "Any positive real number. If non-zero overides ahf_findafter"*OR* |
| trapped_surface_delta | 0.0 | find (expansion = delta) surface |  : :: "Just a real number"*OR* |
| ahf_phi | "no" | Expand also in phi? (seach for non-axisymmetric surface) |  |
| ahf_offset | "no" | Center offset from origin? |  |
| ahf_wander | "no" | Allow the center to wander? |  |
| ahf_lmax | 8 | Maximum number of terms in theta expansion | 0:19 :: "Range from 0 to 19"*OR* |
| ahf_xc | 0.0 | x-coordinate of center of expansion |  : :: "Anything"*OR* |
| ahf_yc | 0.0 | y-coordinate of center of expansion |  : :: "Anything"*OR* |
| ahf_zc | 0.0 | z-coordinate of center of expansion |  : :: "Anything"*OR* |
| ahf_xc_0 | 0.0 | x-coordinate of center of expansion for first surface with find3 |  : :: "Anything"*OR* |
| ahf_yc_0 | 0.0 | y-coordinate of center of expansion for first surface with find3 |  : :: "Anything"*OR* |
| ahf_zc_0 | 0.0 | z-coordinate of center of expansion for first surface with find3 |  : :: "Anything"*OR* |
| ahf_xc_1 | 0.0 | x-coordinate of center of expansion for second surface with find3 |  : :: "Anything"*OR* |
| ahf_yc_1 | 0.0 | y-coordinate of center of expansion for second surface with find3 |  : :: "Anything"*OR* |
| ahf_zc_1 | 0.0 | z-coordinate of center of expansion for second surface with find3 |  : :: "Anything"*OR* |
| ahf_xc_2 | 0.0 | x-coordinate of center of expansion for third surface with find3 |  : :: "Anything"*OR* |
| ahf_yc_2 | 0.0 | y-coordinate of center of expansion for third surface with find3 |  : :: "Anything"*OR* |
| ahf_zc_2 | 0.0 | z-coordinate of center of expansion for third surface with find3 |  : :: "Anything"*OR* |
| ahf_ntheta | 100 | Number of subdivisions in theta |  1: :: "Any sensible integer"*OR* |
| ahf_nphi | 100 | Number of subdivisions in phi |  1: :: "Any sensible integer"*OR* |
| ahf_refx | "no" | Reflection symmetry x->-x? |  |
| ahf_refy | "no" | Reflection symmetry y->-y? |  |
| ahf_refz | "no" | Reflection symmetry z->-z? |  |
| ahf_cartoon | "no" | Cartoon mode? |  |
| ahf_octant | "no" | Octant symmetry? |   "no"   :: "No octant symmetry"*OR*  "yes"  :: "Octant symmetry: reflection symmetry on all three coordinate planes"*OR*  "high" :: "Octant symmetry + symmetry of rotation of pi/2 around z axis"*OR* |
| ahf_minarea | "no" | Minimize area instead of expansion? |  |
| ahf_maxiter | 10 | Maximum number of iterations of POWELL |  : :: "Any sensible integer value"*OR* |
| ahf_tol | 0.1 | Tolerance for minimization routines | 0: :: "A sensible positive number"*OR* |
| ahf_sloppyguess | "no" | Use sphere as initial guess? |  |
| ahf_guess_absmin | "no" | Use absolute min to start minimization? |  |
| ahf_guessold | "no" | Use old horizon as initial guess? |  |
| ahf_inner | "no" | Look for inner horizon? |  |
| ahf_manual_guess | "no" | Use specified coefficients for guess? |  |
| ahf_nn0 | 10 | Number of subdivisions of c0(0) for initial guess |  : :: "Some positive integer"*OR* |
| ahf_nn2 | 10 | Number of subdivisions of c0(2) for initial guess |  : :: "Some positive integer"*OR* |
| ahf_r0 | 0.0 | Radius of initial sphere (0 forces largest sphere) |  : :: "Anything"*OR* |
| ahf_r0_0 | 0.0 | Radius of first initial sphere for find3 (0 forces largest sphere) |  : :: "Anything"*OR* |
| ahf_r0_1 | 0.0 | Radius of second initial sphere for find3 (0 forces largest sphere) |  : :: "Anything"*OR* |
| ahf_r0_2 | 0.0 | Radius of third initial sphere for find3 (0 forces largest sphere) |  : :: "Anything"*OR* |
| ahf_flow | "no" | Use flow instead of minimization? |  |
| ahf_flowiter | 200 | Maximum number of iterations for flow | 0: :: "Anything"*OR* |
| ahf_flowa | 0.01 | alpha parameter for flow |  : ::  "Anything"*OR* |
| ahf_flowb | 0.5 | beta parameter for flow |  : :: "Anything"*OR* |
| ahf_flowh | 0.0 | Weight of H flow |  : :: "Anything"*OR* |
| ahf_flowc | 1.0 | Weight of C flow |  : :: "Anything"*OR* |
| ahf_flowtol | 0.0001 | Tolerance for flow |  : :: "Anything"*OR* |
| ahf_maxchange | 0.1 | Maximum relative difference between 1 big and 2 small steps |  : :: "Anything"*OR* |
| ahf_minchange | 0.01 | Minimum relative difference between 1 big and 2 small steps |  : :: "Anything"*OR* |
| ahf_logfile | "no" | Write log file? |  |
| ahf_verbose | "yes" | Print messages to screen? |  |
| ahf_veryverbose | "no" | Print messages at each iteration step to screen? |  |
| ahf_guessverbose | "no" | Print info on initial guess? |  |
| ahf_1Doutput | "no" | 1D output of grid functions? |  |
| ahf_2Doutput | "no" | 2D output of grid functions? |  |
| ahf_3Doutput | "no" | 3D output of grid functions? |  |
| ahf_HDF5output | "no" | HDF5 output of AHFinder data? |  |
| ahf_areamap | "no" | Find area map? |  |
| ahf_gaussout | "yes" | Output gaussian curvature of horizon? |  |
| ahf_mask | "off" | Use mask? | "off"    :: "Mask is off"*OR*"strong" :: "Mask set only for definite horizons"*OR*"weak"   :: "Mask set for both definite and probable horizons"*OR* |
| ahf_masktype | "cube" | Type of mask | "lego" :: "Mask is a lego sphere"*OR*"cube" :: "Mask is a cube"*OR*"poly" :: "Mask is a polyhedra"*OR* |
| ahf_mask_time | -1.0 | Time after which to start setting the mask | : :: "Anything goes. Negative number means setting the mask as soon as possible"*OR* |
| ahf_mask_0 | "yes" | Mask for first horizon with find3? |  |
| ahf_mask_1 | "yes" | Mask for second horizon with find3? |  |
| ahf_mask_2 | "yes" | Mask for third horizon with find3? |  |
| ahf_maskbuffer | 5 | Number of grid points in mask buffer zone | 0 :: "Positive please"*OR* |
| ahf_maskshrink | 0.8 | Shrink factor for mask | 0.0:1.0 :: "Must be positive and not larger than 1"*OR* |
| ahf_shiftcoeff | 0.0 | Coefficient for shift | : :: "Anything goes"*OR* |
| interpolation_order | 2 | Order for interpolation |   1:4 :: "Choose between first and fourth order interpolation"*OR* |
| interpolation_operator | "uniform | Name of interpolation operator to use |   ".+" :: "A valid name for a registered interpolation operator"*OR* |


### Hydro_Analysis

Cactus Code Thorn Hydro_Analysis
Author(s)    : Frank Löffler
Maintainer(s): Einstein Toolkit maintainers
Licence      : LGPLv2+
--------------------------------------------------------------------------

1. Purpose

This thorn provides basic hydro analysis routines.



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| Hydro_Analysis_comp_rho_max | "false" | Look for the value and location of the maximum of rho |  |
| Hydro_Analysis_comp_rho_max_every | 1 | How often to look  for the value and location of the maximum of rho |   0:0 :: "Never"*OR*  1:* :: "every so often"*OR* |
| Hydro_Analysis_average_multiple_maxima_locations | "false" | when finding more than one global maximum location, average position and use result |  |
| verbosity_level | 1 | how much information to ouptut to the logs |   0 :: "Output nothing"*OR*  1 :: "warn when finding multiple maxima"*OR*  2 :: "also output location of maxima" *OR* |
| Hydro_Analysis_rho_max_loc_only_positive_x | "false" | Restrict location search for density maximum to positive values of x |  |
| Hydro_Analysis_rho_max_loc_use_rotatingsymmetry180 | "false" | Map found maxima into positive x half-plane assuming pi-symmetry |  |
| Hydro_Analysis_comp_vol_weighted_center_of_mass | "false" | Look for the location of the volume-weighted center of mass |  |
| Hydro_Analysis_comp_rho_max_origin_distance | "false" | Look for the proper distance between the maximum of the density and the origin (along a straight coordinate line) |  |
| Hydro_Analysis_rho_max_origin_distance_npoints | 100 | Number of points along the straight line for measuring proper distance |   1:* :: "Any positive number"*OR* |
| Hydro_Analysis_interpolator_name | "uniform | Name of the interpolator |   "Lagrange polynomial interpolation (tensor product)" :: "from AEILocalInterp"*OR*  "Lagrange polynomial interpolation (maximum degree)" :: "from AEILocalInterp"*OR*  "Hermite polynomial interpolation" :: "from AEILocalInterp"*OR*  "uniform cartesian" :: "from LocalInterp"*OR*  ".*" :: "must be a registered interpolator"*OR* |
| Hydro_Analysis_interpolator_options | "order=2" | Options for the interpolator |   ".*" :: "must be a valid option specification"*OR* |
| Hydro_Analysis_interpolator_coordinates | "cart3d" | Coordinate system |   ".*" :: "must be a registered coordinate system"*OR* |
| Hydro_Analysis_comp_core_rho_centroid | "no" | compute location of the centroid of rho*x in region r_core around densest point |  |
| Hydro_Analysis_r_core | 4.0 | size of region around densest point in which to compute the centroid of rho*x |   0:* :: "any positive radius. suggested is 1/2 of stellar radius"*OR* |
| Hydro_Analysis_core_rho_rel_min | 0.01 | only include points where rho>rho_rel_min*rho_max when computing centroid of rho*x around densest point |   0:* :: "any positive value. For best results should be such that the region selected is smaller than r_core. 1/10 of rho_max should be fine."*OR* |


### ADMAnalysis

Cactus Code Thorn ADMAnalysis
Author(s)    : Tom Goodale
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn does basic analysis of the metric and extrinsic curvature 
tensors.

It calculates

  the trace of the extrinsic curvature ({\bf trK})

  the determinant of the metric ({\bf detg})

  the components of the metric in spherical coordinates 
  (grr,grq,grp,gqq,gqp,gpp)

  the components of the extrinsic curvature in spherical coordinates 
  (Krr,Krq,Krp,Kqq,Kqp,Kpp)

  components of the Ricci tensor
  
  the Ricci scalar

if output is requested for them.

(q refers to the theta coordinate and p to the phi coordinate.)

If the parameter 'normalize_dtheta_dphi' is true, the thorn 
projects the spherical components onto (r*dtheta, r*sin(theta)*dphi)
instead of the default vector (dtheta,dphi).

This thorn knows how to handle 'physical' and 'static conformal'
metric types.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| normalize_dtheta_dphi | "no" | Project angular components onto r*dtheta and r*sin(theta)*dphi? |  |
| ricci_persist | "no" | Keep storage of  the  Ricci tensor and scalar around? |  |
| ricci_timelevels | 1 | Number of time levels for the Ricci tensor and scalar |   1:3 :: ""*OR* |
| ricci_prolongation_type | "none" | The kind of boundary prolongation for the Ricci tensor and scalar |   "Lagrange" :: "standard prolongation (requires several time levels)"*OR*  "copy"     :: "use data from the current time level (requires only one time level)"*OR*  "none"     :: "no prolongation (use this if you do not have enough time levels active)"*OR* |


### QuasiLocalMeasures

error| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "no" | Produce log output while running |  |
| veryverbose | "no" | Produce much log output while running |  |
| num_surfaces | 1 | Number of surfaces |   0:100 :: ""*OR* |
| coordsystem | "cart3d" | The coordinate system to use |   "" :: "must be a registered coordinate system"*OR* |
| interpolator | "Lagrange | The interpolator to use |   "" :: "must be a registered interpolator"*OR* |
| interpolator_options | "order=2" | Options for the interpolator |   "" :: "must be a valid options specification"*OR* |
| spatial_order | 2 | Order of spatial differencing |   2 :: "second order"*OR*  4 :: "fourth order"*OR* |
| killing_vector_method | "eigenvector" | Method for finding the Killing vector field |   "axial"       :: "Assume that d/dphi is a Killing vector"*OR*  "eigenvector" :: "Solve the Killing vector equation as eigenvector equation"*OR*  "gradient"    :: "Calculate the normal to the gradient of a scalar"*OR* |
| killing_vector_normalisation | "average" | Method for normalising the Killing vector field |   "average" :: "Average several integral lines"*OR*  "median"  :: "Use the median integral line"*OR* |
| output_vtk_every | 0 | Output a VTK file with the main 2D |   0   :: "don't output VTK file"*OR*  1:* :: "output every so many iterations"*OR* |


### ADMMass

Cactus Code Thorn ADMMass
Author(s)    : Frank Löffler
Maintainer(s): Frank Löffler 
Licence      : GPLv2+
--------------------------------------------------------------------------

1. Purpose

This thorn can be used to calculate different approximations of the ADM mass
on a finite grid.

It requires C99 to compile. For gcc or icc, add -std=c99 to the compiler
options.

Copyright
---------

This thorn is copyright (C) 2005
by Frank Loeffler <frank.loeffler@aei.mpg.de>

This thorn is free software; you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation; either version 2 of the License, or
(at your option) any later version.

This thorn is distributed in the hope that it will be useful, but
WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this thorn (see the file COPYING in this directory);
if not, write to the Free Software Foundation, Inc., 59 Temple
Place, Suite 330, Boston, MA  02111-1307  USA



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| ADMMass_number | 1 | number of measurements |   0: :: "0 or positive"*OR* |
| ADMMass_use_surface_distance_as_volume_radius | "yes" | Use ADMMass_surface_distance instead of ADMMass_volume_radius |  |
| ADMMass_use_all_volume_as_volume_radius | "no" | Use the whole grid for volume integration |  |
| ADMMass_Excise_Horizons | "no" | Should we exclude the region inside the AH to the volume integral |  |
| ADMMass_Debug | "no" | Enable some info at runtime |  |


### Multipole


=========
Multipole
=========

=============    ==============================================================================================
Authors          Ian Hinder and Andrew Knapp
Maintainer       Ian Hinder
Licence          GNU GPL version 2
Documentation    http://einsteintoolkit.org/documentation/ThornGuide/ThornGuidech133.html#x149-2383000O9
Download         svn checkout https://svn.einsteintoolkit.org/cactus/EinsteinAnalysis/Multipole/trunk Multipole
=============    ==============================================================================================

---------------------
Purpose of This Thorn
---------------------

The Multipole thorn performs spherical harmonic mode decomposition of
Cactus grid functions on coordinate spheres.  It can decompose
multiple grid functions with any spin-weight on multiple spheres.
This thorn uses the interpolator interface to access grid functions,
so works with mesh-refinement and multi-patch.

---------------
Required thorns
---------------

* An interpolator, e.g. AEILocalInterp

--------------
Related thorns
--------------

* WeylScal4 can be used to compute the Weyl scalars, which can then be
  decomposed into modes on coordinate spheres by Multipole.

------------
Publications
------------

Multipole has been used in the following publications:

---------
Copyright
---------

This thorn is copyright (C) 2007-2011 (C) by Ian Hinder and Andrew
Knapp.

This thorn is free software; you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation; either version 2 of the License, or
(at your option) any later version.

This thorn is distributed in the hope that it will be useful, but
WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this thorn (see the file COPYING in this directory);
if not, write to the Free Software Foundation, Inc., 59 Temple
Place, Suite 330, Boston, MA  02111-1307  USA


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| interpolator_name | "Hermite | Which interpolator should I use |     ".+" :: "Any nonempty string"*OR* |
| interpolator_pars | "order=3 | Parameters for the interpolator |     ".*" :: "Any string that Util_TableSetFromString() will take"*OR* |
| coord_system | "cart3d" | What is the coord system? |     ".*" :: "Any smart string will do"*OR* |
| integration_method | "midpoint" | How to do surface integrals |   "midpoint"      :: "Midpoint rule (1st order)"*OR*  "Simpson"       :: "Simpson's rule (4th order) [requires even ntheta and nphi]"*OR*  "trapezoidal"   :: "Trapezoidal rule (2nd order)"*OR*  "DriscollHealy" :: "Driscoll & Healy (exponentially convergent) [requires even ntheta]"*OR* |
| out_dir | "" | Output directory for Extract's files, overrides IO::out_dir |   ".+" :: "A valid directory name"*OR*  "^$" :: "An empty string to choose the default from IO::out_dir"*OR* |
| out_every | 1 | How often to output |   0   :: "no output"*OR*  1:* :: "output every to many iterations"*OR* |
| out_1d_every | 0 | How often to output 1d data |   0   :: "no output"*OR*  1:* :: "output every to many iterations"*OR* |
| nradii | 1 | How many extraction radii? |   0:100 :: ""*OR* |
| ntheta | 50 | How many points in the theta direction? |   0:* :: "Positive please"*OR* |
| nphi | 100 | How many points in the phi direction? |   1:* :: ""*OR* |
| variables | "" | What variables to decompose |     ".*" :: "A list of variables"*OR* |
| verbose | "no" | Output detailed information about what is happening |  |
| l_max | 2 | The maximum l mode to extract |   0:* :: "l >= 0"*OR* |
| enable_test | "no" | whether to set a spherical harmonic in the 'harmonic' grid functions |  |
| test_l | 2 | which mode to put into the test variables |   * :: "Any integer"*OR* |
| test_m | 2 | which mode to put into the test variables |   * :: "Any integer"*OR* |
| test_sw | -2 | which spin weight to put into the test variables |   * :: "Any integer"*OR* |
| test_mode_proportional_to_r | "no" | whether the test spherical harmonic coefficient is proportional to the radial coordinate |  |
| mode_type | "deprecated" | Which type of mode extraction do we have |   "all modes"     :: "Extract all modes up to (l_mode, m_mode)."*OR*  "specific mode" :: "Select one specific (l_mode, m_mode) mode"*OR*  "deprecated"    :: "Deprecated"*OR* |
| l_min | -1 | all modes: above which l mode to calculate/ specific mode: which l mode to extract |   -1:* :: "Deprecated"*OR* |
| l_mode | -1 | The maximum l mode to extract |   -1:* :: "Deprecated"*OR* |
| m_mode | -100 | all modes: Up to which m mode to calculate/ specific mode: which m mode to extract  |   -100:* :: "Deprecated"*OR* |
| output_ascii | "yes" | Output a simple ASCII file for each mode at each radius |  |
| output_hdf5 | "no" | Output an HDF5 file for each variable containing one dataset per mode at each radius |  |
| hdf5_chunk_size | 200 | How many iterations to preallocate in extensible HDF5 datasets |   1:* :: "Any integer"*OR* |


### Extract

Cactus Code Thorn Extract
Author(s)    : Gabrielle Allen	
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Calculate gauge invariant waveforms, using the Moncrief formalism,
for cases when the spacetime approximates spherical symmetry.

Calculate other quantities on a surface, such as ADM mass, momenta,
and spins.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| timecoord | "both" | Which time coordinate to use |   "proper" :: ""*OR*  "coordinate" :: ""*OR*  "both" :: ""*OR* |
| all_modes | "yes" | Extract: all l,m modes up to l |  |
| Cauchy | "no" | Do Cauchy data extraction at given timestep |  |
| verbose | "no" | Say what is happening |  |
| doADMmass | "no" | Calculate ADM mass at extraction radii |  |
| do_momentum | "no" | Calculate momentum at extraction radii |  |
| do_spin | "no" | Calculate spin at extraction radii |  |
| itout | 1 | How often to extract, in iterations |  0:* :: ""*OR* |
| l_mode | 2 | l mode | 0:* :: ""*OR* |
| m_mode | 0 | m mode (ignore if extracting all modes | 0:* :: ""*OR* |
| Nt | 100 | Number of theta divisions | 0:* :: ""*OR* |
| Np | 100 | Number of phi divisions | 0:* :: ""*OR* |
| Cauchy_timestep | 0 | Timestep for Cauchy data extraction | 0:* :: ""*OR* |
| num_detectors | 0 | Number of detectors | 0:* :: ""*OR* |
| detector1 | 5.0 | Coordinate radius of detector 1 | 0:* :: ""*OR* |
| detector2 | 5.0 | Coordinate radius of detector 2 | 0:* :: ""*OR* |
| detector3 | 5.0 | Coordinate radius of detector 3 | 0:* :: ""*OR* |
| detector4 | 5.0 | Coordinate radius of detector 4 | 0:* :: ""*OR* |
| detector5 | 5.0 | Coordinate radius of detector 5 | 0:* :: ""*OR* |
| detector6 | 5.0 | Coordinate radius of detector 6 | 0:* :: ""*OR* |
| detector7 | 5.0 | Coordinate radius of detector 7 | 0:* :: ""*OR* |
| detector8 | 5.0 | Coordinate radius of detector 8 | 0:* :: ""*OR* |
| detector9 | 5.0 | Coordinate radius of detector 9 | 0:* :: ""*OR* |
| origin_x | 0.0 | x-origin to extract about | *:* :: ""*OR* |
| origin_y | 0.0 | y-origin to extract about | *:* :: ""*OR* |
| origin_z | 0.0 | z-origin to extract about | *:* :: ""*OR* |
| Cauchy_r1 | 1.0 | First radius for Cauchy data extraction | *:* :: ""*OR* |
| Cauchy_dr | 0.2 | Gridspacing for Cauchy data extraction | *:* :: ""*OR* |
| interpolation_operator | "uniform | Interpolation operator to use (check LocalInterp) |   .+ :: ""*OR* |
| interpolation_order | 1 | Order for interpolation |   1:4 :: "Choose between first and forth order interpolation"*OR* |


### Outflow

Cactus Code Thorn Outflow
Thorn Author(s)     : Roland Haas roland.haas@physics.gatech.edu
Thorn Maintainer(s) : Roland Haas roland.haas@physics.gatech.edu
                      Tanja Bode  tanja.bode@uni-tuebingen.de
--------------------------------------------------------------------------

Purpose of the thorn:
Calculate the flux across a spherical surface.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| compute_every | 1 | How frequently to compute the outflow |   1:* :: "number of iterations"*OR* |
| num_detectors | 0 | how many detectors do we have |   0:* :: "number of detectors"*OR* |
| coord_system | "cart3d" | What is the coord system? |   ".*" :: "Any smart string will do"*OR* |
| interpolator_name | "Hermite | Which interpolator should I use |   ".+" :: "Any nonempty string"*OR* |
| interpolator_pars | "order=3" | Parameters for the interpolator |   ".*" :: "Any string that Util_TableSetFromString() will take"*OR* |
| verbose | 1 | How verbose do you want it? |   0:* :: "0 is nothing, 1 is interesting, >1 is crazy"*OR* |
| out_format | ".19g" | Which format for Scalar floating-point number output |   ^(\.[1]?[0-9])?[EGefg]$ :: "output with given precision in exponential / floating point notation"*OR* |
| output_2d_data | "no" | output 2d data on spherical surface |  |
| output_relative_coordinates | "no" | output coordinates on spheroid relative to sf_centroid, absolute coordinates otherwise |  |
| extra_variables | "" | extra (scalar) variables to project onto the spherical surface |  ".*" :: "Any Cactus variables"*OR* |
| threshold_on_var | "w_lorentz" | Which variable to use as threshold |     "eninf"     :: "(Approximate) specific energy at infinity, - u_t - 1"*OR*    "w_lorentz" :: "Lorentz factor"*OR* |
| num_thresholds | 0 | how many thresholds to use |   0    :: "ignore thresholds, output only one flux"*OR*  1:20 :: "how many to use"*OR* |


## CactusExamples

### HelloWorld

Cactus Code Thorn HelloWorld
Author(s)    : Cactus team
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Hello World example for Cactus, possibly the simplest thorn ever.



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### IDWaveMoL

Cactus Code Thorn IDWaveMoL
Author(s)    : Ian Hawke
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Provides initial data for the WaveMoL thorn.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| initial_data | "gaussian" | Type of initial data |   "gaussian"   :: "Gaussian wave"*OR*  "plane"      :: "plane wave"*OR* |
| amplitude | 1.0 | The amplitude of the waves |   *:* :: "No restriction"*OR* |
| radius | 0.0 | The radius of the gaussian wave |  0:* :: "Positive"*OR* |
| sigma | 0.1 | The sigma for the gaussian wave |  0:* :: "Positive"*OR* |
| centrex | 0.0 | The centre of the gaussian wave, x-direction |   *:* :: "Anything"*OR* |
| centrey | 0.0 | The centre of the gaussian wave, y-direction |   *:* :: "Anything"*OR* |
| centrez | 0.0 | The centre of the gaussian wave, z-direction |   *:* :: "Anything"*OR* |
| kx | 0.0 | Wave number in the x-direction |   *:* :: "Anything"*OR* |
| ky | 0.0 | Wave number in the y-direction |   *:* :: "Anything"*OR* |
| kz | 0.0 | Wave number in the z-direction |   *:* :: "Anything"*OR* |
| offsett | 0.0 | Wave offset in the time-direction |   *:* :: "Anything"*OR* |
| offsetx | 0.0 | Wave offset in the x-direction |   *:* :: "Anything"*OR* |
| offsety | 0.0 | Wave offset in the y-direction |   *:* :: "Anything"*OR* |
| offsetz | 0.0 | Wave offset in the z-direction |   *:* :: "Anything"*OR* |
| slopet | 0.0 | Wave slope in the time-direction |   *:* :: "Anything"*OR* |
| slopex | 0.0 | Wave slope in the x-direction |   *:* :: "Anything"*OR* |
| slopey | 0.0 | Wave slope in the y-direction |   *:* :: "Anything"*OR* |
| slopez | 0.0 | Wave slope in the z-direction |   *:* :: "Anything"*OR* |


### HelloWorldCUDA

Cactus Code Thorn HelloWorldCUDA
Author(s)    : Erik Schnetter <eschnetter@perimeterinstitute.ca>
Maintainer(s): Erik Schnetter <eschnetter@perimeterinstitute.ca>
Licence      : GPL
--------------------------------------------------------------------------

1. Purpose

"Hello, World!" implemented in CUDA.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### TimerInfo

Cactus Code Thorn TimerInfo
Author(s)    : Cactus team
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

At the moment just prints out the value of all timers at chosen
iteration intervals. Will be worked into a more useful thorn to 
report of different kinds of timing information in different ways


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| every | 0 | How often to print information |  0:* :: "Positive"*OR* |
| clock | "all" | Which clock to use |  .* :: "Anything"*OR* |


### WaveToy2DF77

Cactus Code Thorn WaveToy2DF77
Author(s)    : Gabrielle Allen
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn illustrates the solution of the scalar wave equation in 2D using
centered explicit finite differencing. Included are routines to
NOTE: This thorn isn't working properly yet for multiple processors, it 
      will be fixed very soon in the development version.

 * Set up and register the 2D spatial coordinate system
 * Calculate initial data
 * Evolve using a standard explicit method

The Cactus infrastructure used includes

 * Boundary conditions  (CactusBase/Boundary)
 * Timestep calculation (CactusBase/Time)
 * Parallel driver      (CactusPUGH/PUGH)
 * IO			(Standard IO thorns)



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### WaveToy1DF77

Cactus Code Thorn WaveToy1DF77
Author(s)    : Gabrielle Allen
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn illustrates the solution of the scalar wave equation in 1D using
centered explicit finite differencing. Included are routines to

 * Set up and register the 1D spatial coordinate system
 * Calculate initial data
 * Evolve using a standard explicit method

The Cactus infrastructure used includes

 * Boundary conditions  (CactusBase/Boundary)
 * Timestep calculation (CactusBase/Time)
 * Parallel driver      (CactusPUGH/PUGH)
 * IO			(Standard IO thorns)



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### WaveToyOpenCL

Cactus Code Thorn WaveToyOpenCL
Author(s)    : Erik Schnetter <eschnetter@perimeterinstitute.ca>
Maintainer(s): Erik Schnetter <eschnetter@perimeterinstitute.ca>
Licence      : GPL
--------------------------------------------------------------------------

1. Purpose

Scalar wave equation implemented in OpenCL.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "no" | Output progress information |  |
| wavelength | 1.0 | Wavelength of initial data |   *:* :: ""*OR* |


### SampleIO

Cactus Code Thorn SampleIO
Author(s)    : Thomas Radke
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn provides a very simple I/O method which prints the values of
three-dimensional Cactus grid functions and arrays at a chosen location
(selected by parameters) to screen.

The I/O method makes use of the Cactus Hyperslabbing API to obtain the data
values to print.

The code in this thorn is well documented and can be used as an example to 
build your own I/O method.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| out_every | -1 | How often to do SampleIO output, overrides IO::out_every |    1:* :: "Every so many iterations"*OR*   0:  :: "Disable SampleIO output"*OR*  -1:  :: "Choose the default from IO::out_every"*OR* |
| out_vars | "" | Variables to output by SampleIO |   ".+" :: "Space-separated list of fully qualified variable/group names"*OR*  "^$" :: "An empty string to output nothing"*OR* |
| point_x | 0 | x-index (starting from 0) locating the array element to output |   0:* :: "An index between [0, nx)"*OR* |
| point_y | 0 | y-index (starting from 0) locating the array element to output |   0:* :: "An index between [0, ny)"*OR* |
| point_z | 0 | z-index (starting from 0) locating the array element to output |   0:* :: "An index between [0, nz)"*OR* |


### Poisson

Cactus Code Thorn Poisson
Author(s)    : Erik Schnetter <schnetter@gmail.com>
Maintainer(s): Erik Schnetter <schnetter@gmail.com>
Licence      : GPL
--------------------------------------------------------------------------

1. Purpose

Solve the Poisson equation with the TATelliptic framework.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| solver | "TATJacobi" | Name of TATelliptic solver that should be used |   .* :: "must be an activated TATelliptic solver"*OR* |
| options | "" | Options for the solver |   .* :: "no restriction"*OR* |
| radius | 1.0 | Radius of uniformly charged sphere |   0:* :: ""*OR* |
| charge | 1.0 | Charge of uniformly charged sphere |   *:* :: ""*OR* |


### WaveMoL

Cactus Code Thorn WaveMoL
Author(s)    : Ian Hawke
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This implements the wave equation in FOSH form for use with the Method
of Lines thorn. Intended as a basic test. 


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| WaveMoL_MaxNumEvolvedVars | 5 | The maximum number of evolved variables used by WaveMoL |   5:5		:: "Just 5: phi and the four derivatives"*OR* |
| WaveMoL_MaxNumConstrainedVars | 1 | The maximum number of constrained variables used by WaveMoL |   1:1		:: "The energy"*OR* |
| bound | "none" | Type of boundary condition to use |   "none"      :: "No boundary condition"*OR*  "flat"      :: "Flat boundary condition"*OR*  "radiation" :: "Radiation boundary condition"*OR* |


### DemoInterp

Cactus Code Thorn DemoInterp
Author(s)    : Thomas Radke
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn demonstrates the use of the Cactus interpolation API.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| interpolation_order | 1 | Order for interpolation |   1:3 :: "Choose between first, second, and third-order"*OR* |


### HelloWorldOpenCL

Cactus Code Thorn HelloWorldOpenCL
Author(s)    : Erik Schnetter <eschnetter@perimeterinstitute.ca>
Maintainer(s): Erik Schnetter <eschnetter@perimeterinstitute.ca>
Licence      : GPL
--------------------------------------------------------------------------

1. Purpose

"Hello, World!" implemented in OpenCL.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### SampleBoundary

Cactus Code Thorn SampleBoundary
Author(s)    : Carsten Gundlach
               David Rideout
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn provides a simple example of how to create and register a
physical boundary condition.  It implements a linear extrapolation
boundary condition in three dimensions.  



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### FleshInfo

Cactus Code Thorn FleshInfo
Author(s)    : Cactus team
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Demonstrates obtaining information about computational infrastructure
from the flesh.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


## EinsteinBase

### ADMMacros

Cactus Code Thorn ADMMacros
Author(s)    : Tom Goodale
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn provides nothing but a load of macros for other thorns to use


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| spatial_order | 2 | Order of spatial differencing | 2 :: "2nd order finite differencing"*OR*4 :: "4th order finite differencing"*OR* |


### CoordGauge

Cactus Code Thorn CoordGauge
Author(s)    : Tom Goodale
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Base thorn to provide the infrastructure for dynamic gauge selection.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| lapse_list | "" | List of allowed lapses to use |   ".*" :: "Any string"*OR* |
| shift_list | "" | List of allowed shifts to use |   ".*" :: "Any string"*OR* |
| slicing_verbose | "no" | Print information on current slicing |   "yes" :: "print slicing info"*OR*  "no"  :: "do not print slicing info"*OR* |


### StaticConformal

Cactus Code Thorn StaticConformal
Author(s)    : Tom Goodale
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn provides the variables defining a static conformal factor
which is used to transform the physical metric.  If this thorn is
active and the ADMBase::metric_type parameter is set to
`static conformal' then the ADMBase::g... variables are the
conformal values as opposed to the physical values.

The transformation is

$$ g_{ij}^{\mbox{physical}} = \psi^4 g_{ij}^{\mbox{conformal}} $$

The extrinsic curvature is not transformed.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| conformal_storage | "factor+derivs+2nd | How much conformal storage do we have ? |   "factor"                   :: "Just the conformal factor"*OR*  "factor+derivs"            :: "Conformal factor plus first derivatives"*OR*  "factor+derivs+2nd derivs" :: "Conformal factor plus first and second derivatives"*OR* |


### Constants

Cactus Code Thorn Constants
Author(s)    : Frank Löffler
Maintainer(s): Frank Löffler
Licence      : GPLv2+
--------------------------------------------------------------------------

1. Purpose

This thorn provides some constants which are defines.
If you do not find the constant you do want to use, please feel free to
include that.



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### TmunuBase

Cactus Code Thorn TmunuBase
Author(s)    : Erik Schnetter
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn provides grid functions for the stress-energy tensor.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| stress_energy_storage | no | Should the stress-energy tensor have storage? |  |
| stress_energy_at_RHS | no | Should the stress-energy tensor be calculated for the RHS evaluation? |  |
| support_old_CalcTmunu_mechanism | no | Should the old CalcTmunu.inc mechanism be supported? This is deprecated. |  |
| timelevels | 1 | Number of time levels |   0:3 :: ""*OR* |
| prolongation_type | "Lagrange" | The kind of boundary prolongation for the stress-energy tensor |   "^Lagrange$" :: "standard prolongation (requires several time levels)"*OR*  "^none$"     :: "no prolongation (use this if you do not have enough time levels active)"*OR*  ""           :: "any other supported prolongation type"*OR* |


### ADMBase

Cactus Code Thorn ADMBase
Author(s)    : Tom Goodale
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn provides the basic variables used to communicate between
thorns doing General Relativity in the 3+1 formalism.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| initial_data | "Cartesian | Initial metric and extrinsic curvature datasets |   "Cartesian Minkowski" :: "Minkowski values in cartesian coordinates"*OR* |
| initial_lapse | "one" | Initial lapse value |   "one" :: "Uniform lapse"*OR* |
| initial_shift | "zero" | Initial shift value |   "none" :: "Shift is inactive"*OR*  "zero" :: "Shift is zero"*OR* |
| initial_dtlapse | "none" | Initial dtlapse value |   "none"   :: "Dtlapse is inactive"*OR*  "zero"   :: "Dtlapse is zero"*OR* |
| initial_dtshift | "none" | Initial dtshift value |   "none"   :: "Dtshift is inactive"*OR*  "zero"   :: "Dtshift is zero"*OR* |
| evolution_method | "static" | The metric an extrinsic curvature evolution method |   "none"       :: "The metric and extrinsic curvature are not evolved"*OR*  "static"     :: "The metric and extrinsic curvature are not evolved"*OR*  "ID-apply-regrid" :: "The metric and extrinsic curvature are not evolved and initial data is used to fill in new grid points after regridding"*OR*  "ID-apply-always" :: "The metric and extrinsic curvature are not evolved and initial data is used to fill in new grid points before each step and after grid changes"*OR* |
| lapse_evolution_method | "static" | The lapse evolution method |   "static"     :: "lapse is not evolved"*OR*  "ID-apply-regrid" :: "lapse is not evolved and initial data is used to fill in new grid points after regridding"*OR*  "ID-apply-always" :: "lapse is not evolved and initial data is used to fill in new grid points before each step and after grid changes"*OR* |
| shift_evolution_method | "static" | The shift evolution method |   "static"     :: "dtlapse is not evolved"*OR*  "ID-apply-regrid" :: "dtlapse is not evolved and initial data is used to fill in new grid points after regridding"*OR*  "ID-apply-always" :: "dtlapse is not evolved and initial data is used to fill in new grid points before each step and after grid changes"*OR* |
| dtshift_evolution_method | "flat" | The dtshift evolution method |   "" :: "must be a registered boundary condition"*OR* |
| metric_type | "physical" | The semantics of the metric variables (physical, static conformal, etc) |   "physical" :: "metric and extrinsic curvature are the physical ones"*OR* |
| lapse_prolongation_type | "Lagrange" | The kind of boundary prolongation for the lapse |   "Lagrange" :: "standard prolongation (requires several time levels)"*OR*  "none"     :: "no prolongation (use this if you do not have enough time levels active)"*OR* |
| shift_prolongation_type | "Lagrange" | The kind of boundary prolongation for the shift |   "Lagrange" :: "standard prolongation (requires several time levels)"*OR*  "none"     :: "no prolongation (use this if you do not have enough time levels active)"*OR* |
| metric_prolongation_type | "Lagrange" | The kind of boundary prolongation for the metric and extrinsic curvature |   "Lagrange" :: "standard prolongation (requires several time levels)"*OR*  "none"     :: "no prolongation (use this if you do not have enough time levels active)"*OR* |
| lapse_timelevels | 1 | Number of time levels for the lapse |   0:3 :: ""*OR* |
| shift_timelevels | 1 | Number of time levels for the shift |   0:3 :: ""*OR* |
| metric_timelevels | 1 | Number of time levels for the metric and extrinsic curvature |   0:3 :: ""*OR* |


### ADMCoupling

Cactus Code Thorn ADMCoupling
Author(s)    : Ian Hawke
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

The "friend of a friend" thorn to help matter / spacetime coupling. Does nothing itself.



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### EOS_Base

Cactus Code Thorn EOS_Base
Author(s)    : Tom Goodale
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn provides a registration mechanism for various EOS.

These EOS can then be used by hydrodynamical routines.

Usage:

Creation of EOS:

A thorn providing an EOS should register that it does so by creating
an EOS handle with

int EOS_RegisterMethod(const char *name)

which returns a handle by which this EOS is referenced.

This handle can also be obtained at any time by calling

int EOS_Handle(const char *name)

Once the handle is created it can be used to register individual EOS
functions.  There are five of these

EOS_RegisterPressure         (int handle, CCTK_REAL (*func)(CCTK_REAL, CCTK_REAL))

   registers a function which returns pressure 
   as a function of rest mass density and specific internal energy

EOS_RegisterSpecificIntEnergy(int handle, CCTK_REAL (*func)(CCTK_REAL, CCTK_REAL))

   registers a function which returns specific internal energy  
   as a function of rest mass density and pressure

EOS_RegisterRestMassDens     (int handle, CCTK_REAL (*func)(CCTK_REAL, CCTK_REAL))

   registers a function which returns rest mass density  
   as a function of specific internal energy and pressure

EOS_RegisterDPressByDRho     (int handle, CCTK_REAL (*func)(CCTK_REAL, CCTK_REAL))

   registers a function which returns the derivative of pressure by
   rest mass density
   as a function of rest mass density and specific internal energy

EOS_RegisterDPressByDEps     (int handle, CCTK_REAL (*func)(CCTK_REAL, CCTK_REAL))

   registers a function which returns the derivative of pressure by
   specific internal energy
   as a function of rest mass density and specific internal energy

Usage from Fortran:

The Fortran usage is the same as the C usage.  If you register a
function from Fortran it is assumed to be a Fortran function.


Use of EOS by Hydrodynamics thorns:

There are five corresponding calling functions:

pressure = EOS_Pressure(handle, rho, eps)
eps      = EOS_SpecificIntEnergy(handle, rho, pressure)
rho      = EOS_RestMassDens(handle, eps, pressure)
dpdrho   = EOS_DPressByDRho(handle, rho, eps)
dpdeps   = EOS_DPressByDEps(handle, rho, eps)

each of these takes a handle as first argument, which should be
retrieved with

handle = EOS_Handle(name)

where 'name' is the name of the EOS.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### HydroBase

Cactus Code Thorn HydroBase
Author(s)    : Tanja Bode
               Roland Haas
               Frank Löffler
               Erik Schnetter
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn should be the interface between all hydro codes and the
spacetime codes.

For more information see the documentation in the ThornGuide.

2. Publications

The variables provided by this thorn are those of the Valencia formulation of GRHD:
@ARTICLE{1997ApJ...476..221B,
   author = {{Banyuls}, F. and {Font}, J.~A. and {Ibanez}, J.~M.~A. and {Marti}, J.~M.~A. and 
	{Miralles}, J.~A.},
    title = "{Numerical $\{$3+1$\}$ General Relativistic Hydrodynamics: A Local Characteristic Approach}",
  journal = {\apj},
 keywords = {HYDRODYNAMICS, METHODS: NUMERICAL, RELATIVITY, SHOCK WAVES},
     year = 1997,
    month = feb,
   volume = 476,
    pages = {221-+},
      doi = {10.1086/303604},
   adsurl = {http://adsabs.harvard.edu/abs/1997ApJ...476..221B},
  adsnote = {Provided by the SAO/NASA Astrophysics Data System}
}

@ARTICLE{2006ApJ...637..296A,
   author = {{Ant{\'o}n}, L. and {Zanotti}, O. and {Miralles}, J.~A. and 
	{Mart{\'{\i}}}, J.~M. and {Ib{\'a}{\~n}ez}, J.~M. and {Font}, J.~A. and 
	{Pons}, J.~A.},
    title = "{Numerical 3+1 General Relativistic Magnetohydrodynamics: A Local Characteristic Approach}",
  journal = {\apj},
   eprint = {arXiv:astro-ph/0506063},
 keywords = {Methods: Numerical, Magnetohydrodynamics: MHD, Relativity},
     year = 2006,
    month = jan,
   volume = 637,
    pages = {296-312},
      doi = {10.1086/498238},
   adsurl = {http://adsabs.harvard.edu/abs/2006ApJ...637..296A},
  adsnote = {Provided by the SAO/NASA Astrophysics Data System}
}


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| initial_hydro | "zero" | The hydro initial data |   "zero" :: "hydro variables are set to vacuum (without atmosphere)"*OR*  "read from file" :: "Read the initial data using the IOUtil file reader.  Note that this only allows you to read the data from a file, it does not actually do it.  You still have to programme the IOUtil file reader accordingly."*OR* |
| evolution_method | "none" | The hydro evolution method |   "none" :: "hydro variables are not evolved"*OR* |
| timelevels | 1 | Number of time levels in evolution scheme |   1:3 :: ""*OR* |
| prolongation_type | "ENO" | The prolongation operator used by Carpet for HydroBase variables |   "ENO"  :: "Third order ENO operators; only third order is implemented"*OR*  "WENO" :: "Fifth order WENO operators; only fifth order is implemented"*OR*  ".*"   :: "Anything else"*OR* |
| initial_Y_e | "none" | Initial value for Y_e |   "none" :: "inactive"*OR*  "one"  :: "initially set to one"*OR*  "read from file" :: "Read the initial data using the IOUtil file reader.  Note that this only allows you to read the data from a file, it does not actually do it.  You still have to programme the IOUtil file reader accordingly."*OR* |
| initial_Abar | "none" | Initial value for Abar |   "none" :: "inactive"*OR*  "zero" :: "initially set to zero"*OR*  "read from file" :: "Read the initial data using the IOUtil file reader.  Note that this only allows you to read the data from a file, it does not actually do it.  You still have to programme the IOUtil file reader accordingly."*OR* |
| Y_e_evolution_method | "none" | Evolution method for Y_e |   "none" :: "Evolution for Y_e is disabled"*OR* |
| Abar_evolution_method | "none" | Evolution method for Abar |   "none" :: "Evolution for Abar is disabled"*OR* |
| temperature_evolution_method | "none" | Evolution method for temperature |   "none" :: "Evolution for temperature is disabled"*OR* |
| entropy_evolution_method | "none" | Evolution method for entropy |   "none" :: "Evolution for entropy is disabled"*OR* |
| initial_Bvec | "none" | Initial value for Bvec |   "none" :: "inactive"*OR*  "zero" :: "initially set to zero"*OR*  "read from file" :: "Read the initial data using the IOUtil file reader.  Note that this only allows you to read the data from a file, it does not actually do it.  You still have to programme the IOUtil file reader accordingly."*OR* |
| initial_Avec | "none" | Initial value for Avec |   "none" :: "inactive"*OR*  "zero" :: "initially set to zero"*OR*  "read from file" :: "Read the initial data using the IOUtil file reader.  Note that this only allows you to read the data from a file, it does not actually do it.  You still have to programme the IOUtil file reader accordingly."*OR* |
| initial_Aphi | "none" | Initial value for Aphi |   "none" :: "inactive"*OR*  "zero" :: "initially set to zero"*OR*  "read from file" :: "Read the initial data using the IOUtil file reader.  Note that this only allows you to read the data from a file, it does not actually do it.  You still have to programme the IOUtil file reader accordingly."*OR* |
| Bvec_evolution_method | "none" | Evolution method for Bvec |   "none" :: "Evolution for Bvec is disabled"*OR* |
| hydro_excision | 0 | Turn on of off (default) storage for hydro excision |   0:* :: "Anything else than 0 turns hydro_excision on, added to by other thorns"*OR* |
| initial_temperature | "none" | Initial value for temperature |   "none"  :: "inactive"*OR*  "zero"  :: "initially set to zero"*OR*  "read from file" :: "Read the initial data using the IOUtil file reader.  Note that this only allows you to read the data from a file, it does not actually do it.  You still have to programme the IOUtil file reader accordingly."*OR* |
| initial_entropy | "none" | Initial value for entropy |   "none"  :: "inactive"*OR*  "zero"  :: "initially set to zero"*OR*  "read from file" :: "Read the initial data using the IOUtil file reader.  Note that this only allows you to read the data from a file, it does not actually do it.  You still have to programme the IOUtil file reader accordingly."*OR* |


## WVUThorns

### ID_converter_GiRaFFE

Cactus Code Thorn ID_converter_GiRaFFE
Author(s)    : Zachariah B. Etienne
Note         : Convert HydroBase variables to 
               GiRaFFE-compatible variables. Used 
               for compatibility with HydroBase/ADMBase
               initial data thorns in the Einstein 
               Toolkit.               
Maintainer(s): Zachariah B. Etienne
License      : FreeBSD License, AKA "2-Clause BSD License"
----------------------------------------------------------

1. Purpose

GiRaFFE and HydroBase variables are incompatible;
The former uses 3-velocity defined as v^i = u^i/u^0, and
the latter uses the Valencia formalism definition of v^i.

Define the Valencia formalism's definition of v^i to be 
"W^i", and GiRaFFE's definition "v^i"
Then

W^i = (v^i + \beta^i) / (\alpha). (Eq 11 in 
http://arxiv.org/pdf/1501.07276.pdf).

Similarly,

v_i = (\alpha) W^i  - \beta^i

In addition, GiRaFFE needs the A-fields to be 
defined on *staggered* grids, and HydroBase does not yet
support this option. The staggerings are defined in 
Table 1 of the IllinoisGRMHD code announcement paper:
http://arxiv.org/pdf/1501.07276.pdf (page 15).

The long-term goal should be to deprecate this thorn.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| random_seed | 0 | Random seed for random, generally roundoff-level perturbation on initial data. Seeds srand(), and rand() is used for the RNG. |  0:99999999 :: "Anything unsigned goes."*OR* |
| random_pert | 0 | Random perturbation atop data |  *:* :: "Anything goes."*OR* |
| pure_hydro_run | "no" | Set the vector potential and corresponding EM gauge quantity to zero |  |


### ShiftedKerrSchild

Cactus Code Thorn ShiftedKerrSchild
Author(s)    : Zach Etienne <zachetie@gmail.com>
             : 
Maintainer(s): Zach Etienne <zachetie@gmail.com>
             : 
License      : 2-clause BSD (a.k.a., "FreeBSD license"). See LICENSE file
             : for full license terms and conditions.
--------------------------------------------------------------------------

1. Purpose

To set up Kerr-Schild initial data, with a shifted radial coordinate.
See Appendix of GiRaFFE code announcement paper for full description:
https://arxiv.org/abs/1704.00599


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| KerrSchild_radial_shift | 0.0 | Radial shift for Kerr Schild initial data. Actual shift = KerrSchild_radial_shift*BH_mass |   0.0:* ::  "Positive"*OR* |
| BH_mass | 1.0 | The mass of the black hole. Let's keep this at 1! |   0.0:* ::  "Positive"*OR* |
| BH_spin | 0.0 | The z-axis *dimensionless* spin of the black hole |   -1.0:1.0 ::  "Anything between -1 and +1"*OR* |


### GiRaFFEfood

Cactus Code Thorn GiRaFFEFood
Author(s)    : Zach Etienne <zachetie@gmail.com>
             : Mew-Bing Wan <mbwan78@gmail.com>
             : Maria Babiuc <babiuc@marshall.edu>
Maintainer(s): Zach Etienne <zachetie@gmail.com>
             : Mew-Bing Wan <mbwan78@gmail.com>
             : Maria Babiuc <babiuc@marshall.edu>
License      : GNU General Public License, version 2 or higher. See GPLv2
             : for GPL version 2.0 license.
--------------------------------------------------------------------------

1. Purpose

"GiRaFFEfood provides food for the GiRaFFE." 
I.e., it is an initial data and analysis thorn for all
code validation tests performed in the GiRaFFE code
announcement paper: https://arxiv.org/abs/1704.00599

2. Usage

a) Add this thorn, along with GiRaFFE, ShiftedKerrSchild, etc.
   to  your ThornList
b) Compile the Einstein Toolkit (ET), generating ET executable
c) Run the ET executable, reading in one of the parameter files
   in the par/ directory.

Here are the tests included in this thorn, and the corresponding
   parameter files:

1D tests in flat spacetime:
1) Fast Wave:              GiRaFFEfood_1D_FastWave.par
2) Alfven Wave:            GiRaFFEfood_1D_AlfvenWave.par
3) Degenerate Alfven Wave: GiRaFFEfood_1D_DegenAlfvenWave.par
4) Three Waves:            GiRaFFEfood_1D_ThreeWave.par
5) FFE Breakdown:          GiRaFFEfood_1D_FFEBreakdown.par

3D test in flat spacetime:
1) Aligned Rotator: GiRaFFEfood_AlignedRotator.par

3D tests in curved spacetime (black hole in Shifted Kerr-Schild coords):
1) Split Monopole:       GiRaFFEfood_SplitMonopole.par
2a) Exact Wald: Low-Res: GiRaFFEfood_ExactWald_LowRes.par
2b) Exact Wald: Med-Res: GiRaFFEfood_ExactWald_MedRes.par
2c) Exact Wald: Hig-Res: GiRaFFEfood_ExactWald_HigRes.par
3) Magnetospheric Wald:  GiRaFFEfood_MagnetoWald.par

ShiftedKerrSchild code AHFinderDirect validation thorn:
   (Use this to confirm that BH spin and radius are consistent
    with injected spin & radius.)
1) GiRaFFEfood_ShiftedKerrSchild-AHFinderValidation.par


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| test_case | "FastWave" | Select the test case to be set as initial condition |   "FastWave"        :: "Fast 1D force free wave travelling in Minkowski background"*OR*  "AlfvenWave"      :: "Alfven 2D force free wave" *OR*  "DegenAlfvenWave" :: "Degenerate Alfven 2D force free wave" *OR*  "ThreeAlfvenWave" :: "Three Alfven 2D force free waves"*OR*  "FFEBreakdown"    :: "Force free breakdown 2D test"*OR*  "SplitMonopole"   :: "Bandford-Znajek Split Monopole Test"*OR*  "ExactWald"       :: "Exact Wald Electrovacuum Solution"*OR*  "MagnetoWald"    :: "Magnetic Wald Force Free Solution"*OR*  "AlignedRotator"  :: "Aligned Rotator Solution"*OR* |
| wave_speed | -0.5 | What is the wave speed relative to the grid frame? |  -1.0:1.0 :: "Anything between -1 and +1"*OR* |
| split_C | 1.0 | The constant for the split monopole solution |   *:* ::  "Anything?"*OR* |
| Wald_B0 | 1.0 | The constant for the exact Wald solution |   *:* ::  "Anything?"*OR* |
| drop_fr_SplitM | "yes" | Should we drop f(r) and f'(r) for the Split Monopole solution? |  |
| Compute_Exact_Every | 1 | How often do we compute difference between exact and numerical (delAi quantities)? |  -1:* :: "-1 = disabled"*OR* |
| B_p_aligned_rotator | 1e-5 | The magnitude of the poloidal magnetic field in the aligned rotator test. |   *:* :: "any real"*OR* |
| Omega_aligned_rotator | 1e3 | The angular velocity for the aligned rotator solution test. |   *:0) :: "any negative value"*OR*  (0:* :: "any positive value"*OR* |
| R_NS_aligned_rotator |  | The radius of the so-called neutron star (NS) set by hand for the aligned rotator solution test. |   -1.  :: "disable the conservative-to-primitive solver modification"*OR*  (0:* :: "any positive value"*OR* |


### GiRaFFE

Cactus Code Thorn GiRaFFE
Author(s)    : Maria Babiuc, Zachariah B. Etienne, Mew-Bing Wan
Note         : GiRaFFE is based on IllinoisGRMHD, and uses the algorithm of
               the Illinois Numerical Relativity group for incorporating
               force-free electrodynamics. 
Maintainer(s): Zachariah B. Etienne
License      : GNU General Public License, version 2 or higher.
--------------------------------------------------------------------------

1. Purpose

GiRaFFE solves the equations of General Relativistic 
Force-Free Electrodynamics (GRFFE) using a high-resolution shock capturing scheme.
It is based on IllinoisGRMHD, which is a rewrite of the Illinois Numerical Relativity 
(ILNR) group's GRMHD code.

GiRaFFE is particularly good at modeling black hole magnetospheres. Its 
conservative-to-primitive solver has also been modified to check the 
physicality of conservative variables prior to primitive inversion, and move them 
into the physical range if they become unphysical.

Currently GiRaFFE consists of
1) the Piecewise Parabolic Method (PPM) for reconstruction, 
2) the Harten, Lax, van Leer (HLL/HLLE) approximate Riemann solver, and
3) a modified HARM Conservative-to-Primitive solver. 

GiRaFFE evolves the vector potential A_{\mu} (on staggered grids) 
instead of the magnetic fields (B^i) directly, to guarantee that the 
magnetic fields will remain divergenceless even at AMR boundaries. On 
uniform resolution grids, this vector potential formulation produces 
results equivalent to those generated using the standard, staggered 
flux-CT scheme. This scheme is based on that of Del Zanna (2003, see
below OPTIONAL CITATION #1).

REQUIRED CITATIONS:

1) GiRaFFE code announcement paper: https://arxiv.org/abs/1704.00599

2) IllinoisGRMHD code announcement paper: 
Class. Quantum Grav. 32 (2015) 175009, (https://arxiv.org/abs/1501.07276)

OPTIONAL CITATIONS:
1) Del Zanna, Bucciantini & Londrillo A&A 400, 397 (2003), Eq. (44)


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| current_sheet_null_v | "no" | Shall we null the velocity normal to the current sheet? |  |
| GAMMA_SPEED_LIMIT | 2000.0 | Maximum relativistic gamma factor. Note the default is much higher than IllinoisGRMHD. (GRFFE can handle higher Lorentz factors) |  1:* :: "Positive > 1, though you'll likely have troubles far above 2000."*OR* |
| Velocity_BC | "outflow" | Chosen fluid velocity boundary condition |   "outflow"        :: "Outflow boundary conditions"*OR*  "copy"           :: "Copy data from nearest boundary point"*OR*  "frozen"         :: "Frozen boundaries"*OR* |
| EM_BC | "copy" | EM field boundary condition |   "copy"           :: "Copy data from nearest boundary point"*OR*  "frozen"         :: "Frozen boundaries"*OR* |
| Symmetry | "none" | Currently only no symmetry supported, though work has begun in adding equatorial-symmetry support. FIXME: Extend ET symmetry interface to support symmetries on staggered gridfunctions |   "none"       :: "no symmetry, full 3d domain"*OR* |
| Sym_Bz | 1.0 | In-progress equatorial symmetry support: Symmetry parameter across z axis for magnetic fields = +/- 1 |  -1.0:1.0 :: "Set to +1 or -1."*OR* |
| min_radius_inside_of_which_conserv_to_prims_FFE_and_FFE_evolution_is_DISABLED |  | As parameter suggests, this is the minimum radius inside of which the conservatives-to-primitives solver is disabled. In the Aligned Rotator test, this should be set equal to R_NS_aligned_rotator. |   -1.  :: "disable the conservative-to-primitive solver modification"*OR*  (0:* :: "any positive value"*OR* |
| damp_lorenz | 0.0 | Damping factor for the generalized Lorenz gauge. Has units of 1/length = 1/M. Typically set this parameter to 1.5/(maximum Delta t on AMR grids). |  *:* :: "any real"*OR* |


### Convert_to_HydroBase

Cactus Code Thorn Convert_to_HydroBase
Author(s)    : Zachariah B. Etienne
Note         : Convert IllinoisGRMHD-compatible variables 
               to HydroBase-compatible variables. Used 
               for compatibility with HydroBase/ADMBase
               analysis thorns in the Einstein Toolkit.               
Maintainer(s): Zachariah B. Etienne
License      : FreeBSD License, AKA "2-Clause BSD License"
----------------------------------------------------------

1. Purpose

IllinoisGRMHD and HydroBase variables are incompatible;
The former uses 3-velocity defined as v^i = u^i/u^0, and
the latter uses the Valencia formalism definition of v^i.

Define the Valencia formalism's definition of v^i to be 
"W^i", and IllinoisGRMHD's definition "v^i"
Then

W^i = (v^i + \beta^i) / (\alpha). (Eq 11 in 
http://arxiv.org/pdf/1501.07276.pdf).

Similarly,

v_i = (\alpha) W^i  - \beta^i

In addition, IllinoisGRMHD needs the A-fields to be 
defined on *staggered* grids, and HydroBase does not yet
support this option. The staggerings are defined in 
Table 1 of the IllinoisGRMHD code announcement paper:
http://arxiv.org/pdf/1501.07276.pdf (page 15).

The long-term goal should be to deprecate this thorn.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| Convert_to_HydroBase_every | 0 | How often to convert IllinoisGRMHD primitive variables to HydroBase (Valencia formulation) primitive variables? Needed for some ET-based diagnostics. NOT needed for pure IllinoisGRMHD runs. |  0:* :: "zero (disable) or positive (every N iterations)"*OR* |


### IllinoisGRMHD

Cactus Code Thorn IllinoisGRMHD
Author(s)    : Zachariah B. Etienne
Note         : IllinoisGRMHD is based on the GRMHD code of the
               Illinois Numerical Relativity group, written by Matt Duez, Yuk 
               Tung Liu, and Branson Stephens (original version), and then 
               developed primarily by Zachariah Etienne, Yuk Tung Liu, 
               and Vasileios Paschalidis. 
Maintainer(s): Zachariah B. Etienne
License      : GPLv2 for HARM Conservative-to-Primitive solver (src/harm_*)
               GPLv2+ for all other code
--------------------------------------------------------------------------

==========
1. Purpose
==========

IllinoisGRMHD solves the equations of General Relativistic 
MagnetoHydroDynamics (GRMHD) using a high-resolution shock capturing scheme.
It is a rewrite of the Illinois Numerical Relativity (ILNR) group's GRMHD 
code, and generates results that agree to roundoff error with that original
code. Its feature set coincides with the features of the ILNR group's 
recent code (ca. 2009--2014), which was used in their modeling of the
following systems:
1) Magnetized circumbinary disk accretion onto binary black holes
2) Magnetized black hole--neutron star mergers
3) Magnetized Bondi flow, Bondi-Hoyle-Littleton accretion
4) White dwarf--neutron star mergers

IllinoisGRMHD is particularly good at modeling GRMHD flows into black holes
without the need for excision. Its HARM-based conservative-to-primitive solver 
has also been modified to check the physicality of conservative variables 
prior to primitive inversion, and move them into the physical range if they 
become unphysical.

Currently IllinoisGRMHD consists of
1) the Piecewise Parabolic Method (PPM) for reconstruction, 
2) the Harten, Lax, van Leer (HLL/HLLE) approximate Riemann solver, and
3) a modified HARM Conservative-to-Primitive solver. 

IllinoisGRMHD evolves the vector potential A_{\mu} (on staggered grids) 
instead of the magnetic fields (B^i) directly, to guarantee that the 
magnetic fields will remain divergenceless even at AMR boundaries. On 
uniform resolution grids, this vector potential formulation produces 
results equivalent to those generated using the standard, staggered 
flux-CT scheme. This scheme is based on that of Del Zanna (2003, see
below OPTIONAL CITATION #1).

=======================================================
2. Instructions for committing patches to IllinoisGRMHD
=======================================================

Before committing a patch to IllinoisGRMHD that might *conceivably* 
affect IllinoisGRMHD's roundoff-level agreement with previous versions,
you must first perform a correctness test. Here are the instructions:

Definitions:
a) EXE/ is the folder containing the executable built from your new,
patched version of IllinoisGRMHD
b) TEST/ is arrangements/WVUThorns/IllinoisGRMHD/test/
c) ./ is arrangements/WVUThorns/IllinoisGRMHD/

Instructions:
1) Copy TEST/magnetizedTOV.par and ./code_validation.txt to EXE/.

2) Go to the EXE/ directory

3) Edit magnetizedTOV.par, replacing the line
cactus::cctk_itlast = 2
with
cactus::cctk_itlast = 240

4) Perform the run on at least 8 MPI processes. If your computer has
16 cores, then use the command:
OMP_NUM_THREADS=2 mpirun -n 8 -x OMP_NUM_THREADS ./cactus_[configname] magnetizedTOV.par

5) Make sure the run completes without error. If there are errors, 
you must backtrace what is wrong before committing the patch.

6) The central density is extremely sensitive to beyond-truncation-error
changes, so we'll focus on that quantity exclusively. Parse the output 
file as follows:

awk '{if(NF==3 && NR%3==0) print $0}' magnetizedTOV/rho_b.maximum.asc > myoutput.txt

7) Next compare the results:

paste code_validation.txt myoutput.txt | awk '{printf("%.3f %.3f\t%e\t%e\n",$1,$2,sqrt($4^2),sqrt((($3-$7)/$3)^2));}'

Column definitions:

Column 1: cctk_iteration
Column 2: cctk_time 
Column 3: The relative difference between the trusted version of the 
code and itself, but with a 15th significant digit perturbation at t=0.
Column 4: The relative difference between the trusted version of the 
code and your updated code.

If Columns 3 and 4 are within 1.5 orders of magnitude through iteration 240, 
and the relative difference at iteration 240 is 3e-7 or less, then your change 
is accepted. Please push to the git repository.

===================
REQUIRED CITATIONS:
===================

1) IllinoisGRMHD code announcement paper: 
Class. Quantum Grav. 32 (2015) 175009, (http://arxiv.org/abs/1501.07276)

2) Noble, S. C., Gammie, C. F., McKinney, J. C., \& Del Zanna, L. \ 2006, 
        Astrophysical Journal, 641, 626.

OPTIONAL CITATIONS:
1) Del Zanna, Bucciantini & Londrillo A&A 400, 397 (2003), Eq. (44)


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "essential+iteration | Determines how much evolution information is output |   "no"        :: "Complete silence"*OR*  "essential" :: "Essential health monitoring of the GRMHD evolution: Information about conservative-to-primitive fixes, etc."*OR*  "essential+iteration output" :: "Outputs health monitoring information, plus a record of which RK iteration. Very useful for backtracing a crash."*OR* |
| GAMMA_SPEED_LIMIT | 10.0 | Maximum relativistic gamma factor. |  1:* :: "Positive > 1, though you'll likely have troubles far above 10."*OR* |
| tau_stildefix_enable | 0 | tau<0 fix in primitive_vars_hybrid2 to reduce number of Font fixes, especially in puncture+matter evolutions | # 0:3 :: "zero (disable), one (enable everywhere), or two (enable only where Psi6 > Psi6threshold [i.e., inside the horizon, where B's are set to zero], or three (kludge: set B=0 if tau<0 inside horizon))"*OR*# |
| update_Tmunu | "yes" | Update Tmunu, for RHS of Einstein's equations? |  |
| tau_atm | 1e100 | Floor value on the energy variable tau (cf. tau_stildefix_enable). Given the variety of systems this code may encounter, there *is no reasonable default*. Effectively the current (enormous) value should disable the tau_atm floor. Please set this in your initial data thorn, and reset at will during evolutions. |  0:* :: "Positive"*OR* |
| rho_b_atm | 1e200 | Floor value on the baryonic rest mass density rho_b (atmosphere). Given the variety of systems this code may encounter, there *is no reasonable default*. Your run will die unless you override this default value in your initial data thorn. |  *:* :: "Allow for negative values.  This enables us to debug the code and verify if rho_b_atm is properly set."*OR* |
| rho_b_max | 1e300 | Ceiling value on the baryonic rest mass density rho_b. The enormous value effectively disables this ceiling by default. It can be quite useful after a black hole has accreted a lot of mass, leading to enormous densities inside the BH. To enable this trick, set rho_b_max in your initial data thorn! You are welcome to change this parameter mid-run (after restarting from a checkpoint). |  0:* :: "Note that you will have problems unless rho_b_atm<rho_b_max"*OR* |
| conserv_to_prims_debug | 0 | 0: no, 1: yes |  0:1 :: "zero (no) or one (yes)"*OR* |
| Psi6threshold | 1e100 | Where Psi^6 > Psi6threshold, we assume we're inside the horizon in the primitives solver, and certain limits are relaxed or imposed |  *:* :: "Can set to anything"*OR* |
| neos | 1 | number of parameters in EOS table. If you want to increase from the default max value, you MUST also set eos_params_arrays1 and eos_params_arrays2 in interface.ccl to be consistent! |  1:10 :: "Any integer between 1 and 10"*OR* |
| gamma_th | -1 | thermal gamma parameter |  0:* :: "Physical values"*OR*-1   :: "forbidden value to make sure it is explicitly set in the parfile"*OR* |
| K_poly | 1.0 | initial polytropic constant |  0:* :: "Positive"*OR* |
| Matter_BC | "outflow" | Chosen Matter boundary condition |   "outflow" :: "Outflow boundary conditions"*OR*  "frozen"  :: "Frozen boundaries"*OR* |
| EM_BC | "copy" | EM field boundary condition |   "copy"    :: "Copy data from nearest boundary point"*OR*  "frozen"  :: "Frozen boundaries"*OR* |
| Symmetry | "none" | Currently only no symmetry supported, though work has begun in adding equatorial-symmetry support. FIXME: Extend ET symmetry interface to support symmetries on staggered gridfunctions |   "none"       :: "no symmetry, full 3d domain"*OR* |
| Sym_Bz | 1.0 | In-progress equatorial symmetry support: Symmetry parameter across z axis for magnetic fields = +/- 1 |  -1.0:1.0 :: "Set to +1 or -1."*OR* |
| damp_lorenz | 0.0 | Damping factor for the generalized Lorenz gauge. Has units of 1/length = 1/M. Typically set this parameter to 1.5/(maximum Delta t on AMR grids). |  *:* :: "any real"*OR* |


### GiRaFFE_to_HydroBase

Cactus Code Thorn Convert_GiRaFFE_to_HydroBase
Author(s)    : Zachariah B. Etienne
Note         : Convert GiRaFFE-compatible variables 
               to HydroBase-compatible variables. Used 
               for compatibility with HydroBase/ADMBase
               analysis thorns in the Einstein Toolkit.               
Maintainer(s): Zachariah B. Etienne
License      : FreeBSD License, AKA "2-Clause BSD License"
----------------------------------------------------------

1. Purpose

GiRaFFE and HydroBase variables are incompatible;
The former uses 3-velocity defined as v^i = u^i/u^0, and
the latter uses the Valencia formalism definition of v^i.

Define the Valencia formalism's definition of v^i to be 
"W^i", and GiRaFFE's definition "v^i"
Then

W^i = (v^i + \beta^i) / (\alpha). (Eq 11 in 
http://arxiv.org/pdf/1501.07276.pdf).

Similarly,

v_i = (\alpha) W^i  - \beta^i

In addition, GiRaFFE needs the A-fields to be 
defined on *staggered* grids, and HydroBase does not yet
support this option. The staggerings are defined in 
Table 1 of the IllinoisGRMHD code announcement paper:
http://arxiv.org/pdf/1501.07276.pdf (page 15).

The long-term goal should be to deprecate this thorn.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| Convert_GiRaFFE_to_HydroBase_every | 0 | How often to convert GiRaFFE primitive variables to HydroBase (Valencia formulation) primitive variables? Needed for some ET-based diagnostics. NOT needed for pure GiRaFFE runs. |  0:* :: "zero (disable) or positive (every N iterations)"*OR* |


### ID_converter_ILGRMHD

Cactus Code Thorn ID_conerter_ILGRMHD
Author(s)    : Zachariah B. Etienne
Note         : Convert HydroBase variables to 
               IllinoisGRMHD-compatible variables. Used 
               for compatibility with HydroBase/ADMBase
               initial data thorns in the Einstein 
               Toolkit.               
Maintainer(s): Zachariah B. Etienne
License      : FreeBSD License, AKA "2-Clause BSD License"
----------------------------------------------------------

1. Purpose

IllinoisGRMHD and HydroBase variables are incompatible;
The former uses 3-velocity defined as v^i = u^i/u^0, and
the latter uses the Valencia formalism definition of v^i.

Define the Valencia formalism's definition of v^i to be 
"W^i", and IllinoisGRMHD's definition "v^i"
Then

W^i = (v^i + \beta^i) / (\alpha). (Eq 11 in 
http://arxiv.org/pdf/1501.07276.pdf).

Similarly,

v_i = (\alpha) W^i  - \beta^i

In addition, IllinoisGRMHD needs the A-fields to be 
defined on *staggered* grids, and HydroBase does not yet
support this option. The staggerings are defined in 
Table 1 of the IllinoisGRMHD code announcement paper:
http://arxiv.org/pdf/1501.07276.pdf (page 15).

The long-term goal should be to deprecate this thorn.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| Gamma_Initial | 1.3 | Single Gamma-law EOS: Gamma |  0:* :: "Positive or zero"*OR* |
| K_Initial | 1.0 | Single Gamma-law EOS: K |  0:* :: "Positive or zero"*OR* |
| random_seed | 0 | Random seed for random, generally roundoff-level perturbation on initial data. Seeds srand(), and rand() is used for the RNG. |  0:99999999 :: "Anything unsigned goes."*OR* |
| random_pert | 0 | Random perturbation atop data |  *:* :: "Anything goes."*OR* |
| pure_hydro_run | "no" | Set the vector potential and corresponding EM gauge quantity to zero |  |


### Seed_Magnetic_Fields

Cactus Code Thorn Seed_Magnetic_Fields
Author(s)    : Zachariah B. Etienne
Note         : This thorn is designed to seed magnetic 
               fields into initial data. Generally, these
               magnetic fields are sufficiently weak that
               there is no significant dynamical impact
               initially.
Maintainer(s): Zachariah B. Etienne
License      : FreeBSD License, AKA "2-Clause BSD License"
----------------------------------------------------------

1. Purpose

Seed magnetic fields into an initial matter configuration.
Currently seeding into TOV stars is supported, according
to the poloidal magnetic field prescription:
A_x = -y*A_b*pow(MAX(P-P_cut,0.0),n_s)
A_y =  x*A_b*pow(MAX(P-P_cut,0.0),n_s)
A_z = 0
Phi = 0

as specified in Appendix B of the IllinoisGRMHD code
announcement paper:
http://arxiv.org/pdf/1501.07276.pdf

Note that we must be careful if A_i's are staggered. In this
case, the pressure must be interpolated to the staggered
point, and the values of x and y must also be shifted.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| enable_IllinoisGRMHD_staggered_A_fields | "yes" | Define A fields on an IllinoisGRMHD staggered grid |  |
| Afield_type | "Pressure_prescription" | A-field prescription | "Pressure_prescription"     :: "A_phi propto (P - P_cut)^n_s"*OR*"Density_prescription"      :: "A_phi propto rho"*OR* |
| enable_varpi_squared_multiplication | "yes" | Multiply A_phi by varpi^2? |  |
| A_b | 1e-3 | Magnetic field strength parameter. |  *:* :: "Any real"*OR* |
| P_cut | 1e-5 | Cutoff pressure, below which vector potential is set to zero. Typically set to 4% of the maximum initial pressure. |  0:* :: "Positive"*OR* |
| n_s | 1.0 | Magnetic field strength pressure exponent. |  *:* :: "Any real"*OR* |
| rho_cut | 0.2 | Cutoff density, below which vector potential is set to zero. Typically set to 20% of the maximum initial density. |  0:* :: "Positive"*OR* |


## Llama

### Coordinates

Cactus Code Thorn Coordinates
Author(s)    : Denis Pollney
               Christian Reisswig
               Erik Schnetter
               Peter Diener
               Nils Dorband
               many others
Maintainer(s): Denis Pollney, Christian Reisswig
Licence      : GPLv2+
--------------------------------------------------------------------------

1. Purpose

Llama is a multipatch infrastructure for Cactus.

This thorn provides definition of patch systems and coordinates.

For more information see the documentation in the ThornGuide.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |


### WaveExtractL

Cactus Code Thorn WaveExtract
Thorn Author(s)     : Frank Herrmann <herrmann@aei.mpg.de>
Thorn Maintainer(s) : Christian Reisswig <reisswig@aei.mpg.de>
--------------------------------------------------------------------------

Purpose of the thorn:

Does Wave extraction and only wave extraction using the Zerilli formalism.
This thorn was formerly Extract, but that got overloaded with lots of stuff.



NOTE:

This thorn is a modification to the original WaveExtract as found in
AEIDevelopment and has been extended to be OMP parallel and to use
CarpetInterp2.  Some features as working with corotating coordinates
have been removed.

IMPORTANT: The old version in AEIDevelopment/WaveExtract is NOT
           working with general coordinates (especially radial
           stretching)!!  Use this thorn instead!

| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| active | "yes" | Should we run at all? |  |
| start_iteration | 0 | First iteration when we should be called |   *:* :: ""*OR* |
| start_time | -1 | First time when we should be called |   *:* :: ""*OR* |
| calc_when_necessary | no | Start calculation at T = R_detector - 50 and stop after T_merger+Ringdown_margin+R_detector |  |
| ringdown_margin | 150.0 | The expected length of ringdown. If calc_when_necessary=yes, then T_merger+ringdown_margin+R_detector is the time when calculations are switched off |   0:* :: "Any positive number"*OR* |
| out_every | 1 | At which iterations should we be called |   *:* :: "negative means set via out_every_det"*OR* |
| verbose | 1 | how verbose should the output be? |   0:10 :: "higher number means more output"*OR* |
| origin_x | 0 | origin in x direction |   *:* :: ""*OR* |
| origin_y | 0 | origin in y direction |   *:* :: ""*OR* |
| origin_z | 0 | origin in z direction |   *:* :: ""*OR* |
| mode_type | "all | Which type of mode extraction do we have |   "all modes"     :: "Extract all modes up to (l_mode, m_mode)."*OR*  "specific mode" :: "Select one specific (l_mode, m_mode) mode"*OR* |
| l_mode | 2 | all modes: Up to which l mode to calculate/ spefic mode: which l mode to extract |   2:* :: "Positive Please, note that l=0,1 are gauge dependent and not implemented"*OR* |
| m_mode | 0 | all modes: Up to which m mode to calculate/ specific mode: which m mode to extract  |   0:* :: "Positive Please"*OR* |
| maxntheta | 100 | how many points in theta direction at most |   0:* :: "Positive Please - even or odd depends on integration scheme used"*OR* |
| maxnphi | 100 | how many points in phi direction at most |   0:* :: "Positive Please - even or odd depends on integration scheme used"*OR* |
| subtract_spherical_background | "yes" | Subtract spherical background before calculation of Extraction quantities |  |
| interpolation_operator | "Hermite | What interpolator should be used to interpolate onto the surface |   ".+" :: "A valid interpolator name, see Thorn AEILocalInterp"*OR* |
| interpolation_order | 2 | interpolation order for projection onto the sphere |   1:* :: "Positive Please"*OR* |
| interpolation_stencil | 4 | interpolation stencil, this is needed to work out how far out you can place the detectors. It depends on the interpolator and the interpolation order used. |   1:* :: "Positive Please"*OR* |
| integration_method | "Gauss" | which method to use for the integration |   "Gauss" :: "error is O(1/e^N)"*OR*  "extended midpoint rule" :: "weight is one for all points, error is O(1/N^2)"*OR*  "open extended" :: "weight is 23/12,13/12,4/3,2/4,...0,27/12, error O(1/N^4)"*OR* |
| maximum_detector_number | 9 | How many detectors do you want. NOTE: This also fixes the number of detectors in Cauchy mode! |   0:100 :: "Positive Please, less than hard limit FIXME"*OR* |
| check_rmax | no | Whether to check for rmax or not (must be set to no for Llama |  |
| rsch2_computation | "aerial | How to calculate (Schwarzschild_Radius)^2 |   "aerial radius" :: "calculate invariant aerial radius: r^2=int(sqrt(gtt*gpp-gtp^2))"*OR*  "average Schwarzschild metric" :: "assume Schwarzschild coordinates: r^2=int(1/2 (gtt+gpp/sin^2(theta))"*OR*  "Schwarzschild gtt" :: "assume Schwarzschild coordinates: r^2=int(gtt)"*OR*  "Schwarzschild gpp" :: "assume Schwarzschild coordinates: r^2=int(gpp/sin^2(theta))"*OR* |
| drsch_dri_computation | "average | How to calculate dr_Schwarzschild/dr_isotropic |   "average dr_gtt dr_gpp" :: "average using drsch_dri=int(1/2(dr_gtt+dr_gpp/sin^2(theta)))"*OR*  "dr_gtt" :: "drsch_dri=int(dr_gtt)"*OR*  "dr_gpp" :: "drsch_dri=int(dr_gpp/sin^2(theta))"*OR* |
| detection_mode | "specific | Give Specific locations or let the code place the detectors |   "Cauchy extraction"  :: "a lot of detectors in some specified range"*OR*  "specific detectors" :: "give some values"*OR* |
| Cauchy_radius_start_coord | -2 | where to start Cauchy line in coordinates |   -2:* :: "-2 means deactive (ie use percentage), -1 means start from first grid point"*OR* |
| Cauchy_radius_end_coord | -2 | where to end Cauchy line in coordinates |   -2:* :: "-2 means deactive (ie use percentage), -1 means go up to maximum"*OR* |
| Cauchy_radius_start_factor | 0 | where to start Cauchy line as factor of grid size |   -1:1 :: "-1 means deactive (ie use coordinate), 0 means first grid point"*OR* |
| Cauchy_radius_end_factor | 1 | where to end Cauchy line as factor of grid size |   -1:1 :: "-1 means deactive (ie use coordinate), 1 means go up to maximum"*OR* |
| Cauchy_radius_dr | -1 | seperation of 2 detectors for the extraction |   -1:* :: "-1 means: let the code figure it out"*OR* |
| cauchy_radius_extreme | 0 | use min. radius along axis, dr=dx |  |
| Cauchy_time_ID | 0 | compute initial data (ID) for dt_Zerilli? |  |
| cartoon | "no" | use cartoon |  |
| cartoon_grid_acc | 1e-2 | accuracy to use for dx=dy=dz check |   0:: ""*OR* |
| observers | "no" | do we use the observer thorn |  |
| rotation_omega | 0.0 | omega from driftcorrect |   : :: ""*OR* |
| corotate | "no" | do we corotate? give omega if so |  |
| phicorotate | "no" | undo corotation on phi itself |  |
| corotate3d | "no" | do we corotate in 3D (easy slow way)? give omega if so |  |
| switch_output_format | 10 | How many output files do you suffer in your directory, when you have more detectors than this number, the file format is switched |  1:* :: "positive"*OR* |
| out_dir | "" | Output directory for Extract's files, overrides IO::out_dir |   ".+" :: "A valid directory name"*OR*  "^$" :: "An empty string to choose the default from IO::out_dir"*OR* |
| out_style | "gnuplot" | Which style for Scalar output |   "gnuplot" :: "1D output readable by gnuplot"*OR*  "xgraph"  :: "1D output readable by xgraph"*OR* |
| out_format | ".13f" | Which format for Scalar floating-point number output |   ^(\.[1]?[0-9])?[EGefg]$ :: "output with given precision in exponential / floating point notation"*OR* |
| write_timer_info | "no" | write timing information to stdout |  |
| use_spherical_surface | "no" | use spherical surface thorn provided spheres |  |
| make_interpolator_warnings_fatal | "no" | Report interpolator warnings as level-0 error messages (and abort the run) ? |  |


### ADMDerivatives

Cactus Code Thorn ADMDerivatives
Author(s)    : Christian Reisswig <reisswig@aei.mpg.de>
Maintainer(s): Christian Reisswig <reisswig@aei.mpg.de>
Licence      : GNU
--------------------------------------------------------------------------

1. Purpose

This thorn calculates and stores derivatives of the ADMBase variables.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| store_radial_derivatives | yes | Switch calculation of radial derivatives of ADM lapse, shift, 3-metric on/off |  |
| store_cartesian_derivatives | no | Switch calculation of cartesian derivatives of ADM lapse, shift, 3-metric on/off |  |
| store_time_derivatives | yes | Switch calculation of time derivatives of ADM lapse, shift, 3-metric on/off |  |
| spatial_deriv_order | -1 | Order of spatial derivatives |   -1   :: "Use whatever is specified by SummationByParts"*OR*  2:8  :: "Use this order"*OR* |
| timelevels | 1 | Number of timelevels for all variables |   1 :: "Only one active level"*OR*  3 :: "Useful for time interpolation"*OR* |


### GlobalDerivative

CVS info   : $Header$

Cactus Code Thorn GlobalDerivative
Thorn Author(s)     : Christian Reisswig <reisswig@aei.mpg.de>
Thorn Maintainer(s) : Christian Reisswig <reisswig@aei.mpg.de>
--------------------------------------------------------------------------

Purpose of the thorn:

This thorn is ment to provide derivative operators in a global coordinate frame
for variables that are defined on local grid coordinates.

\hat{partial_i} = J^j_i \partial_j


It can also apply dissipation in the same way.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| fd_order_on_non_Cart_maps | -1 | Order of accuracy of spatial derivatives on non-Cartesian patches. |   -1   :: "use same FD order everywhere"*OR*   2:* :: "use different FD order on non-Cartesian patches"*OR* |
| use_dissipation | no | Use global dissipation |  |
| force_diss_order | -1 | Force this order of accuracy for dissipation operator |   -1  :: "Use default as specified in SBP::order"*OR*  2:8 :: "2nd, 4th, 6th and 8th order"*OR* |


### LlamaWaveToy

Cactus Code Thorn LlamaWaveToy
Thorn Author(s)     : Erik Schnetter <schnetter@cct.lsu.edu>
Thorn Maintainer(s) : Erik Schnetter <schnetter@cct.lsu.edu>
--------------------------------------------------------------------------

Purpose of the thorn:

A solver for the scalar wave equation on a non-trivial, static
background.



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| rhsbound | "none" | Boundary condition during RHS evaluation |   ".*" :: "any registered boundary condition"*OR* |
| bound | "static" | Boundary condition |   ".*" :: "any registered boundary condition"*OR* |
| outer_penalty_bound | "zero" | outer penalty boundary |   "zero"     :: "set all characteristics to zero"*OR*  "solution" :: "use the same analytic solution as for the initial data"*OR* |
| outer_bound | "solution" | outer boundary |   "zero"      :: "set all characteristics to zero"*OR*  "solution"  :: "use the same analytic solution as for the initial data"*OR*  "dirichlet" :: "set all fields to zero"*OR*  "radiative" :: "radiative boundary"*OR*  "none"      :: "no boundary condition"*OR* |
| metric | "Minkowski" | Global metric |   "Minkowski"   :: "Minkowski"*OR*  "Kerr-Schild" :: "Kerr-Schild"*OR*  "Kerr" :: "Kerr Black Hole"*OR* |
| mass | 1.0 | Mass M |   *:* :: ""*OR* |
| spin | 0.0 | Spin a=J/M^2 |   -1:+1 :: ""*OR* |
| lapse | 1.0 | Lapse function multiplier |   *:* :: "must not be zero"*OR* |
| shift_omega | 0.0 | Rotational shift vector addition about z axis |   *:* :: ""*OR* |
| initial_data | "plane" | Type of initial data |   "linear"             :: "x and y coordinates"*OR*  "plane"              :: "Plane wave"*OR*  "Gaussian"           :: "Gaussian wave packet"*OR*  "GaussianNonLinear"  :: "Gaussian wave packet for the non-linear RHS"*OR*  "GeneralMultipole"   :: "Multipole with arbitrary l and m"*OR*  "multipole"          :: "L=1 initial data, Gaussian in r"*OR*  "multipole l=1, m=0" :: "L=1 m=0 initial data, Gaussian in r, u=0"*OR*  "multipole l=1, m=1" :: "L=1 m=1 initial data, Gaussian in r, u=0"*OR*  "multipole l=1, m=-1" :: "L=1 m=-1 initial data, Gaussian in r, u=0"*OR*  "multipole l=2"      :: "L=2 initial data, Gaussian in r"*OR*  "multipole l=2, u=0" :: "L=2 initial data, Gaussian in r, u=0"*OR*  "multipole l=2, m=1" :: "L=2 m=1 initial data, Gaussian in r, u=0"*OR*  "multipole l=2, m=-1" :: "L=2 m=-1 initial data, Gaussian in r, u=0"*OR*  "multipole l=2, m=2" :: "L=2 m=2 initial data, Gaussian in r, u=0"*OR*  "multipole l=2, m=-2" :: "L=2 m=-2 initial data, Gaussian in r, u=0"*OR*  "multipole l=2, m=-2" :: "L=2 m=-2 initial data, Gaussian in r, u=0"*OR*  "multipole l=4, m=0" :: "L=4 m=0 initial data, Gaussian in r, u=0"*OR*  "multipole l=4, m=1" :: "L=4 m=1 initial data, Gaussian in r, u=0"*OR*  "multipole l=4, m=-1" :: "L=4 m=-1 initial data, Gaussian in r, u=0"*OR*  "multipole l=4, m=2" :: "L=4 m=-2 initial data, Gaussian in r, u=0"*OR*  "multipole l=4, m=-2" :: "L=4 m=-2 initial data, Gaussian in r, u=0"*OR*  "multipole l=4, m=3" :: "L=4 m=3 initial data, Gaussian in r, u=0"*OR*  "multipole l=4, m=-3" :: "L=4 m=-3 initial data, Gaussian in r, u=0"*OR*  "multipole l=4, m=4" :: "L=4 m=4 initial data, Gaussian in r, u=0"*OR*  "multipole l=4, m=-4" :: "L=4 m=-4 initial data, Gaussian in r, u=0"*OR*  "noise"              :: "Random noise"*OR*  "debug"              :: "number of current patch and grid point index"*OR* |
| initial_data_analytic_derivatives | "no" | Calculate spatial derivatives of the initial data analytically? |  |
| eps | 1.0e-10 | A small number |   0:* :: ""*OR* |
| amplitude | 1.0 | Amplitude |   *:* :: ""*OR* |
| width | 1.0 | Width of the Gaussian |   (0:* :: ""*OR* |
| radius | 0.0 | Radius of the Gaussian |   0:* :: ""*OR* |
| multipole_l | 2 | For GeneralMultipole initial data: degree of spherical harmonic function |   0:* :: "A positive integer"*OR* |
| multipole_m | 2 | For GeneralMultipole initial data: order of spherical harmonic function |   *:* :: "An integer -l<=m<=l"*OR* |
| multipole_s | 0 | For GeneralMultipole initial data: spin weight spherical harmonic function |   *:* :: "A positive integer"*OR* |
| time_offset | 0.0 | Time offset |   *:* :: ""*OR* |
| epsx | 0.0 | eps in x-direction of the non-linear Gaussian |   *:* :: ""*OR* |
| epsy | 0.0 | eps in y-direction of the non-linear Gaussian |   *:* :: ""*OR* |
| RNL | 0.0 | How fat the non-linear Gaussian is (r-R) |   *:* :: ""*OR* |
| ANL | 0.0 | Amplitude of the non-linear Gaussian |   *:* :: ""*OR* |
| deltaNL | 0.0 | sigma of the non-linear Gaussian |   *:* :: ""*OR* |
| muNL | 0.0 | Speed of the non-linear Gaussian |   *:* :: ""*OR* |
| omeNL | 0.0 | Omega of the non-linear Gaussian |   *:* :: ""*OR* |
| formulation | "dt" | Formulation of the wave equation | #   "dt" :: "rho = d_t u"*OR*#  |
| shift_interpolation_type | "shift" | Setting for the interpolating vector field b^i (only used for the db formulation) |   "shift" :: "Set b^i = beta^i (corresponds to d0 formulation)"*OR*  "zero"  :: "Set b^i = 0 (corresponds to dk formulation)"*OR* |
| nonlinearrhs | "no" | Add a non-linear term to the RHS? |  |
| powerrhs | 5.0 | Exponent in the non-linear RHS term |   3.0:13.0 :: ""*OR* |
| compute_second_derivative_from_first_derivative | "no" | Take first derivative twice to compute second derivate |  |
| NumEvolvedVars | 5 | The number of evolved variables in this thorn |   5:5 :: "five"*OR* |
| recalculate_rhs | "yes" | Recalculate the RHSs in the ANALYSIS timebin |  |


### CoordinatesSymmetry

Cactus Code Thorn CoordinatesSymmetry
Author(s)    : Christian Reisswig <reisswig@tapir.caltech.edu>, Erik Schnetter <schnetter@cct.lsu.edu>
Maintainer(s): Christian Reisswig <reisswig@tapir.caltech.edu>, Erik Schnetter <schnetter@cct.lsu.edu>
Licence      : GPL
--------------------------------------------------------------------------

1. Purpose

Provide various symmetries, i.e., bitant, quadrant, and octant mode.

This thorn is adapted from Cactus to work with Llama symmetries.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "no" | Produce screen output while applying boundary conditions |  |
| reflection_x | "no" | Reflection symmetry at the lower x boundary |  |
| reflection_y | "no" | Reflection symmetry at the lower y boundary |  |
| reflection_z | "no" | Reflection symmetry at the lower z boundary |  |
| rotating_90 | "no" | Apply an octant 90 degree rotating symmetry condition |  |
| stagger | "yes" | Stagger symmetry boundaries? |  |


### Interpolate2

Cactus Code Thorn Interpolate2
Author(s)    : Denis Pollney
               Christian Reisswig
               Erik Schnetter
               Peter Diener
               Nils Dorband
               many others
Maintainer(s): Denis Pollney, Christian Reisswig
Licence      : GPLv2+
--------------------------------------------------------------------------

1. Purpose

Llama is a multipatch infrastructure for Cactus.

This thorn provides the interpatch interpolation setup.

For more information see the documentation in the ThornGuide.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| coordsystem | "cart3d" | The coordinate system to use | #  "" :: "must be a registered coordinate system"*OR*# |
| interpolator | "Lagrange | The interpolator to use | #  "" :: "must be a registered interpolator"*OR*# |
| interpolator_options | "order=4" | Options for the interpolator | #  "" :: "must be a valid options specification"*OR*# |
| interpolator_order | 4 | Interpolation order |   0:* :: ""*OR* |
| interpolator_order_matter | -1 | Interpolation order for those variables having an 'interpolator=matter' tag |   -1  :: "Don't use special matter interpolator"*OR*   1  :: "1st-order Lagrange"*OR*   2  :: "2nd order ENO"*OR* |
| fill_patch0_radius_min | 1e+30 | Interpolate to patch 0 from this radius on |   0.0:* :: ""*OR* |
| fill_patch0_radius_max | 1e+30 | Interpolate to patch 0 up to this radius |   0.0:* :: ""*OR* |
| fill_patch0_xyradius_min | 1e+30 | Interpolate to patch 0 from this cylindrical radius in the x-y plane on |   0.0:* :: ""*OR* |
| fill_patch0_xyradius_max | 1e+30 | Interpolate to patch 0 up to this cylindrical radius in the x-y plane |   0.0:* :: ""*OR* |
| poison | -4.2e+20 | Poison value to detect uninitialised variables |   *:* :: ""*OR* |
| verbose | "no" | Produce debug output |  |
| continue_if_selftest_fails | "no" | Continue if the self test fails -- this is only for debugging |  |
| interpolate_zero | "no" | Set interpolation result to zero -- this is only for debugging |  |
| shift_edges | "no" | Artificially shift coordinates of grid function edges (use this for DGFE) |  |


## CactusBase

### IOUtil

Cactus Code Thorn IOUtil
Author(s)    : Gabrielle Allen
               Tom Goodale
               Thomas Radke
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn is the flesh for IO - it always needs to be activated
if you want to do some IO in Cactus.

The thorn just provides basic IO parameters.
These are used in other IO thorns which do the real work.

It also provides a method for registration of recovery routines.


2. Additional information


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| out_dir | "." | Default output directory |   ".+" :: "A valid directory name"*OR* |
| max_entries_per_subdir | 0 | Number of processes that can access the same directory |   0 :: "unlimited"*OR*  2:* :: "at most that many processes"*OR* |
| out_criterion | "iteration" | Criterion to select output intervals |   "never"     :: "Never output"*OR*  "iteration" :: "Output every so many iterations"*OR*  "time"      :: "Output every that much coordinate time"*OR* |
| out_every | -1 | How often to do output by default |    1:* :: "Every so many iterations"*OR*  -1:0 :: "Disable output"*OR* |
| out_dt | -2 | How often to do output by default |   (0:* :: "In intervals of that much coordinate time"*OR*   0   :: "As often as possible"*OR*  -1   :: "Disable output"*OR*  -2   :: "Disable output"*OR* |
| verbose | "standard" | Level of screen output for I/O |   "none"     :: "No output"*OR*  "standard" :: "Initial description for each I/O method"*OR*  "full"     :: "Maximal output"*OR* |
| print_timing_info | "no" | Print timing information on I/O operations. |  |
| new_filename_scheme | "yes" | Use the new filename scheme for output files ? |  |
| require_empty_output_directory | "no" | Require that IO::out_dir is empty at startup ? |  |
| strict_io_parameter_check | "yes" | Stop on errors while parsing I/O parameters from parameter file ? |  |
| abort_on_io_errors | "no" | Abort on I/O errors (rather than just print a warning) ? |  |
| out_fileinfo | "all" | Add some useful file information to output files ? |   "none"               :: "no file information"*OR*  "creation date"      :: "add creation date"*OR*  "parameter filename" :: "add parameter filename"*OR*  "axis labels"        :: "add axis labels information to output files"*OR*  "all"                :: "add all available file information"*OR* |
| out_group_separator | "-" | String to separate group name from variable name in file name |   "" :: "Note: The old default was'::'"*OR* |
| out_mode | "proc" | Which mode to use for output |   "proc"    :: "Every processor writes its share of data into a separate file"*OR*  "np"      :: "Data is collected and written by every N'th processor into a separate file, where N is specified by the parameter IO::out_proc_every"*OR*  "onefile" :: "All output is written into a single file by processor 0"*OR* |
| out_proc_every | 8 | Do output on every N processors |   1:* :: "A number between [1, nprocs)"*OR* |
| out_timesteps_per_file | -1 | How many timesteps to write to a single file | #   1:* :: "Number of timesteps per file"*OR*   1:1 :: "Number of timesteps per file (can only be 1 so far)"*OR*  -1:  :: "All timesteps in a single file"*OR* |
| out3D_septimefiles | "no" | Write one file per time slice, as opposed to all data in one file |  |
| out_unchunked | "no" | Don't write data in chunks. This parameter is ignored for single-processor runs where output is always done in unchunked mode. |  |
| out_save_parameters | "only | Save current parameter settings in output files ? |   "all"      :: "Save all parameter settings"*OR*  "only set" :: "Only save parameters which have been set before"*OR*  "no"       :: "Don't save parameter settings"*OR* |
| out_downsample_x | 1 | Factor by which to downsample output in x direction. Point (0,0,0) is always included. |   1:* :: "A positive integer"*OR* |
| out_downsample_y | 1 | Factor by which to downsample output in y direction. Point (0,0,0) is always included. |   1:* :: "A positive integer"*OR* |
| out_downsample_z | 1 | Factor by which to downsample output in z direction. Point (0,0,0) is always included. |   1:* :: "A positive integer"*OR* |
| out_single_precision | "no" | Output data in single precision ? |  |
| checkpoint_ID | "no" | Checkpoint initial data ? |  |
| recover | "no" | Recover from a checkpoint file ? |   "no"        :: "Don't recover"*OR*  "manual"    :: "Recover from the checkpoint file as given in IO::recover_dir and IO::recover_file"*OR*  "auto"      :: "Automatically recover from the latest checkpoint file found in <recover_dir>"*OR*  "autoprobe" :: "Probe for checkpoint files and automatically recover, continue as usual if nothing was found"*OR* |
| checkpoint_every | -1 | How often to checkpoint |    1:* :: "Every so many iterations"*OR*  -1:0 :: "Disable periodic checkpointing"*OR* |
| checkpoint_every_walltime_hours | -1 | How often to checkpoint |   (0:* :: "After so much walltime has passed"*OR*  -1   :: "Disable periodic walltime checkpointing"*OR* |
| checkpoint_on_terminate | "no" | Checkpoint after last iteration |  |
| checkpoint_keep | 1 | How many checkpoint files to keep |    1:* :: "1 overwrites the latest checkpoint file"*OR*  -1:  :: "Keep all checkpoint files"*OR* |
| checkpoint_file | "checkpoint.chkpt" | File name for regular checkpoint |   ".+" :: "A valid filename"*OR* |
| checkpoint_ID_file | "checkpoint.chkpt" | File name for initial data checkpoint |   ".+" :: "A valid filename"*OR* |
| recover_file | "checkpoint.chkpt" | Basename of recovery file |   ".+" :: "A valid filename"*OR* |
| checkpoint_dir | "." | Output directory for checkpoint files |   ".+" :: "A valid directory name"*OR* |
| recover_dir | "." | Directory to look for recovery files |   ".+" :: "A valid directory name"*OR* |
| filereader_ID_dir | "." | Directory to look for input files |   ".+" :: "A valid directory name"*OR* |
| filereader_ID_files | "" | List of basenames of files to read in as initial data (e.g. omit the filename extention here) |   ".+" :: "Space-separated list of initial data filenames (basenames, e.g. excluding the file name extention)"*OR*  "^$" :: "An empty string for not recovering initial data"*OR* |
| filereader_ID_vars | "all" | List of variables to read in from the given initial data files |   "all" :: "Read all variables contained in the initial data files"*OR*  ".+"  :: "Space-separated list of fully qualified variable/group names"*OR*  "^$"  :: "An empty string for not recovering initial data"*OR* |
| recover_and_remove | "no" | Remove checkpoint file after successful recovery ? |  |
| parfile_write | "copy" | Write a parameter file to 'IO::out_dir' |   "no"               :: "Do not write a parameter file"*OR*  "copy"             :: "Copy the original parameter file"*OR*  "generate"         :: "Generate a parameter file from the current settings"*OR*#  "verbose generate" :: "Like \"generate\" but describe all parameters in detail"*OR* |
| parfile_name | "" | Filename for the parameter file to be written |   ".+" :: "A valid filename"*OR*  "^$" :: "An empty string to choose the original parameter filename"*OR* |
| parfile_update_every | 0 | How often to update the parameter file for steered parameters |   1:* :: "Every so many iterations"*OR*  0:  :: "Disable updating"*OR* |
| out_xline_y | 0.0 | y-coord for 1D lines in x-direction |   *:* :: "A value between [ymin, ymax]"*OR* |
| out_xline_z | 0.0 | z-coord for 1D lines in x-direction |   *:* :: "A value between [zmin, zmax]"*OR* |
| out_yline_x | 0.0 | x-coord for 1D lines in y-direction |   *:* :: "A value between [xmin, xmax]"*OR* |
| out_yline_z | 0.0 | z-coord for 1D lines in y-direction |   *:* :: "A value between [zmin, zmax]"*OR* |
| out_zline_x | 0.0 | x-coord for 1D lines in z-direction |   *:* :: "A value between [xmin, xmax]"*OR* |
| out_zline_y | 0.0 | y-coord for 1D lines in z-direction |   *:* :: "A value between [ymin, ymax]"*OR* |
| out_xline_yi | -2 | y-index (from 0) for 1D lines in x-direction, overrides IO::out_xline_y |    0:* :: "An index between [0, ny)"*OR*  -1:  :: "Default to physical coordinate IO::out_xline_y if it is within grid bounds, otherwise revert to using 0"*OR*  -2:  :: "Default to physical coordinate IO::out_xline_y if it is within grid bounds, otherwise revert to using the y-center of the box"*OR* |
| out_xline_zi | -2 | z-index (from 0) for 1D lines in x-direction, overrides IO::out_xline_z |    0:* :: "An index between [0, nz)"*OR*  -1:  :: "Default to physical coordinate IO::out_xline_z if it is within grid bounds, otherwise revert to using 0"*OR*  -2:  :: "Default to physical coordinate IO::out_xline_z if it is within grid bounds, otherwise revert to using the z-center of the box"*OR* |
| out_yline_xi | -2 | x-index (from 0) for 1D lines in y-direction, overrides IO::out_yline_x |    0:* :: "An index between [0, nx)"*OR*  -1:  :: "Default to physical coordinate IO::out_yline_x if it is within grid bounds, otherwise revert to using 0"*OR*  -2:  :: "Default to physical coordinate IO::out_yline_x if it is within grid bounds, otherwise revert to using the x-center of the box"*OR* |
| out_yline_zi | -2 | z-index (from 0) for 1D lines in y-direction, overrides IO::out_yline_z |    0:* :: "An index between [0, nz)"*OR*  -1:  :: "Default to physical coordinate IO::out_yline_z if it is within grid bounds, otherwise revert to using 0"*OR*  -2:  :: "Default to physical coordinate IO::out_yline_z if it is within grid bounds, otherwise revert to using the z-center of the box"*OR* |
| out_zline_xi | -2 | x-index (from 0) for 1D lines in z-direction, overrides IO::out_zline_x |    0:* :: "An index between [0, nx)"*OR*  -1:  :: "Default to physical coordinate IO::out_zline_x if it is within grid bounds, otherwise revert to using 0"*OR*  -2:  :: "Default to physical coordinate IO::out_zline_x if it is within grid bounds, otherwise revert to using the x-center of the box"*OR* |
| out_zline_yi | -2 | y-index (from 0) for 1D lines in z-direction, overrides IO::out_zline_y |    0:* :: "An index between [0, ny)"*OR*  -1:  :: "Default to physical coordinate IO::out_zline_y if it is within grid bounds, otherwise revert to using 0"*OR*  -2:  :: "Default to physical coordinate IO::out_zline_y if it is within grid bounds, otherwise revert to using the y-center of the box"*OR* |
| out_yzplane_x | 0.0 | x-coord for 2D planes in yz |   *:* :: "A value between [xmin, xmax]"*OR* |
| out_xzplane_y | 0.0 | y-coord for 2D planes in xz |   *:* :: "A value between [ymin, ymax]"*OR* |
| out_xyplane_z | 0.0 | z-coord for 2D planes in xy |   *:* :: "A value between [zmin, zmax]"*OR* |
| out_yzplane_xi | -2 | x-index (from 0) for 2D planes in yz, overrrides IO::out_yzplane_x |    0:* :: "An index between [0, nx)"*OR*  -1:  :: "Default to physical coordinate IO::out_yzplane_x if it is within grid bounds, otherwise revert to using 0"*OR*  -2:  :: "Default to physical coordinate IO::out_yzplane_x if it is within grid bounds, otherwise revert to using the x-center of the box"*OR* |
| out_xzplane_yi | -2 | y-index (from 0) for 2D planes in xz, overrrides IO::out_xzplane_y |   0:* :: "An index between [0, ny)"*OR*  -1:  :: "Default to physical coordinate IO::out_xzplane_y if it is within grid bounds, otherwise revert to using 0"*OR*  -2:  :: "Default to physical coordinate IO::out_xzplane_y if it is within grid bounds, otherwise revert to using the y-center of the box"*OR* |
| out_xyplane_zi | -2 | z-index (from 0) for 2D planes in xy, overrrides IO::out_xyplane_z |    0:* :: "An index between [0, nz)"*OR*  -1:  :: "Default to physical coordinate IO::out_xyplane_z if it is within grid bounds, otherwise revert to using 0"*OR*  -2:  :: "Default to physical coordinate IO::out_xyplane_z if it is within grid bounds, otherwise revert to using the z-center of the box"*OR* |
| truncate_files | "yes" | Truncate existing output files from previous runs (except when recovering) ? |  |
| truncate_files_after_recovering | "no" | Truncate existing output files after recovering ? |  |


### IOBasic

Cactus Code Thorn IOBasic
Author(s)    : Gabrielle Allen
               Thomas Radke
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn provides two I/O methods "Info" and "Scalar" which output grid
variables as scalars as a function on time.

Grid scalars:
 - Output the value of the scalar straight forwardly

Grid functions:
 - Output the reduction value of the grid function versus time.
   For this you also need to activate a thorn which implements reduction
   operators (eg. PUGHReduce which provides the standard reductions
   "minimum", "maximum", "sum", "norm1", "norm2", and "norm_inf").


The "Info" method would print the values, along with the current iteration
number and the physical time, in a formatted table to stdout.

The "Scalar" methods outputs to ASCII files which can be processed with
gnuplot or xgraph.


2. Additional information

There is more comprehensive documention available in this thorn's
doc/documentation.tex file which is included in the Cactus Thorn Guide.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| out_dir | "" | Output directory for IOBasic's scalar files, overrides IO::out_dir |   ".+" :: "A valid directory name"*OR*  "^$" :: "An empty string to choose the default from IO::out_dir"*OR* |
| outInfo_vars | "" | Variables to output as Info to screen |   ".+" :: "Space-separated list of fully qualified variable/group names"*OR*  "^$" :: "An empty string to output nothing"*OR* |
| outScalar_vars | "" | Variables to output into files |   ".+" :: "Space-separated list of fully qualified variable/group names"*OR*  "^$" :: "An empty string to output nothing"*OR* |
| outInfo_reductions | "minimum | List of reductions to output as Info to screen |   ".+" :: "Space-separated list of reduction operators"*OR* |
| outScalar_reductions | "minimum | List of reductions to output into files |   ".+" :: "Space-separated list of reduction operators"*OR* |
| outInfo_criterion | "iteration" | Criterion to select Info output intervals |   "never"     :: "Never output"*OR*  "iteration" :: "Output every so many iterations"*OR*  "time"      :: "Output every that much coordinate time"*OR* |
| outInfo_every | -1 | How often to do Info output |    1:* :: "Every so many iterations"*OR*   0:  :: "Disable Info output"*OR*  -1:  :: "Default to IO::out_every"*OR* |
| outInfo_dt | -2 | How often to do Info output |   (0:* :: "In intervals of that much coordinate time"*OR*   0   :: "As often as possible"*OR*  -1   :: "Disable output"*OR*  -2   :: "Default to IO::out_dt"*OR* |
| outScalar_criterion | "iteration" | Criterion to select Scalar output intervals |   "never"     :: "Never output"*OR*  "iteration" :: "Output every so many iterations"*OR*  "time"      :: "Output every that much coordinate time"*OR* |
| outScalar_every | -1 | How often to do Scalar output |    1:* :: "Every so many iterations"*OR*   0:  :: "Disable Scalar output"*OR*  -1:  :: "Default to IO::out_every"*OR* |
| outScalar_dt | -2 | How often to do Scalar output |   (0:* :: "In intervals of that much coordinate time"*OR*   0   :: "As often as possible"*OR*  -1   :: "Disable output"*OR*  -2   :: "Default to IO::out_dt"*OR* |
| outScalar_style | "xgraph" | Which style for Scalar output |   "gnuplot" :: "1D output readable by gnuplot"*OR*  "xgraph"  :: "1D output readable by xgraph"*OR* |
| out_format | ".13f" | Which format for Scalar floating-point number output |   "^(\.[1]?[0-9])?[EGefg]$" :: "output with given precision in exponential / floating point notation"*OR* |


### IOASCII

Cactus Code Thorn IOASCII
Author(s)    : Gabrielle Allen
               Tom Goodale
               Thomas Radke
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn does 1D, 2D, and 3D output of 3D variables in ASCII format.

1D output slices through the edge (in octant mode) or center
(in all origin-centered modes) of the grid in the x,y, and z directions.
If the grid is cubed it will also slice in the diagonal direction.
Ouput files can be processed with either gnuplot or xgraph.

2D output is done for the xy, xz, and yz plane.
Ouput files can be processed with the splot command in gnuplot.

3D output is simply done for the full 3D grid variables.
Ouput files can be processed with the splot command in gnuplot.

The IO methods "IOASCII_1D", "IOASCII_2D", and "IOASCII_3D" are registered 
which can be called by other thorns via CCTK_OutputVarAsByMethod()
to output a variable without specifying it in the parameter file.


2. Additional information


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| out1D_dir | "" | Output directory for 1D IOASCII files, overrides IO::out_dir |   ".+" :: "A valid directory name"*OR*  "^$" :: "An empty string to choose the default from IO::out_dir"*OR* |
| out2D_dir | "" | Output directory for 2D IOASCII files, overrides out_dir |   ".+" :: "A valid directory name"*OR*  "^$" :: "An empty string to choose the default from IO::out_dir"*OR* |
| out3D_dir | "" | Output directory for 3D IOASCII files, overrides IO::out_dir |   ".+" :: "A valid directory name"*OR*  "^$" :: "An empty string to choose the default from IO::out_dir"*OR* |
| out1D_vars | "" | Variables to output in 1D IOASCII file format |   ".+" :: "Space-separated list of fully qualified variable/group names"*OR*  "^$" :: "An empty string to output nothing"*OR* |
| out2D_vars | "" | Variables to output in 2D IOASCII file format |   ".+" :: "Space-separated list of fully qualified variable/group names"*OR*  "^$" :: "An empty string to output nothing"*OR* |
| out3D_vars | "" | Variables to output in 3D IOASCII file format |   ".+" :: "Space-separated list of fully qualified variable/group names"*OR*  "^$" :: "An empty string to output nothing"*OR* |
| out1D_every | -1 | How often to do 1D IOASCII output, overrides IO::out_every |    1:* :: "Every so many iterations"*OR*   0:  :: "Disable 1D IOASCII output"*OR*  -1:  :: "Default to IO::out_every"*OR* |
| out2D_every | -1 | How often to do 2D IOASCII output, overrides IO::out_every |    1:* :: "Every so many iterations"*OR*   0:  :: "Disable 2D IOASCII output"*OR*  -1:  :: "Default to IO::out_every"*OR* |
| out3D_every | -1 | How often to do 3D IOASCII output, overrides IO::out_every |    1:* :: "Every so many iterations"*OR*   0:  :: "Disable 3D IOASCII output"*OR*  -1:  :: "Default to IO::out_every"*OR* |
| out1D_style | "xgraph" | Which style for 1D lines IOASCII output |   "xgraph"         :: "f over x plots suitable for xgraph"*OR*  "gnuplot f(x)"   :: "f over x plots suitable for gnuplot"*OR*  "gnuplot f(t,x)" :: "f over t,x plots suitable for gnuplot"*OR* |
| out2D_style | "gnuplot | Which style for 2D slices IOASCII output |   "gnuplot f(x,y)"   :: "f over x,y plots suitable for gnuplot"*OR*  "gnuplot f(t,x,y)" :: "f over t,x,y plots suitable for gnuplot"*OR* |
| out3D_style | "gnuplot | Which style for 3D volume IOASCII output |   "gnuplot f(x,y,z)"   :: "f over x,y,z plots suitable for gnuplot"*OR*  "gnuplot f(t,x,y,z)" :: "f over t,x,y,z plots suitable for gnuplot"*OR* |
| out_format | ".13f" | Which format for IOASCII floating-point number output |   "^(\.[1]?[0-9])?[EGefg]$" :: "output with given precision in exponential / floating point notation"*OR* |
| out1D_x | "yes" | Do 1D IOASCII output in the x-direction |  |
| out1D_y | "yes" | Do 1D IOASCII output in the y-direction |  |
| out1D_z | "yes" | Do 1D IOASCII output in the z-direction |  |
| out1D_d | "yes" | Do 1D IOASCII output in the diagonal-direction |  |
| out1D_xline_y | -424242 | y-coord for 1D lines in x-direction |   *:*      :: "A value between [ymin, ymax]"*OR*  -424242: :: "Default to IO::out_xline_y"*OR* |
| out1D_xline_z | -424242 | z-coord for 1D lines in x-direction |   *:*      :: "A value between [zmin, zmax]"*OR*  -424242: :: "Default to IO::out_xline_z"*OR* |
| out1D_yline_x | -424242 | x-coord for 1D lines in y-direction |   *:*      :: "A value between [xmin, xmax]"*OR*  -424242: :: "Default to IO::out_yline_x"*OR* |
| out1D_yline_z | -424242 | z-coord for 1D lines in y-direction |   *:*      :: "A value between [zmin, zmax]"*OR*  -424242: :: "Default to IO::out_yline_z"*OR* |
| out1D_zline_x | -424242 | x-coord for 1D lines in z-direction |   *:*      :: "A value between [xmin, xmax]"*OR*  -424242: :: "Default to IO::out_zline_x"*OR* |
| out1D_zline_y | -424242 | y-coord for 1D lines in z-direction |   *:*      :: "A value between [ymin, ymax]"*OR*  -424242: :: "Default to IO::out_zline_y"*OR* |
| out1D_xline_yi | -1 | y-index (from 0) for 1D lines in x-direction |    0:* :: "An index between [0, ny)"*OR*  -1:  :: "Choose the default from IO::out_xline_yi"*OR* |
| out1D_xline_zi | -1 | z-index (from 0) for 1D lines in x-direction |    0:* :: "An index between [0, nz)"*OR*  -1:  :: "Choose the default from IO::out_xline_zi"*OR* |
| out1D_yline_xi | -1 | x-index (from 0) for 1D lines in y-direction |    0:* :: "An index between [0, nx)"*OR*  -1:  :: "Choose the default from IO::out_yline_xi"*OR* |
| out1D_yline_zi | -1 | z-index (from 0) for 1D lines in y-direction |    0:* :: "An index between [0, nz)"*OR*  -1:  :: "Choose the default from IO::out_yline_zi"*OR* |
| out1D_zline_xi | -1 | x-index (from 0) for 1D lines in z-direction |    0:* :: "An index between [0, nx)"*OR*  -1:  :: "Choose the default from IO::out_zline_xi"*OR* |
| out1D_zline_yi | -1 | y-index (from 0) for 1D lines in z-direction |    0:* :: "An index between [0, ny)"*OR*  -1:  :: "Choose the default from IO::out_zline_yi"*OR* |
| out2D_yzplane_x | -424242 | x-coord for 2D planes in yz |   *:*      :: "A value between [xmin, xmax]"*OR*  -424242: :: "Default to IO::out_yzplane_x"*OR* |
| out2D_xzplane_y | -424242 | y-coord for 2D planes in xz |   *:*      :: "A value between [ymin, ymax]"*OR*  -424242: :: "Default to IO::out_xzplane_y"*OR* |
| out2D_xyplane_z | -424242 | z-coord for 2D planes in xy |   *:*      :: "A value between [zmin, zmax]"*OR*  -424242: :: "Default to IO::out_xyplane_z"*OR* |
| out2D_yzplane_xi | -1 | x-index (from 0) for 2D planes in yz |    0:* :: "An index between [0, nx)"*OR*  -1:  :: "Choose the default from IO::out_yzplane_xi"*OR* |
| out2D_xzplane_yi | -1 | y-index (from 0) for 2D planes in xz |    0:* :: "An index between [0, ny)"*OR*  -1:  :: "Choose the default from IO::out_xzplane_yi"*OR* |
| out2D_xyplane_zi | -1 | z-index (from 0) for 2D planes in xy |    0:* :: "An index between [0, nz)"*OR*  -1:  :: "Choose the default from IO::out_xyplane_zi"*OR* |


### Boundary

Cactus Code Thorn Boundary
Author(s)    : Gabrielle Allen
               Gerd Lanfermann
               Miguel Alcubierre
               David Rideout
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn defines a generic interface for boundary conditions, which
allows, among other things, symmetry boundary conditions associated
with the grid to be applied without other thorns having knowledge of
them.

This thorn also provides some standard outer boundary conditions.

Currently:

  - scalar boundary conditions

  - flat boundary conditions (a copy of the point just inside the
    boundary)
 
  - radiation boundary conditions

  - copy boundary conditions 

  - Robin boundary conditions

  - static boundary conditions


2. Additional information

Each of the boundary conditions mentioned above work only on grid
variables with less than four dimensions.

All the boundary conditions work with an arbitrarily size stencil
width, except for the Robin boundary condition which currently only
works with a stencil width of one.

The Robin boundary condition is only implemented for 3D grid functions
at the moment.



| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| radpower | -1 | Power of decay rate in extrapolation used in radiative boundaries |  : :: "A negative value switches off this feature"*OR* |
| register_scalar | "yes" | Register routine to handle the 'Scalar' boundary condition |  |
| register_flat | "yes" | Register routine to handle the 'Flat' boundary condition |  |
| register_radiation | "yes" | Register routine to handle the 'Radiation' boundary condition |  |
| register_copy | "yes" | Register routine to handle the 'Copy' boundary condition |  |
| register_robin | "yes" | Register routine to handle the 'Robin' boundary condition |  |
| register_static | "yes" | Register routine to handle the 'Static' boundary condition |  |
| register_none | "yes" | Register routine to handle the 'None' boundary condition |  |


### SymBase

Cactus Code Thorn SymBase
Author(s)    : Erik Schnetter
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Provide generic handling of symmetries for grids and grid arrays.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| verbose | "yes" | Output symmetry boundary face descriptions after registration |  |


### Time

Cactus Code Thorn Time
Author(s)    : Gabrielle Allen
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Thorn Time sets the timestep for the simulation by either

	- setting the timestep directly from a parameter value

	- using a Courant-type condition to set the timestep based
	  on the grid-spacing used.

The Courant condition can be applied either once at the start of the
simulation, or the timestep can be calculated dynamically before each timestep.





| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| timestep_method | "courant_static" | Method for calculating timestep |  "given"          :: "Use given timestep"*OR* "courant_static" :: "Courant condition at BASEGRID (using dtfac)"*OR* "courant_speed"  :: "Courant condition at POSTSTEP (using wavespeed and courant_fac)"*OR* "courant_time"   :: "Courant condition at POSTSTEP (using min time and courant_fac)"*OR* |
| timestep_outonly | "no" | Don't set a dynamic timestep, just output what it would be |  |
| timestep | 0.0 | Absolute value for timestep |  *:* :: "Could be anything"*OR* |
| dtfac | 0.5 | The standard timestep condition dt = dtfac*max(delta_space) |  0:* :: "For positive timestep"*OR* *:0 :: "For negative timestep"*OR* |
| courant_fac | 0.9 | The courant timestep condition dt = courant_fac*max(delta_space)/speed/sqrt(dim) |  0:* :: "For positive timestep"*OR* *:0 :: "For negative timestep"*OR* |
| timestep_outevery | 1 | How often to output courant timestep |  1:* :: "Zero means no output"*OR* |
| verbose | "no" | Give selective information about timestep setting |  |


### InitBase

Cactus Code Thorn InitBase
Author(s)    : Erik Schnetter
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Thorn InitBase specifies how initial data are to be set up.  It does
not set up any initial data by itself, nor does it contain any
routines which are to be called.  It is merely a convenient repository
remembering how initial data are to be set up, so that other thorns
can check their actions against this thorn.

There are several possibilities:

1. The initial data thorn sets up data on one time level, while other
   time levels are scratch space.  The time evolution method must
   start up from a single time level.  (This is the default.)

2. The initial data thorn sets up data on exactly one time level, and
   is called once for each active time level.  (This means that the
   initial data thorn can only access the current time level.)

3. The initial data thorn sets up data on exactly two time levels, and
   is called once for each active time level.  (This means that the
   initial data thorn can only access the current and the first past
   time level.)

4. The initial data thorn sets up data on all active time levels.
   (This makes it necessary that the initial data thorn checks the
   number of active time levels.)


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| initial_data_setup_method | "init_some_levels" | Procedure for setting up initial data |   "init_some_levels"  :: "Set up at least one time level; other time levels are scratch space"*OR*  "init_single_level" :: "Set up exactly one time level; other time levels are not accessed"*OR*  "init_two_levels"   :: "Set up exactly two time levels; other time levels are not accessed"*OR*  "init_all_levels"   :: "Set up all active time levels"*OR* |


### Fortran

Cactus Code Thorn Fortran
Thorn Author(s)     : Erik Schnetter <schnetter@aei.mpg.de>
Thorn Maintainer(s) : Erik Schnetter <schnetter@aei.mpg.de>
--------------------------------------------------------------------------

Purpose of the thorn:

Provide Fortran interfaces for the flesh functions.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| one | 1 | This parameter has the value one |   1:1 :: "1"*OR* |


### CoordBase

Cactus Code Thorn CoordBase
Author(s)    : Cactus team
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

Provides generic handling of coordinates for grid variables.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| domainsize | "minmax" | Domain size specification |   "minmax"      :: "lower and upper boundary locations"*OR*  "extent"      :: "coordinate extent"*OR*  "spacing"     :: "grid spacing and number of grid cells"*OR* |
| spacing | "gridspacing" | Grid spacing specification |   "gridspacing" :: "grid spacing"*OR*  "numcells"    :: "number of grid cells"*OR* |
| zero_origin_x | "no" | Is the lower boundary located at x=0? |  |
| xmin | 0.0 | Location of lower x boundary |   (*:*) :: ""*OR* |
| xmax | 1.0 | Location of upper x boundary |   (*:*) :: ""*OR* |
| xextent | 1.0 | Domain extent in x direction |   (0:*) :: ""*OR* |
| dx | 1.0 | Grid spacing in x direction |   (0:*) :: ""*OR* |
| ncells_x | 1 | Number of grid cells in x direction |   0:* :: ""*OR* |
| zero_origin_y | "no" | Is the lower boundary located at y=0? |  |
| ymin | 0.0 | Location of lower y boundary |   (*:*) :: ""*OR* |
| ymax | 1.0 | Location of upper y boundary |   (*:*) :: ""*OR* |
| yextent | 1.0 | Domain extent in y direction |   (0:*) :: ""*OR* |
| dy | 1.0 | Grid spacing in y direction |   (0:*) :: ""*OR* |
| ncells_y | 1 | Number of grid cells in y direction |   0:* :: ""*OR* |
| zero_origin_z | "no" | Is the lower boundary located at z=0? |  |
| zmin | 0.0 | Location of lower z boundary |   (*:*) :: ""*OR* |
| zmax | 1.0 | Location of upper z boundary |   (*:*) :: ""*OR* |
| zextent | 1.0 | Domain extent in z direction |   (0:*) :: ""*OR* |
| dz | 1.0 | Grid spacing in z direction |   (0:*) :: ""*OR* |
| ncells_z | 1 | Number of grid cells in z direction |   0:* :: ""*OR* |
| boundary_size_x_lower | 1 | Boundary zones at the lower x face |   0:* :: ""*OR* |
| boundary_internal_x_lower | "no" | Do the boundary points extend inwards at the lower x face? |  |
| boundary_staggered_x_lower | "no" | Is the boundary is staggered about the grid points at the lower x face? |  |
| boundary_shiftout_x_lower | 0 | Offset between the boundary location and the first boundary point at the lower x face |   *:* :: "when not staggered: use 0 for an open, 1 for a closed manifold"*OR* |
| boundary_size_x_upper | 1 | Boundary zones at the upper x face |   0:* :: ""*OR* |
| boundary_internal_x_upper | "no" | Do the boundary points extend inwards at the upper x face? |  |
| boundary_staggered_x_upper | "no" | Is the boundary is staggered about the grid points at the upper x face? |  |
| boundary_shiftout_x_upper | 0 | Offset between the boundary location and the first boundary point at the upper x face |   *:* :: "when not staggered: use 0 for an open, 1 for a closed manifold"*OR* |
| boundary_size_y_lower | 1 | Boundary zones at the lower y face |   0:* :: ""*OR* |
| boundary_internal_y_lower | "no" | Do the boundary points extend inwards at the lower y face? |  |
| boundary_staggered_y_lower | "no" | Is the boundary is staggered about the grid points at the lower y face? |  |
| boundary_shiftout_y_lower | 0 | Offset between the boundary location and the first boundary point at the lower y face |   *:* :: "when not staggered: use 0 for an open, 1 for a closed manifold"*OR* |
| boundary_size_y_upper | 1 | Boundary zones at the upper y face |   0:* :: ""*OR* |
| boundary_internal_y_upper | "no" | Do the boundary points extend inwards at the upper y face? |  |
| boundary_staggered_y_upper | "no" | Is the boundary is staggered about the grid points at the upper y face? |  |
| boundary_shiftout_y_upper | 0 | Offset between the boundary location and the first boundary point at the upper y face |   *:* :: "when not staggered: use 0 for an open, 1 for a closed manifold"*OR* |
| boundary_size_z_lower | 1 | Boundary zones at the lower z face |   0:* :: ""*OR* |
| boundary_internal_z_lower | "no" | Do the boundary points extend inwards at the lower z face? |  |
| boundary_staggered_z_lower | "no" | Is the boundary is staggered about the grid points at the lower z face? |  |
| boundary_shiftout_z_lower | 0 | Offset between the boundary location and the first boundary point at the lower z face |   *:* :: "when not staggered: use 0 for an open, 1 for a closed manifold"*OR* |
| boundary_size_z_upper | 1 | Boundary zones at the upper z face |   0:* :: ""*OR* |
| boundary_internal_z_upper | "no" | Do the boundary points extend inwards at the upper z face? |  |
| boundary_staggered_z_upper | "no" | Is the boundary is staggered about the grid points at the upper z face? |  |
| boundary_shiftout_z_upper | 0 | Offset between the boundary location and the first boundary point at the upper z face |   *:* :: "when not staggered: use 0 for an open, 1 for a closed manifold"*OR* |


### CartGrid3D

Cactus Code Thorn CartGrid3D
Author(s)    : Gabrielle Allen
               Gerd Lanfermann
               Joan Masso
Maintainer(s): Cactus team
Licence      : LGPL
--------------------------------------------------------------------------

1. Purpose

This thorn sets up a Cartesian grid, for a given domain. It also
provides a method for registering symmetries of Grid Functions
across the grid axes, and a call for applying symmetry boundary
conditions.

CartGrid3d also registers a coordinate system spher3d, under the old
API, but this is deprecated.  Spherical coordinate systems will be
provided by another thorn in the future.


2. Additional information

This thorn currently only works in 3D, in that it creates 3D grid functions.
It registers Cartesian coordinate systems of 1, 2, and 3 dimensions.


| Key | Defaults | Describe | Option |
| ------------ | ------------- | ------------- | ------------- |
| no_origin | "yes" | DEPRECATED: Don't place grid points on the coordinate origin/axes |  : :: ""*OR* |
| no_originx | "yes" | DEPRECATED: Don't place grid points on the x-coordinate origin/axes |  : :: ""*OR* |
| no_originy | "yes" | DEPRECATED: Don't place grid points on the y-coordinate origin/axes |  : :: ""*OR* |
| no_originz | "yes" | DEPRECATED: Don't place grid points on the z-coordinate origin/axes |  : :: ""*OR* |
| avoid_originx | "yes" | Don't place grid points on the x-coordinate origin/axes |  : :: ""*OR* |
| avoid_originy | "yes" | Don't place grid points on the y-coordinate origin/axes |  : :: ""*OR* |
| avoid_originz | "yes" | Don't place grid points on the z-coordinate origin/axes |  : :: ""*OR* |
| avoid_origin | "yes" | Don't place grid points on the coordinate origin/axes |  : :: ""*OR* |
| register_default_coordinate_systems | "yes" | register cartnd as the default coordinate systems |  |
| dx | 0.3 | Coarse grid spacing in x-direction |   0:* :: "Positive"*OR* |
| dy | 0.3 | Coarse grid spacing in y-direction |   0:* :: "Positive"*OR* |
| dz | 0.3 | Coarse grid spacing in z-direction |   0:* :: "Positive"*OR* |
| dxyz | 0.0 | Coarse grid spacing in x,y,z-directions |   0:* :: "Positive"*OR* |
| xmin | -1.0 | Coordinate minimum in x-direction |   : :: "Anything"*OR* |
| ymin | -1.0 | Coordinate minimum in y-direction |   : :: "Anything"*OR* |
| zmin | -1.0 | Coordinate minimum in z-direction |   : :: "Anything"*OR* |
| xyzmin | -424242 | Coordinate minimum in x,y,z-directions |   : :: "Anything"*OR* |
| xmax | 1.0 | Coordinate maximum in x-direction |   : :: "Anything"*OR* |
| ymax | 1.0 | Coordinate maximum in y-direction |   : :: "Anything"*OR* |
| zmax | 1.0 | Coordinate maximum in z-direction |   : :: "Anything"*OR* |
| xyzmax | -424242 | Coordinate maximum in xyz-directions |   : :: "Anything"*OR* |
| type | "box" | Grid type |   "box"        :: "Box grid from -0.5 to 0.5"*OR*  "byrange"    :: "Specify min and max values"*OR*  "byspacing"  :: "Specify grid spacings"*OR*  "coordbase"  :: "Get specification from CoordBase"*OR*  "multipatch" :: "Get specification from MultiPatch"*OR* |
| domain | "full" | Domain type |   "octant"     :: "Use an octant about the origin"*OR*  "quadrant"   :: "Use a quadrant in x-y plane"*OR*  "quadrant_reflect_rotate" :: "Use a quadrant with rotation symmetry about an axis"*OR*  "bitant"     :: "Use a bitant about the x-y plane"*OR*  "bitant_rotate"   :: "Use a bitant with rotation symmetry about an axis"*OR*  "full"       :: "Use the full domain"*OR* |
| bitant_plane | "xy" | Plane defining bitant domain |   "xy"       :: "xy-plane"*OR*  "xz"       :: "xz-plane"*OR*  "yz"       :: "yz-plane"*OR* |
| quadrant_direction | "z" | Direction defining quadrant domain |   "x"        :: "x-direction"*OR*  "y"        :: "y-direction"*OR*  "z"        :: "z-direction"*OR* |
| rotation_axis | "z" | Axis about which the rotation symmetry is to be applied |   "x"        :: "x-axis"*OR*  "y"        :: "y-axis"*OR*  "z"        :: "z-axis"*OR* |
| symmetry_xmin | "no" | Symmetry boundary condition on lower x boundary |  : :: "Logical"*OR* |
| symmetry_ymin | "no" | Symmetry boundary condition on lower y boundary |  : :: "Logical"*OR* |
| symmetry_zmin | "no" | Symmetry boundary condition on lower z boundary |  : :: "Logical"*OR* |
| symmetry_xmax | "no" | Symmetry boundary condition on upper x boundary |  : :: "Logical"*OR* |
| symmetry_ymax | "no" | Symmetry boundary condition on upper y boundary |  : :: "Logical"*OR* |
| symmetry_zmax | "no" | Symmetry boundary condition on upper z boundary |  : :: "Logical"*OR* |
| set_coordinate_ranges_on | "all | On which grids to set the coordinate ranges |   "all grids"   :: "set ranges in local mode, on the coarsest level"*OR*  "all maps"    :: "set ranges in singlemap mode, on the coarsest level"*OR*  "first level" :: "set ranges in level mode, on the first level"*OR* |