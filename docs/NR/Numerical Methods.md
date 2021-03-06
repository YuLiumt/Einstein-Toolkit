There are two major classes of techniques used for numerical simulations of the Einstein equations. These are **finite-difference methods**, typically coupled to adaptive mesh refinement techniques, and **pseudo-spectral methods**.

!!! note 
    Most of the finite difference codes are based on modifications to the ADM system. These equations are in a form with mixed second and first derivatives. Basically, the system is such that **only first time derivatives occur, but first and second spatial derivatives occur.** (Of course, auxiliary evolution variables can be introduced so that the system only has first spatial derivatives, but at the cost of introducing additional constraints.)

A black hole interior presents a major challenge to any numerical technique because of the curvature singularity it harbors. Fortunately, the singularity is concealed behind an event horizon. The region inside the horizon cannot affect the exterior solution, so numerical simulations need not evolve it accurately.

One way to do this is to simply not evolve a region inside the horizon, i.e., to excise this region. The other method, the puncture method involves allowing singularities in the computational domain. In the appropriate gauge, these singularities are sufficiently benign that finite difference methods can handle them.

## Finite Difference Methods

**In a finite difference approximation a function $f(t,x)$ is represented by values at a discrete set of points.** At the core of finite difference approximation is therefore a discretization of the spacetime, or a numerical grid. Instead of evaluating f at all values of x, for example, we only consider discrete values $x_i$. The distance between the gridpoints $x_i$ is called the gridspacing $∆x$. For uniform grids, for which $∆x$ is constant, we have

$$
x _ { i } = x _ { 0 } + i \Delta x
$$

If the solution depends on time we also discretize the time coordinate, for example as

$$
t ^ { n } = t ^ { 0 } + n \Delta t
$$

The finite difference representation of the function $f(t,x)$, for example, is

$$
f _ { i } ^ { n } = f \left( t ^ { n } , x _ { i } \right) + \text { truncation error. }
$$

Differential equations involve derivatives, so we must next discuss how to represent derivatives in a finite difference representation.

Assuming that $f(x)$ can be differentiated to sufficiently high order and that it can be represented as a Taylor series, we have

$$
f _ { i + 1 } = f \left( x _ { i } + \Delta x \right) = f \left( x _ { i } \right) + \Delta x \left( \partial _ { x } f \right) _ { x _ { i } } + \frac { ( \Delta x ) ^ { 2 } } { 2 } \left( \partial _ { x } ^ { 2 } f \right) _ { x _ { i } } + \mathcal { O } \left( \Delta x ^ { 3 } \right)
$$

Solving for $\left( \partial _ { x } f \right) _ { x _ { i } } = \left( \partial _ { x } f \right) _ { i }$ we find

$$
\left( \partial _ { x } f \right) _ { i } = \frac { f _ { i + 1 } - f _ { i } } { \Delta x } + \mathcal { O } ( \Delta x )
$$

The truncation error of this expression is linear in $∆x$, and it turns out that we can do better. We call equation a **one-sided derivative**, since it uses only neighbors on one side of $x_i$.

Consider the Taylor expansion to the point $x_{ i − 1 }$,

$$
f _ { i - 1 } = f \left( x _ { i } - \Delta x \right) = f \left( x _ { i } \right) - \Delta x \left( \partial _ { x } f \right) _ { x _ { i } } + \frac { ( \Delta x ) ^ { 2 } } { 2 } \left( \partial _ { x } ^ { 2 } f \right) _ { x _ { i } } + \mathcal { O } \left( \Delta x ^ { 3 } \right)
$$

we now find

$$
\left( \partial _ { x } f \right) _ { i } = \frac { f _ { i + 1 } - f _ { i - 1 } } { 2 \Delta x } + \mathcal { O } \left( \Delta x ^ { 2 } \right)
$$

which is second order in $∆x$. In general, **centered derivatives** lead to higher order schemes than one-sided derivatives for the same number of gridpoints.

The key point is that we are able to combine the two Taylor expansions in such a way that the leading order error term cancels out, leaving us **with a higher order representation of the derivative**. This cancellation **only works out for uniform grids**, when $∆x$ is independent of x. ==This is one of the reasons why many current numerical relativity applications of finite difference schemes work with uniform grids.==

