Here we solve the Poisson equation

$$
\Delta \Phi(x)=\rho(x)
$$

where the right hand side $\rho$ is given by

$$
\rho(r)=\left\{\begin{array}{ll}{Q / V } & {r \leq R} \\ {0} & {r>R}\end{array}\right.
$$

## Parameter File

https://www.einsteintoolkit.org/gallery/poisson/poisson.par

### Flesh parameters

```
# Description of this simulation
Cactus::cctk_run_title = "Solving Poisson's equation with CT_MultiLevel (multigrid setup, FMG)"

# Give detailed information for each warning statement
Cactus::cctk_full_warnings         = yes
Cactus::highlight_warning_messages = no

# Give timing information
Cactus::cctk_timer_output          = "off"

# Condition on which to terminate evolution loop
Cactus::terminate       = "any"
Cactus::cctk_initial_time = 0
Cactus::cctk_final_time = 0
# Final iteration number ?
Cactus::cctk_itlast     = 0
```

### Grid Structure

To do so, we first have to construct a numerical grid that covers an interval between $x_{min}$ and $x_{max}$. We then divide the interval $\left[x_{\min }, x_{\max }\right]$ into N gridcells, leading to a gridspacing of

$$
\Delta x=\frac{x_{\max }-x_{\min }}{N}
$$

We can choose our grid points to be located either at the center of these cells, which would be referred to as a cell-centered grid, or on the vertices, which would be referred to as a vertex-centered grid. For a cell-centered grid we have N grid points located at

$$
x_{i}=x_{\min }+(i-1 / 2) \Delta x, \quad i=1, \ldots, N
$$

![-w1252](media/15516017095776.jpg)

whereas for a vertex centered grid we have N + 1 gridpoints located a

$$
x_{i}=x_{\min }+(i-1) \Delta x, \quad i=1, \ldots, N+1
$$

For now, the difference between cell-centered and vertex-centered grids only affects the implementation of boundary conditions, but not the finite difference representation of the differential equation itself.


```
ActiveThorns = "Boundary CartGrid3D CoordBase Slab SymBase"

CoordBase::domainsize = "minmax"

CoordBase::xmin =  -1.00
CoordBase::ymin =  -1.00
CoordBase::zmin =  -1.00
CoordBase::xmax =  +1.00
CoordBase::ymax =  +1.00
CoordBase::zmax =  +1.00
CoordBase::dx   =  0.25
CoordBase::dy   =  0.25
CoordBase::dz   =  0.25

CoordBase::boundary_size_x_lower     = 3
CoordBase::boundary_size_y_lower     = 3
CoordBase::boundary_size_z_lower     = 3
CoordBase::boundary_size_x_upper     = 3
CoordBase::boundary_size_y_upper     = 3
CoordBase::boundary_size_z_upper     = 3

CoordBase::boundary_shiftout_x_lower = 1
CoordBase::boundary_shiftout_y_lower = 1
CoordBase::boundary_shiftout_z_lower = 1
CoordBase::boundary_shiftout_x_upper = 1
CoordBase::boundary_shiftout_y_upper = 1
CoordBase::boundary_shiftout_z_upper = 1

CartGrid3D::type = "coordbase"
```