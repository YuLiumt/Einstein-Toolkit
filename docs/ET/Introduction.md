Cactus components (called thorns) interact with the framework (called the flesh) via a set of configuration (CCL) files provided by each thorn.

The Cactus API supports C/C++ and F77/F90 programming languages for the thorns. This makes it easier for scientists to turn existing codes into thorns which can then make use of the complete Cactus infrastructure, and in turn be used by other thorns within Cactus.

The [Einstein Toolkit](https://www.einsteintoolkit.org/index.html) is a community-driven software platform of core computational tools to advance and support research in relativistic astrophysics and gravitational physics.

## Glossary

| Name | Describe |
| ------------ | ------------- |
| alias function | function aliasing. |
| AMR | Automatic Mesh Refinement |
| API | Applications Programming Interface, the interface provided by some software component to programmers who use the component. An API usually consists of subroutine/function calls, but may also include structure definitions and definition of constant values. The Cactus Reference Manual documents most of the Cactus flesh APIs. |
| arrangement | A collection of thorns, stored in a subdirectory of the Cactus arrangements directory. | 
| autoconf | A GNU program which builds a configuration script which can be used to make a Makefile. |
| boundary zone | A boundary zone is a set of points at the edge of a grid, interpreted as the boundary of the physical problem, and which contains boundary data. |
| CCTK | Cactus Computational Tool Kit (The Cactus flesh and computational thorns). |
| CCL | The Cactus Configuration Language, this is the language that the thorn configuration files are written in. |
| configuration | The combination of a set of thorns, and all the Cactus configure options which affect what binary will be produced when compiling Cactus. |
| checkout | Get a copy of source code from SVN. |
| checkpoint | Save the entire state of a Cactus run to a file, so that the run can be restarted at a later time.|
| computational grid | A discrete finite set of spatial points in $\mathfrak{R}^{n}$ (typically, $1 \leq n \leq 3$). Historically, Cactus has required these points to be uniformly spaced (uniformly spaced grid), but now, Cactus supports non-uniform spacings (non-uniformly spaced grid), and mesh refinement. The grid consists of the physical domain and the boundary and symmetry points. |
| CST | The Cactus Specification Tool, which is the set of Perl scripts which parse the thorns’ `.ccl` files, and generates the code that binds the thorn source files with the flesh. |
| SVN | Subversion is the favoured code distribution system for Cactus. |
| domain decomposition | The technique of breaking up a large computational problem into parts that are easier to solve. In Cactus, it refers especially to a decomposition wherein the parts are solved in parallel on separate computer processors. |
| driver | A special kind of thorn which creates and handles grid hierarchies and grid variables. Drivers are responsible for memory management for grid variables, and for all parallel operations, in response to requests from the scheduler. |
| evolution | An iteration interpreted as a step through time. Also, a particular Cactus schedule bin for executing routines when evolution occurs. |
| flesh | The Cactus routines which hold the thorns together, allowing them to communicate and scheduling things to happen with them. |
| friend | Interfaces that are friends, share their collective set of protected grid variables. |
| function aliasing | The process of referring to a function to be provided by an interface independently of which thorn actually contains the function, or what language the function is written in. The function is called an alias function. |
| GA | Shorthand for a grid array. |
| GF | Shorthand for a grid function. |
| gmake | GNU version of the make utility. |
| ghost zone | A set of points added for parallelisation purposes to a block of a grid lying on one processor, corresponding to points on the boundary of an adjoining block of the grid lying on another processor. Points from the boundary of the one block are copied (via an inter-processor communication mechanism) during synchronisation to the corresponding ghost zone of the other block, and vice versa. In single processor runs there are no ghost zones. |
| grid | Short for computational grid. |
| grid array | A grid variable whose global size need not be that of the computational grid; instead, the size is declared explicitly in an `interface.ccl` file. |
| grid function | A grid variable whose global size is the size of the computational grid. From another perspective, grid functions are functions defined on the domain of grid points. Typically, grid functions are used to discretely approximate functions defined on the domain $R^n$, with finite differencing used to approximate partial derivatives. |
| grid hierarchy | A computational grid, and the grid variables associated with it. |
| grid point | A spatial point in the computational grid. |
| grid scalar | A grid variable with index zero, i.e. just a number on each processor. |
| grid variable | A variable which is passed through the flesh interface, either between thorns or between routines of the same thorn. This implies the variable is related to the computational grid, as opposed to being an internal variable of the thorn or one of its routines. grid scalar, grid function, and grid array are all examples of grid variables. |
| GNATS | The GNU program we use for reporting and tracking bugs, comments and suggestions. |
| GNU | GNU’s Not Unix: a freely-distributable code project. |
| GV | Shorthand for grid variable. |
| handle | A signed integer value >= 0 passed by many Cactus routines and used to represent a dynamic data or code object. |
| implementation | Defines the interface that a thorn presents to the rest of a Cactus program. |
| inherit | A thorn that inherits from another implementation can access all the other implementation’s public variables. |
| interpolation | Given a set of grid variables and interpolation points (points in the grid coordinate space, which are typically distinct from the grid points), interpolation is the act of producing values for the grid variables at each interpolation point over the entire grid hierarchy. |
| local array | An array that is declared in thorn code, but not declared in the thorn’s `interface.ccl`, as opposed to a grid array. |
| local interpolation | Given a set of grid variables and interpolation points (points in the grid coordinate space ,which are typically distinct from the grid points), interpolation is the act of producing values for the grid variables at each interpolation point on a particular grid. |
| NUL character | The C programming language uses a “NUL character” to terminate character strings. A NUL character has the integer value zero, but it’s useful to write it as ’\0’, to emphasize to human readers that this has type char rather than int. |
| null pointer, NULL pointer | C defines a “null pointer”, often (slightly incorrectly) called a “NULL pointer”, which is guaranteed not to point to any object. You get a null pointer by converting the integer constant 0 to a pointer type, e.g. `int* ptr = 0;`. |
| parallelisation | The process of utilising multiple computer processors to work on different parts of a computational problem at the same time, in order to obtain a solution of the problem more quickly. Cactus achieves parallelisation by means of domain decomposition. | 
| parameter | A variable that controls the run time behaviour of the Cactus executable. Parameters have default values which can be set in a parameter file. |
| parameter file | Also called par file.) A text file used as the input of a Cactus program, specifying initial values of thorn parameters. |
| PUGH | The default driver thorn for Cactus which uses MPI. |
| PVM | Parallel Virtual Machine, provides interprocessor communication. |
| reduction | Given a set of grid variables on a computational grid, reduction is the process of producing values for the variables on a proper subset of points from the grid. |
| scheduler | The part of the Cactus flesh that determines the order and circumstances in which to execute Cactus routines. Thorn functions and schedule groups are registered with the flesh via the thorn’s `schedule.ccl` file to be executed in a certain schedule bin, before or after another function or group executes, and so forth. |
| schedule bin | One of a set of special timebins pre-defined by Cactus. |
| schedule group | A timebin defined by a thorn, in its `schedule.ccl` file. Each schedule group must be defined to occur in a Cactus schedule bin or another schedule group. |
| shares | An implementation may share restricted parameters with another implementation, which means the other implementation can get the parameter values, and if the parameters are steerable, it can change them. |
| steerable parameter | A parameter which can be changed at any time after the program has been initialised. |
| symmetry operation | A grid operation that is a manifestation of a geometrical symmetry, especially rotation or reflection. |
| symmetry zone | A set of points laying at the edge of the computational grid and containing data obtained by some symmetry operation from another part of the same grid. |
| synchronisation | The process of copying information from the outer part of a computational interior on one processor to the corresponding ghost zone (see) on another processor. Also refers to a special Cactus timebin corresponding to the occurrence of this process. | 
| thorn | A collection of subroutines defining a Cactus interface. |
| ThornList | A file used by the Cactus CST to determine which thorns to compile into a Cactus executable. Can also be used to determine which thorns to check out from SVN. |
| time bin | A time interval in the duration of a Cactus run wherein the flesh runs specified routines. See scheduler, schedule bin. |