Higher order derivatives can be constructed in a similar fashion. Adding the two Taylor expansions all terms odd in $∆x$ drop out and we find for the second derivative

$$
\left( \partial _ { x } ^ { 2 } f \right) _ { i } = \frac { f _ { i + 1 } - 2 f _ { i } + f _ { i - 1 } } { ( \Delta x ) ^ { 2 } } + \mathcal { O } \left( \Delta x ^ { 2 } \right)
$$

!!! note "Fourth-order finite-difference"
    Fourth-order finite-difference representations of the first and second derivatives of a function f are given by

    $$
    \left( \partial _ { x } f \right) _ { i } = \frac { 1 } { 12 \Delta x } \left( f _ { i - 2 } - 8 f _ { i - 1 } + 8 f _ { i + 1 } - f _ { i + 2 } \right) \\
    \left( \partial _ { x } ^ { 2 } f \right) _ { i } = \frac { 1 } { 12 ( \Delta x ) ^ { 2 } } \left( - f _ { i - 2 } + 16 f _ { i - 1 } - 30 f _ { i } + 16 f _ { i + 1 } - f _ { i + 2 } \right)
    $$
    
    where we have omitted the truncation error, $\mathcal { O } \left( \Delta x ^ { 4 } \right)$

### Elliptic Equations

As an example of a simple, one-dimensional elliptic equation consider

$$
\partial_{x}^{2} f=s
$$

We first have to construct a numerical grid that covers an interval between $x_{min}$ and $x_{max}$. We then divide the interval $\left[x_{\min }, x_{\max }\right]$ into N gridcells, leading to a gridspacing of

$$
\Delta x=\frac{x_{\max }-x_{\min }}{N}
$$

We can choose our grid points to be located either at the center of these cells, which would be referred to as a cell-centered grid, or on the vertices, which would be referred to as a vertex-centered grid. For a cell-centered grid we have N grid points located at

$$
x_{i}=x_{\min }+(i-1 / 2) \Delta x, \quad i=1, \ldots, N
$$

![](media/15516083713832.jpg)

whereas for a vertex centered grid we have N + 1 gridpoints located a

$$
x_{i}=x_{\min }+(i-1) \Delta x, \quad i=1, \ldots, N+1
$$

**The difference between cell-centered and vertex-centered grids only affects the implementation of boundary conditions, but not the finite difference representation of the differential equation itself.**

We are now ready to finite difference the differential equation. We define two arrays, $f_i$ and $s_i$, which represent the functions f and s at the gridpoints $x_i$ for $i = 1, . . . , N$. In the interior of our domain we can represent the differential equation as

$$
f_{i+1}-2 f_{i}+f_{i-1}=(\Delta x)^{2} s_{i} \quad i=2, \ldots, N-1
$$

At the lower boundary point $i = 1$ the neighbor $i − 1$ does not exist in our domain, and, similarly, at the upper boundary point $i = N$ the point $i + 1$ does not exist. At these points we have to implement the boundary conditions, which can be done in many different ways.

Let us assume that the solution f is a symmetric function about $x = 0$, in which case we can restrict the analysis to positive x and impose a Neuman condition at the origin,
    
$$
\partial_{x} f=0 \quad \text { at } x=0
$$
 
The two grid points $x_0$ and $x_1$ then bracket the boundary point $x_{min} = 0$ symmetrically. We can then write the boundary condition as
    
$$
f_{1}=f_{0}
$$
    
For i = 1 we yields

$$
f_{i+1}-f_{i}=(\Delta x)^{2} s_{i} \quad i=1
$$

We can use a similar strategy at the upper boundary. Let us also assume that f falls off with $1/x$ for large x, which results in the Robin boundary condition

$$
\partial_{x}(x f)=0 \quad \text { as } x \rightarrow \infty
$$

With the help of a virtual grid point $x_{N + 1}$ we can write the boundary condition in $\Delta x$ as

$$
f_{N+1}=\frac{x_{N}}{x_{N+1}} f_{N}=\frac{x_{N}}{x_{N}+\Delta x} f_{N}
$$

We can again insert this into for i = N and find

$$
\left(\frac{x_{i}}{x_{i}+\Delta x}-2\right) f_{i}+f_{i-1}=(\Delta x)^{2} s_{i} \quad i=N
$$

Elliptic Equations now form a coupled set of N linear equations for the N elements $f_i$ that we can write as

