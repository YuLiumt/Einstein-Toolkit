Here we solve the Poisson equation

$$
\Delta \Phi(x)=\rho(x)
$$

where the right hand side $\rho$ is given by

$$
\rho(r)=\left\{\begin{array}{ll}{Q / V } & {r \leq R} \\ {0} & {r>R}\end{array}\right.
$$

## Grid Structure

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
ActiveThorns = "CoordBase"

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
```