$$
\left( \begin{array}{ccccccc}{-1} & {1} & {0} & {0} & {0} & {0} & {0} \\ {1} & {-2} & {1} & {0} & {0} & {0} & {0} \\ {0} & {\ddots} & {\ddots} & {\ddots} & {0} & {0} & {0} \\ {0} & {0} & {1} & {-2} & {1} & {0} & {0} \\ {0} & {0} & {0} & {\ddots} & {\ddots} & {\ddots} & {0} \\ {0} & {0} & {0} & {0} & {1} & {-2} & {1} \\ {0} & {0} & {0} & {0} & {0} & {1} & {x_{N} /\left(x_{N}+\Delta x\right)-2}\end{array}\right) \cdot \left( \begin{array}{c}{f_{1}} \\ {f_{2}} \\ {\vdots} \\ {f_{i}} \\ {\vdots} \\ {f_{N-1}} \\ {f_{N}}\end{array}\right)=(\Delta x)^{2} \left( \begin{array}{c}{s_{1}} \\ {s_{2}} \\ {\vdots} \\ {s_{i}} \\ {\vdots} \\ {s_{N-1}} \\ {s_{N}}\end{array}\right)
$$

or, in a more compact form,

$$
\mathbf{A} \cdot \mathbf{f}=(\Delta x)^{2} \mathbf{S}
$$

The solution is given by

$$
\mathbf{f}=(\Delta x)^{2} \mathbf{A}^{-1} \cdot \mathbf{S}
$$

so that we have reduced the problem to inverting an N × N matrix.

### Hyperbolic Equations

For simplicity it does not contain any source terms, and the the wave speed v is constant.

$$
\partial _ { t } u + v \partial _ { x } u = 0
$$

The equation is satisfied exactly by any function of the form $u ( t , x ) = u ( x - v t )$. **The equation has a time derivative in addition to the space derivative, and thus requires initial data**.

Inserting both finite-difference representations

$$
\left( \partial _ { x } u \right) _ { j } ^ { n } = \frac { u _ { j + 1 } ^ { n } - u _ { j - 1 } ^ { n } } { 2 \Delta x } + \mathcal { O } \left( \Delta x ^ { 2 } \right) \\
\left( \partial _ { t } u \right) _ { j } ^ { n } = \frac { u _ { j } ^ { n + 1 } - u _ { j } ^ { n } } { \Delta t } + \mathcal { O } ( \Delta t )
$$

we can solve for $u^{n+1}_j$ and find

$$
u _ { j } ^ { n + 1 } = u _ { j } ^ { n } - \frac { v } { 2 } \frac { \Delta t } { \Delta x } \left( u _ { j + 1 } ^ { n } - u _ { j - 1 } ^ { n } \right)
$$

or reasons that are quite obivous this differencing scheme is called forward-time centered-space, or FTCS.

![](media/15512306516707.jpg)

It is an example of an explicit scheme, meaning that we can solve for the grid function $u _ { j } ^ { n + 1 }$ at the new time level n + 1 directly in terms of function values on the old time level n.

#### Courant-Friedrichs-Lewy condition

Unfortunately, however, FTCS is fairly useless. The equation is satisfied exactly by any function of the form $u ( t , x ) = u ( x - v t )$. we can write the solution $u ( t , x )$ to our continuum hyperbolic differential equation as a superposition of eigenmodes $e^{i(\omega t+k x)}$. Here k is a spatial wave number.

A real $\omega$, for which $e^{i \omega t}$ has a magnitude of unity, yields sinusoidally oscillating modes, while the existence of a complex piece in $\omega$ leads to exponentially growing or damping modes. In the case of exponential growth, the magnitude of $e^{i \omega t}$ will exceed unity.

We can perform a similar spectral analysis of the finite difference equation. Write the eigenmode for $u_{j}^{n}$ as

$$
u_{j}^{n}=\xi^{n} e^{i k(j \Delta x)}
$$

Here the quantity $\xi$ plays the role of $e^{i \omega \Delta t}$ and is called the amplification factor:

$$
u_{j}^{n}=\xi u_{j}^{n-1} = \xi^{2} u_{j}^{n-2} \ldots=\xi^{n} u_{j}^{0}
$$

For the scheme to be stable, the magnitude $\xi$ must be smaller or equal to unity for all k,

$$
|\xi(k)| \leq 1
$$

To perform a von Neumann stability anaylsis of the FTCS scheme

$$
\xi(k)=1-i \frac{v \Delta t}{\Delta x} \sin k \Delta x
$$

**the magnitude of $\xi$ is greater than unity for all k, indicating that this scheme is unstable.** In fact, we have $|\xi|>1$ independently of our choice for $\Delta x$ and $\Delta t$, which makes this scheme unconditionally unstable. That is bad. **The good news is that there are several ways of fixing this problem.**

For example, we could replace the term $u_{j}^{n}$ by the spatial average $\left(u_{j+1}^{n}+u_{j-1}^{n}\right) / 2$.

$$
u_{j}^{n+1}=\frac{1}{2}\left(u_{j+1}^{n}+u_{j-1}^{n}\right)-\frac{v}{2} \frac{\Delta t}{\Delta x}\left(u_{j+1}^{n}-u_{j-1}^{n}\right)
$$

a von Neumann analysis results in the amplification factor

$$
\xi=\cos k \Delta x-i \frac{v \Delta t}{\Delta x} \sin k \Delta x
$$

The von Neumann stability criterion then implies that we must have

$$
\frac { | v | \Delta t } { \Delta x } \leq 1
$$

The Courant condition states that the the grid point $u _ { j } ^ { n + 1 }$ at the new time level n+1 has to reside inside the domain of determinacy of the interval spanned by the finite difference stencil at the time level n. This makes intuitive sense: if $u _ { j } ^ { n + 1 }$ were outside this domain, its physical specification would require more information about the past than we are providing numerically, which may trigger an instability.

![](media/15512306641761.jpg)

Recalling that v represents the speed of a characteristic, we may interpret the Courant condition in terms of the domain of determinacy.

**It seems somewhat like a miracle that simply replacing a grid function by a local average manages to change the numerical scheme from unconditionally unstable to conditionally stable.**

This change can be interpreted in very physical terms

$$
u_{j}^{n+1}=u_{j}^{n}+\frac{1}{2}\left(u_{j+1}^{n}-2 u_{j}^{n}+u_{j-1}^{n}\right)-\frac{v}{2} \frac{\Delta t}{\Delta x}\left(u_{j+1}^{n}-u_{j-1}^{n}\right)
$$

or

$$
\frac{u_{j}^{n+1}-u_{j}^{n}}{\Delta t}=-v \frac{u_{j+1}^{n}-u_{j-1}^{n}}{2 \Delta x}+\frac{(\Delta x)^{2}}{2 \Delta t} \frac{u_{j+1}^{n}-2 u_{j}^{n}+u_{j-1}^{n}}{(\Delta x)^{2}}
$$

But equation is a finite-difference representation of the differential equation

$$
\partial_{t} u+v \partial_{x} u=D \partial_{x}^{2} u
$$

where the term on the right-hand side is essentially a diffusion term, with parameter $D=(\Delta x)^{2} /(2 \Delta t)$ serving as a constant coefficient of diffusion.

**This feature implies the amplitude of any wave will decrease spuriously with time as it propagates. A related effect is anomalous dispersion, an additional price we pay for stablity in the Lax scheme and many other finite-difference schemes for hyperbolic systems.**

#### implicit scheme

<!--Lax scheme

___

There are a number of other ways of constructing stable finite difference schemes for the model equation. A popular alternative to the Lax scheme is upwind differencing

$$
\frac{u_{j}^{n+1}-u_{j}^{n}}{\Delta t}=-v \left\{\begin{array}{l}{\frac{u_{j}^{n}-u_{j-1}^{n}}{\Delta x},} & {v>0} \\ {\frac{u_{j+1}^{n}-u_{j}^{n}}{\Delta x},} & {v>0}\end{array}\right.
$$

This scheme borrows its name from the fact that for a wave with $v>0$, that travels “to the right”, say, the new grid-point $u_{j}^{n+1}$ is affected only by the “upwind” grid-points to its left, i.e. that lie in the region through which the wave travels before reaching $x_{j}$.

three-level scheme

___

It would be desirable, however, to have a scheme that is second order in both in space and time. One way to construct such a code is to abandon two-level schemes, and instead consider a three-level scheme. We can then construct centered derivatives both for the time-derivative,

$$
\left(\partial_{t} u\right)_{j}^{n}=\frac{u_{j}^{n+1}-u_{j}^{n-1}}{2 \Delta t}+\mathcal{O}\left(\Delta t^{2}\right)
$$

the leap-frog scheme,

$$
u_{j}^{n+1}=u_{j}^{n-1}-v \frac{\Delta t}{\Delta x}\left(u_{j+1}^{n}-u_{j-1}^{n}\right)
$$

Some researchers prefer two-level schemes over three-level schemes because three level schemes require initial data on two different time levels, which can be somewhat awkward.

![-w619](media/15532637365521.jpg)

The leap-frog scheme has the additional disadvantage that, it only connects fields of the same color. “Black” gridpoints can therefore evolve completely independently of “white” gridpoints, and the two sets of grid points may drift apart as numerical error accumulates differently for the two sets of points. If necessary, this problem can be solved by artificially adding a very small viscous term that links the two sets together. Once these potential issues are resolved, leap-frog is a very simple, accurate and powerful method.
-->

Yet another way of constructing a stable two-level scheme is to use backward time differencing instead of forward differencing. This approach then yields the “backward-time, centered-space” scheme,

$$
u_{j}^{n+1}=u_{j}^{n}-\frac{v}{2} \frac{\Delta t}{\Delta x}\left(u_{j+1}^{n+1}-u_{j-1}^{n+1}\right)
$$

![-w600](media/15532642037929.jpg)

Performing a von Neumann stability analysis we find the amplification factor

$$
|\xi(k)| \leq 1
$$

for all values of $\Delta t$. This finding means that this scheme is unconditionally stable. **The size of the stepsize ∆t is no longer restricted by stability, and instead is limited only by accuracy requirements.** this property is even more important for parabolic equations.

The disadvantage of the backward differencing scheme is that we can no longer solve for the new grid function $u_{j}^{n+1}$ at the new time $t^{n+1}$ explicitly in terms of old grid functions at $t^{n}$ alone. Instead, now couples $u_{j}^{n+1}$ with the its closest neighbors $u_{j+1}^{n+1}$ and $u_{j-1}^{n+1}$. This coupling provides an implicit linear relation between the new grid functions, and is therefore an example of an implicit finite-differencing scheme. We can no longer sweep through the grid and update one point at a time; instead **we now have to solve for all grid points simultaneously**. Writing equation at all interior grid points, and then taking into account the boundary conditions, leads to a system of equations quite similar to elliptic equations.

<!--Crank-Nicholson scheme

___

A second-order scheme would be time-centered, meaning that we should estimate the the time derivative at the mid-point between the two time levels n and n + 1,

Implementing time-centering means that we also have to evaluate the space derivative at this same midpoint $n+1/2$  which we can do by averaging between the values at n and n+1.

$$
\left(\partial_{t} u\right)_{j}^{n+1 / 2}=\frac{u_{j}^{n+1}-u_{j}^{n}}{\Delta t}+\mathcal{O}\left(\Delta t^{2}\right)
$$

This approach yields the Crank-Nicholson scheme

$$
u_{j}^{n+1}=u_{j}^{n}-\frac{v}{4} \frac{\Delta t}{\Delta x}\left(\left(u_{j+1}^{n+1}-u_{j-1}^{n+1}\right)+\left(u_{j+1}^{n}-u_{j-1}^{n}\right)\right)
$$

![-w696](media/15532650020363.jpg)

Crank-Nicholson is second order in both space and time, and shows that it is unconditionally stable.

The iterative Crank-Nicholson scheme uses a predictor-corrector approach. In the predictor step we predict the new values $u_{j}^{n+1}$ by using the fully explicit FTCS scheme.

$$
^{(1)} u_{j}^{n+1}=u_{j}^{n}-\frac{v \Delta t}{2 \Delta x}\left(u_{j+1}^{n}-u_{j-1}^{n}\right)
$$

which, as we have seen above, would be unconditionally unstable by itself. In a subsequent corrector step we use these predicted values $^{(1)}{u_{j}}^{n+1}$ together with the $u_{j}^{n}$ to obtain a time-centered approximation for the spatial derivative on the right-hand side of equation. This step yields the corrected values of the grid function,

$$
^{(2)} u_{j}^{n+1}=u_{j}^{n} - \frac{v \Delta t}{4 \Delta x} \left(\left( ^{(1)} u_{j+1}^{n+1} - ^{(1)} u_{j-1}^{n+1}\right)\right)+\left(u_{j+1}^{n}-u_{j-1}^{n}\right) )
$$

The corrector step can be repeated an arbitrary number times N, always using the previous values $^{(N-1)} u_{j}^{n+1}$ on the right-hand side to find new corrected values $^{(N)} u_{j}^{n+1}$.

The iterative Crank-Nicholson scheme is an explicit two-level scheme that is second order in both space and time. Since this form is very similar to the 3 + 1 equations and related formulations, the iterative Crank-Nicholson scheme has often been used in numerical relativity simulations.
-->

#### Method of Lines

A popular alternative to these complete finite difference schemes is therefore the method of lines (or MOL for short).

**The basic idea of the method of lines is to finite difference the space derivatives only.** Now we introduce a spatial grid only, at least for now, so that the function values at these gridpoint, $u_{j}(t)=u\left(t, x_{j}\right)$, remain functions of time. As a result, our partial differential equation for $u(t, x)$ becomes a set of ordinary differential equations for the grid values $u_{j}(t)$. The next question is how to integrate the ordinary differential equations. The appealing feature of the method of lines, however, is that we can use any method for the integration of the ordinary differential equations that we like. In fact, many such methods, including very efficient, high-order methods, are precoded and readily available. One such algorithm is the ever-popular Runge-Kutta method.

To implement, say, a fourth-order scheme for our model

$$
\partial_{t} u+v \partial_{x} u=0
$$

we could adopt the fourth-order differencing stencil to replace the spatial derivative, yielding

$$
\frac{d u_{j}}{d t}=-\frac{v}{12 \Delta x}\left(u_{i-2}-8 u_{j-1}+8 u_{j+1}-u_{i+2}\right)
$$

and then integrate this set of ordinary differential equations with a fourth-order Runge-Kutta method.

##### Runge-Kutta (RK) method 

The explicit form of the algorithms is

1. Prediction step (common for both RK2 and RK3):

$$
\boldsymbol{U}^{(1)}=\boldsymbol{U}^{(n)}+\Delta t L\left(\boldsymbol{U}^{(n)}\right)
$$

2. Depending on the order do:

- RK2:

$$
\boldsymbol{U}^{n+1}=\frac{1}{\alpha}\left[\beta \boldsymbol{U}^{n}+\boldsymbol{U}^{(1)}+\Delta t L\left(\boldsymbol{U}^{(1)}\right)\right]
$$

- RK3:

$$
\begin{aligned} \boldsymbol{U}^{(2)} &=\frac{1}{\alpha}\left[\beta \boldsymbol{U}^{(2 n)}+\boldsymbol{U}^{(1)}+\Delta t L\left(\boldsymbol{U}^{(1)}\right)\right] \\ \boldsymbol{U}^{n+1} &=\frac{1}{\beta}\left[\beta \boldsymbol{U}^{(2 n)}+2 \boldsymbol{U}^{(2)}+2 \Delta t L\left(\boldsymbol{U}^{(2)}\right)\right] \end{aligned}
$$

## Ghost Zones

Cactus is based upon a distributed computing paradigm. That is, the problem domain is split into blocks, each of which is assigned to a processor.

Consider the 1-D wave equation
 
$$
\frac{\partial^{2} \phi}{\partial t^{2}}=\frac{\partial^{2} \phi}{\partial x^{2}}
$$

To solve this by partial differences, one discretises the derivatives to get an equation relating the solution
at different times.

$$
\phi(t+\Delta t, x)-2 \phi(t, x)+\phi(t-\Delta t, x)=\frac{\Delta t^{2}}{\Delta x^{2}}\{\phi(t, x+\Delta x)-2 \phi(t, x)+\phi(t, x-\Delta x)\}
$$

On examination, you can see that to generate the data at the point $(t+\Delta t, x)$ we need data from the four points $(t, x)$, $(t -\Delta t, x)$, $(t, x + \Delta x)$ and $(t, x- \Delta x)$ only.

Now, if you evolve the above scheme, it becomes apparent that at each iteration the number of grid points you can evolve decreases by one at each edge.

![-w816](media/15528964483464.jpg)

At the outer boundary of the physical domain, the data for the boundary point can be generated by the boundary conditions, however, at internal boundaries, the data has to be copied from the adjacent processor.

It would be inefficient to copy each point individually, so instead, a number of ghostzones are created at the internal boundaries. A ghostzone consists of a copy of the whole plane (in 3D, line in 2D, point in 1D) of the data from the adjacent processor.

![-w785](media/15528964642193.jpg)

Once the data has been evolved one step, the data in the ghostzones can be exchanged (or synchronised) between processors in one fell swoop before the next evolution step.

## Mesh Refinement

It is often the case in simulations of physical systems that **the most interesting phenomena may occur in only a subset of the computational domain.** In the other regions of the domain it may be possible to use a less accurate approximation, thereby reducing the computational resources required, and still obtain results which are essentially similar to those obtained if no such reduction is made.

!!! note
    Imagine, for concreteness, a simulation of a strong-field gravitational wave source, like a compact binary containing neutron stars or black holes. On the one hand we have to resolve these sources well, so as to minimize truncation error in the strong-field region. On the other hand, the grid must extend into the weak-field region at large distances from the sources, so as to minimize error from the outer boundaries and to enable us to extract the emitted gravitational radiation accurately.

![-w1062](media/15533057187758.jpg)

In particular, we may consider using a computational mesh which is non-uniform in space and time, using **a finer mesh resolution in the “interesting” regions** where we expect it to be necessary, and using **a coarser resolution in other areas**. ==This is what we mean by mesh refinement (MR).==

The mesh refinement driver that we use is called Carpet and is available together with the application framework Cactus. It uses the Berger–Oliger approach, where the computational domain as well as all refined subdomains consist of a set of rectangular grids. **Furthermore, there is a constant refinement ratio between refinement levels.** The basic idea underlying mesh refinement techniques is to perform the simulation not on one numerical grid, but on several, as in the multigrid methods.

![](media/15523870938244.jpg)

![](media/15551363353554.jpg)

!!! note "notation"
    The grids are grouped into refinement levels (or simply “levels”) $L^K$, each containing an arbitrary number of grids $G^{k}_{j}$. Each grid on refinement level k has the grid spacing (in one dimension) $\Delta x^{k}$. The grid spacings are related by the relation $\Delta x^{k}=\Delta x^{k-1} / N_{\text { refine }}$ with the integer refinement factor $N_{\text { refine }}$. The base level $L^0$ covers the entire domain (typically with a single grid) using a coarse grid spacing. The refined grids have to be properly nested. That is, any grid $G^{k}_{j}$ must be completely contained within the set of grids $L^{k-1}$ of the next coarser level, except possibly at the outer boundaries.

The times and places where refined grids are created and removed are decided by some refinement criterion. The simplest criterion, which is also indispensable for testing, is **manually specifying the locations of the refined grids at fixed locations in space at all times. This is called fixed mesh refinement.** A bit more involved is keeping the same refinement hierarchy, but **moving the finer grids according to some knowledge about the simulated system**, tracking some feature such as a black hole or a neutron star. This might be called “moving fixed mesh refinement”. Clearly the most desirable strategy is an automatic criterion that estimates the truncation error, and places the refined grids only when and where necessary. This is what is commonly understood by adaptive mesh refinement. Carpet supports all of the above in principle.

!!! note
    The situation is more complicated for objects that are moving, as is the case for a coalescing binary star system. In this case we do not know a priori the trajectories of the companion stars, hence do not know which regions need refining. Moreover, these regions will be changing as the system evolves and the stars move. Clearly, we would like to move the refined grids with the stars. Such an approach, whereby the grid is relocated during the simulation to give optimal resolution at each time step, is called adaptive mesh refinement or AMR.

### Time evolution scheme

In multigrid methods, the numerical solution is computed on a hierarchy of computational grids with increasing grid resolution. The finer grids may or may not cover all the physical space that is covered by the coarser grids. The numerical solution is then computed by completing sweeps through the grid hierarchy. 

![-w731](media/15533068726818.jpg)

#### Prolongation

The coarse grid is sufficiently small so that we can compute a solution with a direct solver. This provides the “global” features of the solution, albeit on a coarse grid and hence with a large local truncation error. We then interpolate this approximate solution to the next finer grid. This interpolation from a coarser grid to a finer grid is called a “prolongation”.

> In the mathematical field of numerical analysis, interpolation is a method of constructing new data points within the range of a discrete set of known data points.

AMR scheme evolves coarse grid data forward in time before evolving any data on the finer grids. These evolved coarse grid data can then be used to **provide boundary conditions for the evolution of data on the finer grids via prolongation**, i.e. interpolation in time and space.

![](media/15551377083235.jpg)

A refinement by a factor of $N_{\text { refine }}$ requires time step sizes that are smaller by a factor $N_{\text { refine }}$, and hence $N_{\text { refine }}$ time steps on level $k+1$ are necessary for each time step on level k.

#### Restriction

At time steps in which the coarse and fine grids are both defined, the fine grid data are restricted onto the coarse grid (via a simple copy operation) after it has been evolved forward in time.

![](media/15551378176639.jpg)

The coarser grids now “learn” from the finer grids by comparing their last solution with the one that comes back from a finer grid. This comparison provides an estimate for the local truncation error. These sweeps through the grid hierarchy can be repeated until the solution has converged to a pre-determined accuracy.

If there are more than two grid levels, then one proceeds recursively from coarsest to finest, evolving data on the coarsest grid first, interpolating this data in time and space along boundaries of finer grids, evolving the finer grid data, and restricting evolved data from finer to coarser grids whenever possible.

!!! note
    For time evolution schemes that consist only of a single iteration (or step), the fine grid boundary condition needs to be applied only once. Most higher-order time integrations schemes, such as Runge-Kutta or iterative Crank-Nicholson, are actually multi-step schemes and correspondingly require **the fine grid boundary condition to be applied multiple times**. If this is not done in a consistent manner at each iteration, then the coarse and the fine grid time evolution will not couple correctly, and this can introduce a significant error.
    
    There are several ways to guarantee consistent boundary conditions on fine grids. Our method use a larger fine grid boundary. That is, each of the integration substeps is formally applied to a progressively smaller domain, and the prolongation operation re-enlarges the domain back to its original size.
    
    ![](media/15551389503538.jpg)

    Note that this “buffering” is done only for prolongation boundaries; outer boundaries are handled in the conventional way. Note also that the use of buffer zones is potentially more computationally efficient. We emphasise that the use of these buffer zones is not always necessary. To our knowledge the buffer zones are necessary only when the system of equations contains second spatial derivatives, and a multi-step numerical method is used for time integration.

### Inter-grid transport operators

As described above, the interaction between the individual refinement levels happens via prolongation and restriction. For prolongation, Carpet currently supports polynomial interpolation, up to quadratic interpolation in time, which requires keeping at least two previous time levels of data. It also supports up to quintic interpolation in space, which requires using at least three ghost zones. We usually use cubic interpolation in space, which requires only two ghost zones. For restricting, Carpet currently uses sampling (i.e., a simple copy operation). These transport operators are not conservative. Since our formulation of Einstein’s equation is not in a conservative form, any use of conservative inter-grid operations offers no benefit. However, the transport operators can easily be changed.

### Initial data generation

Initial data generation and time evolution are controlled by the driver Carpet. Initial data are created recursively, starting on the coarsest level $L^{0}$. 

In many cases, the initial data specification is only valid for a single time $t=0$. However, for the time interpolation necessary during prolongation, it may be necessary to have data on several time levels. One solution is to use only lower order interpolation during the first few time steps. We decided instead, we offer the option to evolve coarse grid data backwards in time in order to provide sufficient time levels for higher order interpolation in time at fine grid boundaries. 

This initial data generation proceeds in two stages.

First the data are evolved both forwards and backwards in time one step, leading to the “hourglass” structure.

![](media/15551393149475.jpg)

This evolution proceeds recursively from coarsest to finest, so that all data necessary for time interpolation are present. Note that this would not be the case if we evolved two steps backwards in time, as there would not be enough data for the time interpolation for the restriction operation between these two steps.

## Pseudo-spectral methods

In spectral methods the evolved fields are expressed in terms of a finite sum of basis functions. An example of this would be to describe a field on a sphere in terms of an expansion in spherical harmonics. These methods have the advantage that if the fields are smooth then **the error in truncating the expansion converges to zero exponentially with the number of basis functions used in the expansion.**