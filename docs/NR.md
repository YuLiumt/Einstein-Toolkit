# Numerical Relativity

For all but the simplest systems, **analytic solutions for the evolution of such systems do not exist**. Hence the task of **solving Einstein's equations must be performed numerically on a computer**.
In classical dynamics, the evolution of a system is uniquely determined by the initial positions and velocities of its constituents. By analogy, the evolution of general relativistic gravitational field is determined by specifying **the metric quantities $g_{ab}$ and $\partial_t g_{ab}$ at a given (initial) instant of time t**. Now these metric quantities can be integrated forward in time provided we can obtain from **the Einstein field equations expressions for $\partial^2_t g_{ab}$ at all points on the hypersurface**. That way we can integrate these expressions to compute $g_{ab}$ and $\partial_t g_{ab}$ on a new spacelike hypersurface at some new time $t + \delta t$, and then, by repeating the process, obtain  $g_{ab}$ for all other points $x^0$ and $x^i$ in the (future) spacetime.

<!-- more -->

## Complications

The equations that arise in **numerical relativity are typically multidimensional, nonlinear, coupled partial differential equations in space and time**. They have in common with other areas of computational physics, like magnetohydrodynamics. However, solving Einstein’s equations poses some additional complications that are unique to general relativity. In general relativity, coordinates are merely labels that distinguish points in spacetime; by themselves coordinate intervals have no physical significance. **To use coordinate intervals to determine physically measurable proper distances and proper times requires the spacetime metric, but the metric is known only after Einstein’s equations have been solved**. Moreover, as the numerical integrations that determine the metric proceed, it often turns out that the original, arbitrary choice of coordinates turns out to be bad, because, for example, singularities appear in the equations. Encountering such **singularities**, be they physical or coordinate, results in some of the terms in Einstein’s equations becoming infinite, **potentially causing overflows in the computer output and premature termination of the numerical integration**.

Treating black holes is one of the main goals of numerical relativity, but this poses another complication. The reason is that **black holes contain physical spacetime singularities** – regions where the gravitational tidal field, the matter density and the spacetime curvature all become infinite. Thus, **when dealing with black holes, it is crucial to choose a computational technique that avoids encountering their interior spacetime singularities in the course of the simulation**.

Another complication arises in the context of one of the most pressing goals of numerical relativity – the calculation of waveforms from promising astrophysical sources of gravitational radiation. These theoretical templates are essential for the identification and physical interpretation of gravitational wave sources. However, the gravitational wave components of the spacetime metric usually constitute small fractions of the smooth background metric. Moreover, to extract the waves from the background in a simulation requires that one probe the numerical spacetime in the far-field, or radiation, zone, which is typically at large distance from the strong-field central source. Yet it is the strong-field region which usually consumes most the computational resources (e.g. spatial resolution) to guarantee accuracy. Furthermore, waiting for the wave to propagate to the far-field region usually takes nonnegligible integration time.

> **Oppenheimer-Snyder**
> 
> The Oppenheimer-Snyder, or OS, solution illustrates many generic features of gravitational collapse and black hole formation. Since the solution is analytic, it is simple to work with and is often used to test and calibrate numerical codes designed to deal with more complicated cases.

## 3+1 Decomposition

> **Cauchy problem**
> 
> This is a fundamental problem arising in the mathematical theory of partial differential equations.
> 
> The Bianchi identities $\nabla_b G^{ab} = 0$ give
> 
> $$
> \partial_t G^{a0} = - \partial_i G^{ai} - G^{bc} \Gamma^a_{bc} - G^{ab} \Gamma^c_{bc}
> $$
> 
> Since no term on the right hand side of equation contains third time derivatives or higher, the four quantities $G^{a0}$ cannot contain second time derivatives. Hence the four equations
> 
> $$
> G^{a0} = 8 \pi T^{a0}
> $$
> 
> do not furnish any of the information required for the dynamical evolution of the fields. Rather, they supply four constraints on the initial data, i.e. four relations between $g_{ab}$ and $\partial_t g_{ab}$ on the initial hypersurface at $x^0 = t$. The only truly dynamical equations must be provided by the six remaining relations
> 
> $$
> G^{ij} = 8 \pi T^{ij}
> $$
> 
> It is not surprising that there is a mismatch between the required number (10) of second time 2 derivatives $\partial_t^2 g_{ab}$ and the available number (6) of dynamical ﬁeld equations. After all, there is always a fourfold ambiguity associated with the freedom to choose four different coordinates to label points in spacetime. So, for example, we could always choose Gaussian normal coordinates and set $g_{00} = −1 $ and $g_{0i} = 0$.
> The Cauchy problem in general relativity logically involves a **decomposition of four-dimensional spacetime into three dimensional space and one-dimensional time**.
> 
> **Cauchy surface**
> 
> Cauchy surface is a plane in space-time which is like an instant of time; its significance is that giving the initial conditions on this plane determines the future (and the past) uniquely.

![-w846](media/15421762095016/15457867415457.jpg)

The 3+1 equations are entirely equivalent to the usual ﬁeld equations but they focus on the evolution of 12 purely spatial quantities closely related to $g_{ij}$ and $\partial_t g_{ij}$ and the constraints that they must satisfy on spatial hypersurfaces. Once these spatial ﬁeld quantities are specified on some initial “time slice” (i.e. spatial hypersurface) consistent with the 3 + 1 constraint equations, the 3 + 1 evolution equations can then be integrated, together with evolution equations for the matter sources, to determine these ﬁeld quantities at all later times.

> The 3+1 formalism
> 
> - 4 constraint equations that contain no time derivatives but provide relations between the spatial ﬁeld quantities and their matter sources that must be satisﬁed on any time slice.
> - A convenient set of 12 coupled, first-order, time-evolution equations for the spatial ﬁeld variables in terms of ﬁeld and source quantities residing on the slice.
> 
> Analogy Maxwell’s equations
> 
> $$
> \begin{array} { l } { D _ { i } E ^ { i } - 4 \pi \rho = 0 } \\ D _ { i } B ^ { i } = 0  \end{array}
> $$
> 
> The above equations involve only spatial derivatives of the electric and magnetic fields and hold at each instant of time independently of the prior or subsequent evolution of the fields. They therefore constrain any possible configurations of the fields, and are correspondingly called the constraint equations.
> 
>$$
> \begin{aligned} \partial _ { t } E _ { i } & = \epsilon _ { i j k } D ^ { j } B ^ { k } - 4 \pi j _ { i } \\ \partial _ { t } B _ { i } & = - \epsilon _ { i j k } D ^ { j } E ^ { k } \end{aligned}
> $$
> 
> These equations describe how the fields evolve forward in time, and are therefore called the evolution equations.
> 
> To completely determine the time evolution of the electromagnetic fields we also have to specify how the sources ρ and $j^i$ evolve according to the net force acting on them.
> 
> It is possible to bring Maxwell’s equations into a form that is closer to the 3+1 form of Einstein’s equations. To do so, we introduce the vector potential $A ^ { a } = \left( \Phi , A ^ { i } \right)$ and write $B^i$ as
> 
> $$
> B _ { i } = \epsilon _ { i j k } D ^ { j } A ^ { k }
> $$
> 
> By construction, $B_i$ automatically satisfies the constraint $D _ { i } B ^ { i } = 0$.
> 
> The two evolution equations can be rewritten in terms of $E_i$ and $A_i$
> 
> $$
> \begin{aligned} \partial _ { t } A _ { i } & = - E _ { i } - D _ { i } \Phi \\ \partial _ { t } E _ { i } & = D _ { i } D ^ { j } A _ { j } - D ^ { j } D _ { j } A _ { i } - 4 \pi j _ { i } \end{aligned}
> $$
> 
> With the vector potential $A_i$ we have introduced a gauge freedom into electrodynamics which is expressed in the freely specifiable gauge variable $Φ$.
> 
> The initial value problem in electrodynamics can now be solved in two steps. In the first step, initial data ($A_i$, $E_i$), together with the sources (ρ, $j_i$), are specified that satisfy the constraint equations. In the second step, these fields are evolved according to the evolution equations. **Before the evolution equations can be solved, a suitable gauge condition has to be chosen**.

The standard 3 + 1 equations are sometimes referred to as the “ADM equations”. Einstein’s equations can be split into a set of constraint and evolution equations

1. To specify gravitational fields $(\gamma_{ij}, K_{ij})$ on some **initial spatial slice Σ**. These fields can then be used as “starting values”. 
    
    > the spatial metric $\gamma_{ij}$, the extrinsic curvature $K_{ij}$ and any matter fields have to satisfy the the constraint equations.
    >
    > - Contracting Gauss’ equation
    >
    > 	$$
    > 	R + K^2 - K_{ij}K^{ij} = 16 \pi \rho
    > 	$$
    >
    > - Contracting the Codazzi equation
    >
    > 	$$
    > 	D_j (K^{ij} - \gamma^{ij} K) = 8 \pi S^i
    > 	$$

The constraint equations contain no time derivatives and relate ﬁeld quantities on a given t = constant spacelike hypersurface. The evolution equations contain first-order time derivatives that tell us how the ﬁeld quantities change from one hypersurface to the next.

If the ﬁeld data $(\gamma_{ij}, K_{ij})$ satisfy the constraints at some time t and are evolved with the evolution equations, then the data will also satisfy the constraint equations at all later times.

The goal of achieving numerically **stable computer solutions**, especially when **the absence of spatial symmetries** requires us to work in all three spatial dimensions, has led to alternative formulations and to crucial **modiﬁcations of the standard 3 + 1 equations**.

### Foliations of Spacetime

We assume that the spacetime $(M, g_{ab})$ can be foliated into a family of non-intersecting spacelike three-surfaces Σ, which arise, at least locally, as the level surfaces of a scalar function t that can be interpreted as a global time function. From t we can define the 1-form

$$
\Omega _ { a } = \nabla _ { a } t
$$

which is closed by construction,

$$
\nabla _ { [ a } \Omega _ { b ] } = \nabla _ { [ a } \nabla _ { b ] } t = 0
$$

The 4-metric $g_{ab}$ allows us to compute the norm of $\tilde{Ω}$􏰞, which we call $- \alpha ^ { - 2 }$

$$
\| \Omega \| ^ { 2 } = g ^ { a b } \nabla _ { a } t \nabla _ { b } t \equiv - \frac { 1 } { \alpha ^ { 2 } }
$$

$α$ measures how much proper time elapses between neighboring time slices along the normal vector $Ω^a$ to the slice, and is therefore called the lapse function. We assume that $α > 0$, so that $Ω^a$ is timelike and the hypersurface Σ is spacelike everywhere.

We can now define the unit normal to the slices as

$$
n ^ { a } \equiv - g ^ { a b } \alpha \Omega _ { b }
$$

Here the negative sign has been chosen so that $n^a$ points in the direction of increasing t, and may therefore be thought of as the four-velocity of a “normal” observer whose worldline is always normal to the spatial slices Σ.

#### The spatial metric $\gamma_{ab}$

With the normal vector we can construct the spatial metric $\gamma_{ab}$ that is induced by $g_{ab}$ on the three-dimensional hypersurfaces Σ

$$
\gamma_{ab} = g_{ab} + n_a n_b
$$

Thus $\gamma_{ab}$ is a projection tensor that projects out all geometric objects lying along $n^a$. This metric allows us to compute distances within a slice Σ. To see that $γ_{ab}$ is purely spatial, i.e., resides entirely in Σ with no piece along $n^a$, we contract it with the normal $n^a$,

$$
n ^ { a } \gamma _ { a b } = n ^ { a } g _ { a b } + n ^ { a } n _ { a } n _ { b } = n _ { b } - n _ { b } = 0
$$



> We break up 4-dimensional tensors by decomposing them into a purely spatial part, which lies in the hypersurfaces $\Sigma$, and a timelike part, which is normal to the spatial surface. To do so, we need two projection operators.
> 
> The first one, which projects a 4-dimensional tensor into a spatial slice
> 
> $$
> \gamma^a_{\space b} = g^a_{\space b} + n^a n_b
> $$
> 
> Similarly, we may define the normal projection operator as
> 
> $$
> N^a_{\space b} = - n^a n_b
> $$
> 
> We can now use these two projection operators to decompose any tensor into its spatial and timelike parts.
> 
> The three-dimensional metric only contains **information about the curvature intrinsic to a slice Σ**, but it gives no information about what shape this slice takes in the spacetime M in which it is embedded. This information is contained in a tensor called **extrinsic curvature**.

The three-dimensional covariant derivative can be expressed in terms of three-dimensional connection coefficients, which, in a coordinate basis, are given by

$$
\Gamma _ { b c } ^ { a } = \frac { 1 } { 2 } \gamma ^ { a d } \left( \partial _ { c } \gamma _ { d b } + \partial _ { b } \gamma _ { d c } - \partial _ { d } \gamma _ { b c } \right)
$$

The three-dimensional Riemann tensor can be computed from

$$
R _ { a b c } ^ { d } = \partial _ { b } \Gamma _ { a c } ^ { d } - \partial _ { a } \Gamma _ { b c } ^ { d } + \Gamma _ { a c } ^ { e } \Gamma _ { e b } ^ { d } - \Gamma _ { b c } ^ { e } \Gamma _ { e a } ^ { d }
$$

The three-dimensional curvature $R _ { b c d } ^ { a }$ only contains information about the curvature intrinsic to a slice Σ, but it gives no information about what shape this slice takes in the spacetime M in which it is embedded.

#### The Extrinsic Curvature $K_{ab}$

![extrinsic curvature](https://ws4.sinaimg.cn/large/006tNbRwgy1fwgtsk6zubj317o0np7d2.jpg) 

**The extrinsic curvature** $K_{ab}$ can be found by **projecting gradients of the normal vector into the slice Σ**. The metric and the extrinsic curvature $(\gamma_{ab}, K_{ab})$ can therefore be considered as the equivalent of positions and velocities in classical mechanics – they measure the “instantaneous” state of the gravitational ﬁeld, and form the fundamental variables in our initial value formulation.
We now define the extrinsic curvature, $K_{ab}$, as the negative expansion

$$
K_{ab} = - \gamma_a^{\space c} \gamma_b^{\space d} \nabla_{(c} n_{d)} = - \gamma_a^{\space c} \gamma_b^{\space d} \nabla_c n_d
$$

By definition, the extrinsic curvature is symmetric and purely spatial. they can only differ in the direction in which they are pointing, and the extrinsic curvature therefore provides information on **how much this direction changes from point to point across a spatial hypersurface.** As a consequence, the extrinsic curvature measures the rate at which the hypersurface deforms as it is carried forward along a normal.

Finally, we can write the extrinsic curvature as

$$
K_{ab} = - \frac{1}{2} \mathcal{L}_n \gamma_{ab}
$$

where $L_n$ denotes the Lie derivative along $n^a$.

Since $n^a$ is a timelike vector, equation illustrates the intuitive interpretation of the extrinsic curvature as a geometric generalization of the “time derivative” of the spatial metric $\gamma_{ab}$.

> Proof: $\gamma_{ab}$ changes proportionally to $K_{ab}$
>
> $$
> \begin{align}
> K_{ab} &= - \gamma_a^{\space c} \gamma_b^{\space d} \nabla_c n_d = - (\delta_a^{\space c} + n_a n^c) (\delta_b^{\space d} + n_b n^d) \nabla_c n_d  \\
>        &= -(\delta_a^{\space c} + n_a n^c) \delta_b^{\space d} \nabla_c n_d = - \nabla_a n_b - n_a a_b \\
> \mathcal{L}_n \gamma_{ab} &= \mathcal{L}_n (g_{ab} + n_a n_b) = 2 \nabla_{(a} n_{b)} + n_a \mathcal{L}_n n_b + n_b \mathcal{L}_n n_a \\
>                           &= 2 (\nabla_{(a} n_{b)} - n_{(a} a_{b)})  \\
> \mathcal{L}_n \gamma_{ab} &= - 2 K_{ab}
> \end{align}
> $$

#### Riemann Tensor

The metric $\gamma_{ab}$ and the extrinsic curvature $K_{ab}$ cannot be chosen arbitrarily. Instead, they have to satisfy **certain constraints**.

In order to find these relations, we have to relate the three-dimensional Riemann tensor $R^a_{\space bcd}$ of the the hypersurfaces Σ to the four-dimensional Riemann tensor $^{(4)} R^a_{\space bcd}$ of M. To do so, we first take a completely spatial projection of $^{(4)} R^a_{\space bcd}$, then a projection with one index projected in the normal direction, and finally a projection with two indices projected in the normal direction. All other projections vanish identically because of the symmetries of the Riemann tensor. A decomposition of $^{(4)} R^a_{\space bcd}$ into spatial and normal pieces therefore involves these **three different types of projections**.

$$
\begin{align}
^{(4)} R_{abcd} &= \gamma_a^{\space p} \gamma_b^{\space q} \gamma_c^{\space r} \gamma_d^{\space s} {}^{(4)} R_{pqrs} - 2 \gamma_a^{\space p} \gamma_b^{\space q} \gamma_{[c}^{\space r} n_{d]} n^s {}^{(4)} R_{pqrs} \\
&- 2 \gamma_c^{\space p} \gamma_d^{\space q} \gamma_{[a}^{\space r} n_{b]} n^s {}^{(4)} R_{pqrs} + 2 \gamma_a^{\space p} \gamma_{[c}^{\space r} n_{d]} n_b n^q n^s {}^{(4)} R_{pqrs} \\
&- 2 \gamma_b^{\space p} \gamma_{[c}^{\space r} n_{d]} n_a n^q n^s {}^{(4)} R_{pqrs} 
\end{align}
$$

The above projections give rise to the equations of Gauss, Codazzi and Ricci. **Gauss’ equation and Codazzi equations give rise to the “constraint” equations.**

##### Gauss’ equation

a completely spatial projection.

$$
R_{abcd} + K_{ac} K_{bd} -K_{ad} K_{cb} = \gamma^P_{\space a} \gamma^q_{\space b} \gamma^r_{\space c} \gamma^s_{\space d} {}^{(4)} R_{pqrs}
$$

##### Codazzi equation

$R^a_{\space bcd}$ with one index projected in the normal direction.

$$
D_b K_{ac} - D_a K_{bc} = \gamma^P_{\space a} \gamma^q_{\space b} \gamma^r_{\space c} n^s_{\space d} {}^{(4)} R_{pqrs}
$$

### Constraint equations

We can rewrite Einstein’s ﬁeld equations in a 3+1 form. Basically, we just need to take the equations of Gauss, Codazzi and Ricci and eliminate the four-dimensional Rieman tensor using Einstein’s equations.

$$
G_{ab} = {}^{(4)} R_{ab} - \frac{1}{2} {}^{(4)} R g_{ab} = 8 \pi T_{ab}
$$

We will first derive the constraint equations from Gauss’ equation and the Codazzi equation.

- Contracting Gauss’ equation

	$$
	R + K^2 - K_{ab}K^{ab} = 16 \pi \rho
	$$ 

	> We now define the energy density ρ to be the total energy density as measured by a normal observer $n^a$,
	>
	> $$
	> \rho = n_a n_b T^{ab} \\
	> 2 n^p n^r G_{pr} = R + K^2 - K_{ab} K^{ab}
	> $$

- Contracting the Codazzi equation
	$$
	D_b K^b_{\space a} - D_a K = 8 \pi S_a
	$$

	> We now define $S_a$ to be the momentum density as measured by a normal observer $n^a$,
	>
	> $$
	> S_a = - \gamma^b_{\space a} n^c T_{bc} \\
	> D_b K^b_{\space a} - D_a K = - \gamma^q_{\space a} n^s G_{qs}
	> $$

They are **the conditions** that allow a three-dimensional slice Σ with data $(\gamma_{ab}, K_{ab})$ to be embedded in a four-dimensional manifold M with data $g_{ab}$. We will discuss strategies for **solving the constraint equations and finding initial data** that represent a snapshot of the gravitational fields at a certain instant of time.

> The four constraint equations cannot determine all of the gravitational fields $(\gamma_{ij}, K_{ij})$.
> 
> Both $\gamma_{ij}$ and $K_{ij}$ are symmetric, three-dimensional tensors, they together have **twelve independent components**.
> 
> - The **four constraint equations** can only determine four of these
> - Four undetermined functions are related to **coordinate choices**
> - Two independent sets of values for **the conjugate pair** $(\gamma_{ij}, K_{ij})$.
> - Two dynamical degrees of freedom correspond to **the two polarization modes of a gravitational wave**
> 
> It is quite intuitive that the state of a dynamical field, like a **gravitational wave, cannot be determined from constraint equations**. Waves satisfy hyperbolic equations, and their state at any time depends on their past history. It is therefore natural that the constraint equations serve to constrain only the “longitudinal” parts of the fields, while the “transverse” parts, related to the dynamical degrees of freedom, remain freely specifiable.
> 
> Ideally one would like to separate unambiguously the longitudinal from the transverse parts of the fields at some initial time, freely specifying the latter and then solving the constraints for the former. Given the nonlinear nature of general relativity such a rigorous separation is not possible; instead, all these fields are entangled in the spatial metric and the extrinsic curvature.

Constraint equations constrain the fields in space at one instant of time, independently of their past history.

> Analogy Electric Field
> 
> Maxwell’s equations also split into constraint and evolution equations. The constraint equations have to be satisﬁed by any electric and magnetic ﬁeld at each instant of time, but they are not sufficient to completely determine these fields. Consider the equation for the electric field  $\vec{E}$,
> 
> $$
> \nabla \cdot \vec{E} = 4 \pi \rho
> $$
> 
> Given an electrical charge density ρ, we can solve this equation for one of the components of $E^i$ , but not all three of them. For example, we could make certain choices for $E^x$ and $E^y$, and then solve for $E^z$, even though we might be **troubled by the asymmetry** in singling out one particular component in this approach.
> 
> Alternatively, we may prefer to write $E^i$ as some “background” field $\bar { E } ^ { i }$ times some overall scaling factor, say $ψ^4$
> 
> $$
> E ^ { i } = \psi ^ { 4 } \bar { E } ^ { i }
> $$
> 
> **make certain choices for all three components of the background field** $\bar { E } ^ { i }$, and then solve for the scaling factor $ψ^4$. Though it might not be so useful for treating Maxwell’s equations, such an approach leads to a very convenient and tractable system for Einstein’s equations.

#### Conformal Transformations

##### Conformal Transformation of the Spatial Metric

We begin by writing the spatial metric $\gamma_{ij}$ as a product of some power of a positive scaling factor ψ and a background metric $\bar{\gamma}_{ij}$,

$$
\gamma_{ij} = ψ^4 \bar{\gamma}_{ij}
$$

This identification is a **conformal transformation** of the spatial metric. We call ψ **the conformal factor**, and $\bar{\gamma}_{ij}$ **the conformally related metric**. Taking ψ to the fourth power turns out to be convenient, but is otherwise arbitrary. Loosely speaking, **the conformal factor absorbs the overall scale of the metric, which leaves five degrees of freedom in the conformally related metric**.

Superficially, **the conformal transformation is just a mathematical trick**, namely, rewriting one unknown as a product of two unknowns in order to make solving some equations easier.

The inverse of $\bar{\gamma}_{ij}$:

$$
\gamma ^ { i j } = \psi ^ { - 4 } \bar { \gamma } ^ { i j }
$$

In three dimensions, the connection coefficients must transform according to

$$
\Gamma _ { j k } ^ { i } = \bar { \Gamma } _ { j k } ^ { i } + 2 \left( \delta _ { j } ^ { i } \bar { D } _ { k } \ln \psi + \delta _ { k } ^ { i } \bar { D } _ { j } \ln \psi - \bar { \gamma } _ { j k } \bar { \gamma } ^ { i l } \bar { D } _ { l } \ln \psi \right)
$$

For the scalar curvature

$$
R = \psi ^ { - 4 } \bar { R } - 8 \psi ^ { - 5 } \bar { D } ^ { 2 } \psi
$$

Inserting the scalar curvature into the Hamiltonian constraint yields

$$
8 \bar { D } ^ { 2 } \psi - \psi \bar { R } - \psi ^ { 5 } K ^ { 2 } + \psi ^ { 5 } K _ { i j } K ^ { i j } = - 16 \pi \psi ^ { 5 } \rho
$$

which, for a given choice of the conformally related metric $\bar { \gamma } ^ { i j }$, we may interpret as an equation for the conformal factor ψ. The extrinsic curvature $K_{ij}$ has to satisfy the momentum constraint, and it will be useful to rescale $K_{ij}$ conformally as well.

##### Conformal transformation of the extrinsic curvature

We have conformally transformed the spatial metric, but before we proceed we also have to decompose the extrinsic curvature. It is convenient to split $K_{ij}$ into its trace K and a traceless part $A_{ij}$ according to

$$
K_{ij} = A_{ij} + \frac{1}{3} \gamma_{ij} K
$$

and to conformally transform K and $A_{ij}$ separately. A priori it is not clear how to transform K and $A_{ij}$, and our only guidance for inventing rules is that the transformation should bring the constraint equations into a simple and solvable form. Consider the transformations

$$
\begin{aligned} A ^ { i j } & = \psi ^ { \alpha } \bar { A } ^ { i j } \\ K & = \psi ^ { \beta } \bar { K } \end{aligned}
$$

where α and β are two so far undetermined exponents.

> Inserting the above expressions into the momentum constraint (the choice $α = −10$) yields
>
> $$
> \psi ^ { - 10 } \bar { D } _ { j } \bar { A } ^ { i j } - \frac { 2 } { 3 } \psi ^ { \beta - 4 } \bar { \gamma } ^ { i j } \bar { D } _ { j } \bar { K } - \frac { 2 } { 3 } \beta \psi ^ { \beta - 5 } \bar { K } \bar { \gamma } ^ { i j } \bar { D } _ { j } \psi = 8 \pi S ^ { i }
> $$
> 
> Our desire to simplify equations motivates the choice $β = 0$, so that we treat K as a conformal invariant, $K = \bar{K}$. With these choices, 
> 
> the Hamiltonian constraint now becomes
> 
> $$
> 8 \bar { D } ^ { 2 } \psi - \psi \bar { R } - \frac { 2 } { 3 } \psi ^ { 5 } K ^ { 2 } + \psi ^ { - 7 } \bar { A } _ { i j } \bar { A } ^ { i j } = - 16 \pi \psi ^ { 5 } \rho
> $$
> 
> and the momentum constraint is
> 
> $$
> \bar { D } _ { j } \bar { A } ^ { i j } - \frac { 2 } { 3 } \psi ^ { 6 } \bar { \gamma } ^ { i j } \bar { D } _ { j } K = 8 \pi \psi ^ { 10 } S ^ { i }
> $$

**In addition to the spatial metric and extrinsic curvature, it may also be necessary to transform the matter sources ρ and $S^i$ to insure uniqueness of solutions.**

> We start by considering the linear equation
> 
> $$
> \nabla ^ { 2 } u = f u
> $$
> 
> on some domain Ω. Here f is some given function, and we will assume $u = 0$ on the boundary $∂Ω$. If f is non-negative everywhere, we can apply the maximum principle to show that u = 0 everywhere. The point is that if u were non-zero somewhere in Ω, say positive, then it must have a maximum somewhere. At the maximum the left hand side of (3.40) must be negative, but the right hand side is non-negative if $f ≥ 0$, which is a contradiction. Clearly, the argument works the same way if u is negative somewhere, implying that $u = 0$ everywhere if $f ≥ 0$.
> 
> Now consider the non-linear equation
> 
> $$
> \nabla ^ { 2 } u = f u ^ { n }
> $$
> 
> and assume there exist two positive solutions $u_1$ and $u_2 ≥ u_1$ that are identical, $u_1 = u_2$, on the boundary $∂Ω$. The difference $∆u = u_2 − u_1$ must then satisfy an equation
> 
> $$
> \nabla ^ { 2 } \Delta u = n f \tilde { u } ^ { n - 1 } \Delta u
> $$
> 
> where $\tilde { u }$ is some positive function satisfying $u_1 ≤ \tilde{u} ≤ u_2$. Applying the above argument to $∆u$, we see that the maximum principle implies $∆u = 0$ and hence uniqueness of solutions if and only if $nf ≥ 0$, i.e. if the coefficient and exponent in the source term have the same sign.

Inspecting the Hamiltonian constraint we see that the matter term $−16 π ψ^5 ρ$ features the “wrong signs”: it has a negative coefficient (assuming a positive matter density ρ), but a positive exponent for ψ. Therefore the maximum principle cannot be applied, and the uniqueness of solutions cannot be established.

Uniqueness of solutions can be restored, however, by introducing a conformal rescaling of the density. With $\rho = \psi ^ { \delta } \bar { \rho }$, where $δ ≤ −5$ and where $\bar{ρ}$ is now considered a given function, the matter term carries the “right signs”, and the maximum principle can be applied to establish the uniqueness of solutions.

###### Conformal Transverse-Traceless Decomposition

Any symmetric, traceless tensor can be split into a transverse-traceless part that is divergenceless and a longitudinal part that can be written as a symmetric, traceless gradient of a vector. We can therefore decompose $\bar{A}^{ij}$ as

$$
\bar { A } ^ { i j } = \bar { A } _ { T T } ^ { i j } + \bar { A } _ { L } ^ { i j }
$$

where the transverse part is divergenceless

$$
\bar { D } _ { j } \bar { A } _ { T T } ^ { i j } = 0
$$

and where the longitudinal part satisfies

$$
\bar { A } _ { L } ^ { i j } = \bar { D } ^ { i } W ^ { j } + \bar { D } ^ { j } W ^ { i } - \frac { 2 } { 3 } \bar { \gamma } ^ { i j } \bar { D } _ { k } W ^ { k } \equiv ( \bar { L } W ) ^ { i j }
$$

Here $W^i$ is a vector potential, and it is easy to see that the longitudinal operator or vector gradient $\bar{L}$ produces a symmetric, traceless tensor. We can now write the divergence of $\bar{A}^{ij}$ as

$$
\bar { D } _ { j } A ^ { i j } = \bar { D } _ { j } A _ { L } ^ { i j } = \bar { D } _ { j } ( \bar { L } W ) ^ { i j } = \bar { D } ^ { 2 } W ^ { i } + \frac { 1 } { 3 } \bar { D } ^ { i } \left( \bar { D } _ { j } W ^ { j } \right) + \bar { R } _ { j } ^ { i } W ^ { j } \equiv \left( \bar { \Delta } _ { L } W \right) ^ { i }
$$

where $\bar{∆}_L$ is the vector Laplacian.

Note that $\bar{A}^{ij}_{TT}$ and $\bar{A}^{ij}_{L}$ are transverse and longitudinal with respect to the conformal metric $\bar{γ}_{ij}$, which is why this decomposition is called the conformal transverse-traceless decomposition. Alternatively one can also adopt a physical transverse-traceless decomposition, where the corresponding tensors are transverse and longitudinal with respect to the physical metric $γ_{ij}$.

![-w1135](media/15421762095016/15460053036766.jpg)

We started out with six independent variables in both the spatial metric $\gamma_{ij}$ and the extrinsic curvature $K_{ij}$. Splitting oﬀ the conformal factor ψ left five degrees of freedom in the conformally related metric $\bar{\gamma}_{ij}$ (once we have specified its determinant $\bar{\gamma}$). Of the six independent variables in $K_{ij}$ we moved one into its trace K, two into $\bar{A}^{ij}_{TT}$ (which is symmetric, traceless, and divergenceless), and three into $\bar{A}^{ij}_L$ (which is reflected in its representation by a vector). Of the twelve original degrees of freedom, the constraint equations determine only four, namely the conformal factor ψ (Hamiltonian constraint) and the longitudinal part of the traceless extrinsic curvature $\bar{A}^{ij}_L$ (momentum constraint). Four of the remaining eight degrees of freedom are associated with the coordinate freedom - three spatial coordinates hidden in the spatial metric and a time coordinate that is associated with K. This leaves four physical degrees of freedom undetermined - two in the conformally related metric $\bar{\gamma}_{ij}$, and two in the transverse part of traceless extrinsic curvature $\bar{A}^{ij}_{TT}$. These two freely specifiable degrees of freedom carry the dynamical degrees of freedom of the gravitational fields. All others are either fixed by the constraint equations or represent coordinate freedom. 

**We have reduced the Hamiltonian and momentum constraint to equations for the conformal factor ψ and the vector potential $W^i$, from which the longitudinal part of the extrinsic curvature is constructed.**

These quantities can be solved for only after choices have been made for the remaining quantities in the equations, namely the conformally related metric $\bar{γ}_{ij}$, the transverse-traceless part of the extrinsic curvature $\bar{A}^{ij}_{TT}$, the trace of the extrinsic curvature K, and, if present, any matter sources. The choice of these background data has to be made in accordance with the physical or astrophysical situation that one wants to represent. Physically, the choice affects the gravitational wave content present in the initial data, in the sense that a dynamical evolution of data constructed with different background data leads to different amounts of emitted gravitational radiation. It is often not clear how a suitable background can be constructed precisely, and we will return to this issue on several occasions. Given its loose association with the transverse parts of the gravitational fields, one often sets $\bar{A}^{ij}_{TT}$ equal to zero in an attempt to minimize the gravitational wave content in the initial data.

##### Conformal Transformations of Black Hole Solutions

It is instructive to consider some simple, but physically interesting, solutions to the constraint equation.

Consider vacuum solutions for which the matter source terms vanish $(ρ = 0 = S^i)$ and focus on a “moment of time symmetry”. At a moment of time symmetry, all time derivatives of $γ_{ij}$ are zero and the 4−dimensional line interval has to be invariant under time reversal, $t → −t$. The latter condition implies that the shift must satisfy $β^i = 0$ and, hence, the extrinsic curvature also has to vanish everywhere on the slice, $K_{ij} = 0 = K$. On such a time slice the momentum constraints are satisfied trivially. The Hamiltonian constraint reduces to

$$
\bar { D } ^ { 2 } \psi = \frac { 1 } { 8 } \psi \bar { R }
$$

Let us further choose the conformally related metric to be flat,

$$
\bar { \gamma } _ { i j } = \eta _ { i j }
$$

Whenever this is the case, we call the physical spatial metric $γ_{ij}$ conformally flat.

“conformal flatness” refers, for our purposes, to the spatial metric and not the spacetime metric.

In four or any higher dimensions, we can evaluate the Weyl tensor to examine whether any given metric is conformally flat. This is a consequence of the fact that the Weyl tensor is invariant under conformal transformations of the spacetime metric – this explains why it is often called the conformal tensor.

Any spherically symmetric spatial metric is always conformally flat, meaning that we can always write such a metric as $\gamma _ { i j } = \psi ^ { 4 } \eta _ { i j }$. For any spherically symmetric space, we may hence assume conformal flatness without loss of generality.

Assuming conformal flatness dramatically simplifies all calculations, since $\bar{D}_i$ reduces to the flat covariant derivative (and in particular to partial derivatives in cartesian coordinates). Moreover, the Ricci tensor and scalar curvature associated with the conformally related metric must now vanish, $\bar{R}_{ij} = \bar{R} = 0$. Under this assumption, the Hamiltonian constraint becomes the remarkably simple Laplace equation

$$
\bar { D } ^ { 2 } \psi = 0
$$

Spherically symmetric solutions are

$$
\psi = 1 + \frac { \mathcal { M } } { 2 r }
$$

in this particular case, the constant M is in fact the black hole mass M. It shouldn’t come as a great surprise that this is just the Schwarzschild solution in isotropic coordinates.

$$
d l ^ { 2 } = \gamma _ { i j } d x ^ { i } d x ^ { j } = \left( 1 + \frac { M } { 2 r } \right) ^ { 4 } \eta _ { i j } d x ^ { i } d x ^ { j } = \left( 1 + \frac { M } { 2 r } \right) ^ { 4 } \left( d r ^ { 2 } + r ^ { 2 } \left( d \theta ^ { 2 } + \sin ^ { 2 } \theta d \phi ^ { 2 } \right) \right)
$$

This solution forms the basis of the so-called puncture methods for black holes.

The solution is singular at $r = 0$. However, we can show that this singularity is only a coordinate singularity by considering the coordinate transformation

$$
r = \left( \frac { \mathcal { M } } { 2 } \right) ^ { 2 } \frac { 1 } { \hat { r } }
$$

under which the isotropic Schwarzschild metric becomes

$$
d l ^ { 2 } = \left( 1 + \frac { \mathcal { M } } { 2 \hat { r } } \right) ^ { 4 } \left( d \hat { r } ^ { 2 } + \hat { r } ^ { 2 } \left( d \theta ^ { 2 } + \sin ^ { 2 } \theta d \phi ^ { 2 } \right) \right)
$$

The geometry described by metric evaluated at a radius $\hat{r} = a$ is identical to that of the above metric evaluated at $r = a$. The mapping therefore maps the metric into itself, and is hence an isometry. 

In particular, this demonstrates that the origin $r = 0$ is isomorphic to spatial infinity, which is perfectly regular.

![-w570](media/15421762095016/15459872428819.jpg)

This demonstrates that the isotropic radius r covers only the black hole exterior, and that each Schwarzschild R corresponds to two values of the isotropic radius r.

The isotropic radius r corresponding to the smallest areal (or circumferential) radius R is $r = M/2$, which we refer to as the black hole throat. For a single Schwarzschild black hole, the throat coincides with both the apparent and event horizons.

It is almost trivial to generalize our one black hole solution $\psi = 1 + \frac { \mathcal { M } } { 2 r }$ to an arbitrary number of black holes at a moment of time symmetry. Since $\bar { D } ^ { 2 } \psi = 0$ is linear, we obtain the solution simply by adding the individual contribution of each black hole according to

$$
\psi = 1 + \sum_{\alpha} \frac { \mathcal { M } _ { \alpha } } { 2 r _ { \alpha } }
$$

==Here $r_α = |x^i − C_α^i |$ is the (coordinate) separation from the center $C_α^i$ of the αth black hole. **The total mass of the spacetime is the sum of the coefficients $M_α$. However, since the total mass will also include contributions from the black hole interactions, $M_α$ can be identified with the mass of the α-th black hole only in the limit of large separations.**==

Particularly interesting astrophysically and for the generation of gravitational waves is the case of binary black holes

$$
\psi = 1 + \frac { \mathcal { M } _ { 1 } } { 2 r _ { 1 } } + \frac { \mathcal { M } _ { 2 } } { 2 r _ { 2 } }
$$

This simple solution to the constraint equations for two black holes instantaneously at rest at a moment of time symmetry can be used as initial data for head-on collisions of black holes.

In general, the existence of other black holes destroys the symmetry that we found for a single black hole.

![-w822](media/15421762095016/15459884709374.jpg)

Drawing an embedding diagram for such a geometry yields several different “sheets”, where each sheet corresponds to one universe. A geometry containing N black holes may contain up to N + 1 different asymptotically flat universes.

![-w1137](media/15421762095016/15459882037284.jpg)

For each throat we can add terms inside that throat that correspond to images of the other black holes. Doing so, the solution becomes “symmetrized” so that the reflection through each throat is again an isometry. In other words, each Einstein-Rosen bridge connects to the same asymptotically flat universe, and the geometry consists of only two asymptotically flat universes, which are connected by several Einstein-Rosen bridges.

For two equal-mass black holes we may also interpret this solution as a wormhole black hole solution.

![-w816](media/15421762095016/15460003091162.jpg)

Cut off the bottom universe at the two throats, which leaves two “open-ended” throats hanging down from the top universe. We can now identify these two open ends with each other, effectively gluing them together. The two throats now form a “wormhole” that connects to a single, asymptotically flat (but multiply connected) universe. Given the original isometry conditions across the throats, and given that they have the same mass, the resulting metric is smooth across the throat and a valid solution to the Hamiltonian constraint.

In cylindrical coordinates the metric becomes

$$
d l ^ { 2 } = \psi ^ { 4 } \left( d \rho ^ { 2 } + d z ^ { 2 } + \rho ^ { 2 } d \phi ^ { 2 } \right)
$$

where the corresponding conformal factor is given by

$$
\psi = 1 + \sum _ { n = 1 } ^ { \infty } \frac { 1 } { \sinh ( n \mu ) } \left( \frac { 1 } { \sqrt { \rho ^ { 2 } + \left( z + z _ { n } \right) ^ { 2 } } } + \frac { 1 } { \sqrt { \rho ^ { 2 } + \left( z - z _ { n } \right) ^ { 2 } } } \right)
$$

Here $z _ { n } = \operatorname { coth } ( n \mu )$, and μ is a free parameter.

the total mass of this system, which we will identify with the “ADM mass” is

$$
M _ { \mathrm { ADM } } = 4 \sum _ { n = 1 } ^ { \infty } \frac { 1 } { \sinh ( n \mu ) }
$$

The proper distance L along the spacelike geodesic connecting the throats, or equivalently the proper length of a geodesic loop through the wormhole, is

$$
L = 2 \left( 1 + 2 \mu \sum _ { n = 1 } ^ { \infty } \frac { n } { \sinh ( n \mu ) } \right)
$$

The parameter μ is seen to parameterize both the mass and separation of the two holes. Since the solution can be rescaled to arbitrary physical mass, μ effectively determines the dimensionless ratio $L/M_{ADM}$, the parameter that, apart from mass, distinguishes one binary from another in this class of initial data.

#### Mass

There are several important global conserved quantities that characterize an isolated system, such as its total mass and angular momentum. **The rate of loss of these quantities from an isolated system is equal to the rate at which matter, fields and gravitational waves carry them away**.

Once we have solved the constraint equations and constructed a complete set of initial data for a system, we can then determine the values of the global conserved parameters associated with the system.

During a numerical evolution, monitoring the degree to which these parameters are conserved provides a very **useful check on the accuracy of the numerical integration**.

Suppose the system contains matter. Then we can derive an expression for its conserved rest-mass $M_0$ (sometimes called the baryon mass, if the matter is composed of baryons) from the continuity equation,

$$
\nabla _ { a } \left( \rho _ { 0 } u ^ { a } \right) = 0
$$

where $ρ_0$ is the rest-mass density. Integrating this expression over a 4-dimensional region of spacetime Ω yields

$$
\int _ { \Omega } d ^ { 4 } x \sqrt { - g } \nabla _ { a } \left( \rho _ { 0 } u ^ { a } \right) = 0
$$

Using Gauss’s theorem we can relate the divergence of $ρ_0u^a$ inside the region Ω to the value of $ρ_0u^a$ on the region’s 3-dimensional boundary $∂Ω$

$$
\int _ { \Omega } d ^ { 4 } x \sqrt { - g } \nabla _ { a } \left( \rho _ { 0 } u ^ { a } \right) = \int _ { \partial \Omega } d ^ { 3 } \Sigma _ { a } \rho _ { 0 } u ^ { a }
$$

where $d ^ { 3 } \Sigma _ { a } = \epsilon \mathcal { N } _ { a } \sqrt { \gamma } d ^ { 3 } x$ and $\mathcal { N } ^ { a }$ is the outward-pointing unit normal vector on $∂Ω$. When $∂Ω$ is spacelike, the factor $ε = −1$ and when $∂Ω$ is timelike, $ε = +1$.

![](media/15421762095016/15466880052944.jpg)

Now imagine a “pill-box”-shaped spacetime region that is bounded by two spatial slices $Σ_1$ and $Σ_2$ as well as a timelike hypersurface residing entirely outside the source, as illustrated in Figure. In this case only the spatial surfaces contribute to the surface integral. On $Σ_2$ the normal vector Na points toward the future, and therefore coincides with the normal vector $n^a$ of the spacetime foliation $\mathcal { N } _ { a } u ^ { a } = n _ { a } u ^ { a } = - \alpha u ^ { t }$. On $Σ_1$, Na points toward the past, while $n^a$ points toward the future, which introduces a negative sign between them.

The conservation law can therefore be written as

$$
\int _ { \Sigma _ { 1 } } d ^ { 3 } x \sqrt { \gamma } \alpha u ^ { t } \rho _ { 0 } - \int _ { \Sigma _ { 2 } } d ^ { 3 } x \sqrt { \gamma } \alpha u ^ { t } \rho _ { 0 } = 0
$$

implies that the rest mass, defined as

$$
M _ { 0 } = \int _ { \Sigma } d ^ { 3 } x \sqrt { \gamma } \alpha u ^ { t } \rho _ { 0 }
$$

is conserved. The rest-mass is thus determined by a 3−dimensional volume integral over a spatial region spanning the matter source.

**Defining the total mass-energy of the system is more subtle, since it cannot be defined locally in general relativity**. One useful measure of mass-energy is provided by the ADM mass, $M_{ADM}$.

The ADM mass measures the total mass-energy of an isolated gravitating system at any instant of time measured within a spatial surface enclosing the system at infinity.

This definition requires the spacetime to be asymptotically flat and the spacetime metric to approach the Minkowski metric sufficiently quickly with increasing distance from the source,

$$
g _ { a b } - \eta _ { a b } = \mathcal { O } \left( r ^ { - 1 } \right)
$$

The existence of such a surface integral to determine the mass-energy of an isolated system is consistent with the principle that **the total mass-energy of such a system can always by determined by a measurement performed by a distant observer**.

**By definition, the spatial surface $∂Σ_∞$ must be taken out to infinity, in which case $M_{ADM}$ is rigorously conserved**. In numerical applications, **the integral is often evaluated on a large surface at a distant, but finite, radius from the gravitating source**, in the asymptotically flat region of spacetime. In this case, **$M_{ADM}$ will change in time whenever there is a flux of matter or gravitational radiation passing across the surface**. However, the rate of change of $M_{ADM}$ will exactly reflect the rate at which mass-energy is carried across the surface by these fluxes.

> Assume a perfect gas, so that the stress energy tensor is given by
> 
> $$
> T ^ { a b } = \left( \rho _ { 0 } + \epsilon \rho _ { 0 } + P \right) u ^ { a } u ^ { b } + P g ^ { a b }
> $$
> 
> where ε is the specific internal energy density and P the pressure. The difference between the ADM mass $M_{ADM}$ and the rest mass $M_0$ is given by
> 
> $$
> M _ { \mathrm { ADM } } - M _ { 0 } = T + W + U
> $$
> 
> in the Newtonian limit, where T is the kinetic energy,
> 
> $$
> T = \frac { 1 } { 2 } \int \rho _ { 0 } v ^ { 2 } d ^ { 3 } x
> $$
> 
> W the gravitational potential energy,
> 
> $$
> W = \frac { 1 } { 2 } \int \rho _ { 0 } \phi d ^ { 3 } x
> $$
> 
> and U is the internal energy,
> 
> $$
> U = \int \rho _ { 0 } \epsilon d ^ { 3 } x
> $$

On further probing, things can sometimes get a little confusing, as when evaluating the ADM mass for Schwarzschild spacetime in Painleve-Gullstrand coordinates. All the above expressions yield an ADM mass of zero in these coordinates. The reason for this is that the shift in Painleve-Gullstrand coordinates does not fall off sufficiently fast. If nothing else, this result provides a warning to us that we must be careful to check that the metric satisfies the correct asymptotic conditions in the adopted coordinates when applying the above formulae to calculate the mass. It also motivates a search for other mass definitions.

#### Choosing Coordinates

The 3+1 evolution equations for $\gamma_{ij}$ and for $K_{ij}$ are not quite ready for numerical integration. For one thing, we have yet to impose coordinate conditions by specifying the lapse function $α$ and the shift vector $β^i$ that appear in these equations. The lapse and shift are freely specifiable gauge variables that need to be chosen in order to advance the field data from one time slice to the next.

**What constitutes a “good” coordinate system?** Clearly, the adopted coordinates must not allow the appearance of any singularities, which could have dire consequences for a numerical simulation. Such a singularity, which is often associated with a black hole, could be either a coordinate singularity or a physical singularity.

To avoid coordinate singularities associated with horizons, like the one at $r_s = 2M$, black hole simulations have sometimes been carried out using “horizon penetrating” coordinates in which light cones do not pinch-off at the horizon.

The lapse α determines how the shape of the slices Σ changes in time, since it relates the advance of proper time to coordinate time along the normal vector na connecting one spatial slice to the next. The shift $β^i$, on the other hand, determines how spatial points at rest with respect to a normal observer $n^a$ are relabeled on neighboring slices. The spatial gauge or spatial coordinates is therefore imposed by a choice for the shift vector.

![-w727](media/15421762095016/15467428106026.jpg)

##### Geodesic Slicing

Since the lapse $α$ and the shift $β^i$ can be chosen freely, let us first consider the simplest possible choice,

$$
\alpha = 1 , \quad \beta ^ { i } = 0
$$

In the context of numerical relativity this gauge choice is often called geodesic slicing; the resulting coordinates are also known as Gaussian-normal coordinates.

Recall that coordinate observers move with 4−velocities $u ^ { a } = t ^ { a } = e _ { ( 0 ) } ^ { a }$ (i.e. spatial velocities $u^i = 0$). Thus with $β^i = 0$, coordinate observers coincide with normal observers ($u^a = n^a$). With $α = 1$, the proper time intervals that they measure agree with coordinate time intervals. Their acceleration is given by equation

$$
a _ { b } = D _ { b } \ln \alpha = 0
$$

Evidently, since their acceleration vanishes, normal observers are freely-falling and therefore follow geodesics, hence the name of this slicing condition. 

Despite its simplicity, geodesic slicing tends to form coordinate singularities very quickly during an evolution. This result is not surprising, since geodesics tend to focus in the presence of gravitating sources. **Coordinate observers therefore approach each other, collide, and thereby form a coordinate singularity**.
 
As an example, consider a weak gravitational wave that is initially centered on the origin of an otherwise flat vacuum spacetime. After a brief interaction the wave disperses and leaves behind flat space. Also consider a set of coordinate observers that are at rest with respect to each other initially. **The gravitational wave packet carries energy and hence attracts the observers gravitationally, who, initially, start moving toward the origin of the spacetime**. **Once the gravitational wave has dispersed, the observers are no longer attracted gravitationally to the center, but they continue to coast toward each other until they form a coordinate singularity**. As the following exercise demonstrates, we can even estimate the time scale after which this singularity will form.

##### Maximal Slicing

A common choice is the maximal slicing condition

$$
K = 0
$$

If we assume maximal slicing not only on one slice, but at all times, then the time derivative of K must vanish as well,

$$
K = 0 = \partial _ { t } K
$$

$$
D ^ { 2 } \alpha = \alpha \left( K _ { i j } K ^ { i j } + 4 \pi ( \rho + S ) \right)
$$

which we can solve for the lapse $α$ independently of the shift $β^i$.

By construction, maximal slicing prevents the focussing of normal observers that we have found for geodesic slicing. This means that maximal slices are “volume preserving” along the normal congruence $n^a$. **Normal observers in maximal slicing move like irrotational and incompressible fluid elements.** The incompressible property prevents the focussing of normal observers that occurs in geodesic slicing.

##### Harmonic Coordinates

Consider a contraction of the four dimensional connection coefficients

$$
^ { ( 4 ) } \Gamma ^ { a } \equiv g ^ { b c ( 4 ) } \Gamma _ { b c } ^ { a } = - \frac { 1 } { | g | ^ { 1 / 2 } } \partial _ { b } \left( | g | ^ { 1 / 2 } g ^ { a b } \right)
$$

One way to impose a gauge condition is to set these quantities equal to some pre-determined gauge source functions $H^a$,

$$
^ { ( 4 ) } \Gamma ^ { a } = H ^ { a }
$$

In particular, we may choose these gauge source functions to vanish, which defines harmonic coordinates

$$
^ { ( 4 ) } \Gamma ^ { a } = 0
$$

More common in 3+1 calculations is harmonic slicing, in which only the time-component $^ { ( 4 ) } \Gamma ^ { 0 }$ is set to zero.

**The singularity avoidance properties of harmonic slicing are weaker than those, for example, of maximal slicing.**

##### Quasi-isotropic and Radial Gauge

We now turn to gauge conditions for the spatial coordinates, i.e., conditions that specify the shift vector $β^i$.

As is the case when picking a lapse, an important goal when choosing a shift is to provide for a stable, long-term dynamical evolution. In addition, it is often desirable to **bring the spatial metric into a simple form**.

Loosely speaking, two different strategies can be employed when constructing a spatial gauge condition. One strategy is to define a geometric condition on the spatial metric from which a gauge condition can be derived. Alternatively, we can impose an algebraic condition on the spatial metric directly. For example, we can set some of its components to zero in order to simplify the Einstein equations.

In general the spatial metric $γ_{ij}$ has six independent components. Using our three degrees of spatial coordinate freedom we can impose three conditions on the metric, and thereby reduce the number of its independent variables to three.

> In spherical polar coordinates, quasi-isotropic gauge is defined by the three conditions
>
> $$
> \gamma _ { r \theta } = \gamma _ { r \phi } = 0 \\
> \gamma _ { \theta \theta } \gamma _ { \phi \phi } - \left( \gamma _ { \theta \phi } \right) ^ { 2 } = \gamma _ { r r } \gamma _ { \phi \phi } r ^ { 2 }
> $$
> 
> which reduces the metric to the form
>
> $$
> d l ^ { 2 } = A ^ { 2 } \left( d r ^ { 2 } + r ^ { 2 } d \theta ^ { 2 } \right) + B ^ { 2 } r ^ { 2 } ( \sin \theta d \phi + \xi d \theta ) ^ { 2 }
> $$

##### Minimal Distortion

The conformally related metric $\bar{γ}_{ij}$ has five independent functions, two of which correspond to true dynamical degrees of freedom and three to coordinate freedom. For a stable and accurate numerical evolution it is desirable to eliminate purely coordinate-related fluctuations in $\bar{γ}_{ij}$. To accomplish this, one may want to construct a gauge condition that **minimizes the time rate of change of the conformally related metric. This gauge condition is called minimal distortion**.

![-w1093](media/15421762095016/15467419933345.jpg)

An “approximate minimal distortion” condition may lead to a coordinate system with similar geometric properties.

#### Matter Sources

The stress-energy tensor accounts for all sources of energy-momentum in spacetime, excluding gravity. It thus arises from all forms of matter, electromagnetic fields, neutrinos, scalar fields, etc, in the universe.

For brevity, we shall sometimes refer to these sources collectively as the “matter sources” and the terms that they contribute in the 3 + 1 equations as the “matter source terms”. “Matter” source terms appear in the Hamiltonian constraint equation, the momentum constraint equation, and the 3 + 1 evolution equation.

The evolution equations for the “matter” sources are given by $\nabla _ { b } T ^ { a b } = 0$, which express the conservation of the total 4-momentum in spacetime. These conservation equations must be solved simultaneously with the 3 + 1 evolution equations for the gravitational field to determine the entire foliation of spacetime. Some of the quantities appearing in the stress-energy tensor require auxiliary equations. **These auxiliary equations include, for example, the continuity equation and an equation of state in the case of hydrodynamic matter, and Maxwell’s equations in the case of an electromagnetic field**, and so on.

The “matter” source terms ρ, $S_i$ and $S_{ij}$ appearing in the 3+1 equations for the gravitational field are projections of the stress-energy tensor into $n^a$ and Σ and are given by

$$
\rho = n_a n_b T^{ab}  \\
S_i = - \gamma_{ia} n_b T^{ab}  \\
S_{ij} = \gamma_{ia} \gamma_{jb} T^{ab}
$$

The quantity $\rho$ is the total mass-energy density as measured by a normal observer, $S_i$ is the momentum density and $S_{ij}$ is the stress. Finally, S is deﬁned as the trace of $S_{ij}$,

$$
S = \gamma^{ij} S_{ij}
$$

In the following sections, we discuss some of the most important “matter” sources that arise in astrophysical applications. These sources include hydrodynamic fluids, magnetohydrodynamic plasmas threaded by magnetic fields, radiation gases (e.g., photon and neutrino), collisionless matter, and scalar fields.

##### Vacuum

Vacuum spacetimes are characterized by the vacuum stress energy tensor

$$
T^{ab} = 0
$$

**Spacetimes containing black holes and (or) gravitational waves, and nothing else, are characterized by such a stress-energy tensor in Einstein’s ﬁeld equations.** Vacuum spacetimes are simpler to deal with numerically since they require no additional energy-momentum conservation equations or auxiliary ﬁeld equations to solve.

##### Hydrodynamics

Relativistic hydrodynamic matter is an important source of stress-energy in many astrophysical applications. Loosely speaking, a hydrodynamic description of matter is appropriate whenever the mean free path of a particle due to collisions with neighboring particles is much shorter than the characteristic size or local scale length of the system.
 
###### Perfect Gases

The stress-energy tensor of a perfect gas is given by

$$
T^{ab} = \rho_0 h u^a u^b + P g^{ab}
$$

h is the specific enthalpy

$$
h = 1 + \epsilon + P / \rho _ { 0 }
$$

where $\epsilon$ is the specific internal energy density. The total mass-energy density as measured by an observer comoving with the fluid is then given by $\rho ^ { * } = \rho _ { 0 } ( 1 + \epsilon )$.

The local conservation of energy-momentum

$$
\nabla_b T^{ab} = 0
$$

The conservation of rest mass

$$
\nabla_a (\rho_0 u^a) = 0
$$

###### Imperfect Gases

There are many important astrophysical applications that involve imperfect gases characterized by viscosity, conductivity and (or) radiation. For example, viscosity can drive non-axisymmetric instabilities in rotating stars, while radiation can lead to the cooling and contraction of stars.

- Viscosity

    The contribution of viscosity to the stress-energy tensor is

    $$
    T _ { \mathrm { visc } } ^ { a b } = - 2 \eta \sigma ^ { a b } - \zeta \theta P ^ { a b }
    $$
    
    where $η ≥ 0$ is the coefficient of dynamic, or shear, viscosity, $ζ ≥ 0$ is the coefficient of bulk viscosity.

- Heat and Radiation Diffusion

    The contribution of heat flux (i.e. conduction) to the stress-energy tensor is
    
    $$
    T _ { \text { heat } } ^ { a b } = u ^ { a } q ^ { b } + u ^ { b } q ^ { a }
    $$
    
    where the heat-flux 4-vector $q^a$ is given by
    
    $$
    q ^ { a } = - \lambda _ { \mathrm { th } } P ^ { a b } \left( \nabla _ { b } T + T a _ { b } \right)
    $$
    
    T is the temperature, $a ^ { a } = u ^ { b } \nabla _ { b } u ^ { a }$ is the fluid 4-acceleration, and $λ_{th}$ is the coefficient of thermal conduction.

    A useful application of the thermal conduction formalism is heat transport via thermal radiation.
    
- Radiation Hydrodynamics

    In general, **a gas is neither optically thick nor optically thin** (i.e. transparent) everywhere, nor is the radiation always in thermal equilibrium with the matter. In such cases we cannot treat radiation transport in the diffusion approximation as discussed above. To handle the more general case, the radiation field can be described by a radiation stress-energy tensor,
    
    $$
    T _ { \mathrm { rad } } ^ { a b } = \iint d \nu d \Omega I _ { \nu } N ^ { a } N ^ { b }
    $$
    
    The radiation hydrodynamics problem is very difficult to solve in general, since the intensity is a function of six-dimensional phase space plus time, and the radiation transport equation with complicated radiation-fluid interaction terms (including scattering) has a nontrivial integrodifferential character.

##### Magnetohydrodynamics


Magnetic fields play a crucial role in determining the evolution of many relativistic objects. In any highly conducting astrophysical plasma, a frozen-in magnetic field can be amplified appreciably by gas compression or shear. Even when an initial seed field is weak, the field can grow in the course of time to significantly influence the gas dynamical behavior of the system.

![-w760](media/15421762095016/15467601453185.jpg)

###### Electromagnetic Field Equations

Along with the electromagnetic field, we shall assume the presence of a perfect fluid, so that the total stress-energy tensor is given by

$$
T ^ { a b } = \rho _ { 0 } h u ^ { a } u ^ { b } + P g ^ { a b } + T _ { \mathrm { em } } ^ { a b }
$$

The electromagnetic stress-energy tensor

$$
4 \pi T _ { \mathrm { em } } ^ { a b } = F ^ { a c } F _ { c } ^ { b } - \frac { 1 } { 4 } g ^ { a b } F _ { c d } F ^ { c d }
$$

##### Equation of state

For many purposes it is useful to employ a simple “Γ-law” equation of state (EOS) of the form

$$
P = (\Gamma - 1) \rho_0 \varepsilon
$$

Realistic applications involving relativistic objects are **rarely described by EOSs obeying this simple form**. However, a Γ-law EOS provides a computationally practical, albeit crude, approximation that can be adapted to mimick the gross behavior of different states of matter in many applications.

For example, to model **a stiff nuclear EOS in a neutron star**, one can adopt a moderately high value of Γ in a Γ-law EOS, e.g. Γ ≈ 2. By contrast, to model a moderately soft, thermal radiation-dominated EOS governing a very massive or supermassive star, one can set Γ = 4/3. For isentropic flow, a Γ-law EOS is equivalent to the equation of state of a polytrope,

$$
P = K \rho _ { 0 } ^ { \Gamma } , \quad \Gamma = 1 + 1 / n
$$

where n is the polytropic index and K is the gas constant. However, for non-isentropic flow, which is always the case **when encountering a shock, K is no longer constant throughout the fluid**.

##### Collisionless Matter

Several important astrophysical systems are made up of particles of collisionless matter. In such systems, the mean-free-path for particle-particle interactions is much longer than the scale of the system.

One example of a collisionless system is a star cluster, a large, self-gravitating, N-body system in which the individual particles – the stars – interact exclusively via gravitation.

##### Scalar Fields

Scalar fields give rise to particles of spin 0, while vector fields (like the electromagnetic field) give rise to particles of spin 1 (like the photon, in the case of electromagnetism), and tensor fields of rank two or higher give rise to higher-spin particles. A complex scalar field has two degrees of freedom instead of just one, and it can be interpreted as a particle and an antiparticle. Real fields are their own antiparticles. A neutral π meson is an example of a real scalar field, while the charged $π^+$- and $π_−$-mesons are described by complex scalar fields.

### Evolution Equations

The evolution equations evolve the data $(γ_{ab},K_{ab})$ forward in time. However, the Lie derivative along $n^a$, $\mathcal { L } _ { \mathbf { n } }$, is not a natural time derivative since $n^a$ is not dual to the surface 1-form $Ω_a$, i.e. their dot product is not unity

$$
n ^ { a } \Omega _ { a } = - \alpha g ^ { a b } \nabla _ { a } t \nabla _ { b } t = \alpha ^ { - 1 }
$$

Instead, consider the vector

$$
t ^ { a } = \alpha n ^ { a } + \beta ^ { a }
$$

which is dual to $Ω_a$ for any spatial shift vector $β^a$,

$$
t ^ { a } \Omega _ { a } = \alpha n ^ { a } \Omega _ { a } + \beta ^ { a } \Omega _ { a } = 1
$$

It will prove useful to choose $t^a$ to be the congruence along which we propagate the spatial coordinate grid from one time slice to the next slice. In other words, $t^a$ will connect points with the same spatial coordinates on neighboring time slices. Then the shift vector $β^a$ will measure the amount by which the spatial coordinates are shifted within a slice with respect to the normal vector. The lapse function $α$ measures how much proper time elapses between neighboring time slices along the normal vector. The lapse and the shift therefore determine how the coordinates evolve in time. The choice of $α$ and $β^a$ is quite arbitrary. **The freedom to choose these four gauge functions$α$ and $β^a$ completely arbitrarily embodies the four-fold coordinate degrees of freedom inherent in general relativity**.

The lapse and the shift determine how the coordinates evolve from one time slice Σ to the next, whereas the constraint equations represent integrability conditions which have to be satisfied within each slice. Therefore, the constraints have to be independent of how the coordinates evolve, and **the lapse and the shift can enter only the evolution equations**.

![](https://ws4.sinaimg.cn/large/006tNbRwgy1fwgysv45epj31kw0r44qp.jpg)

Observers who are “at rest” relative to the slices follow the normal congruence $n^a$ and are called either normal or Eulerian observers. while observers following the congruence $t^a$ are called coordinate observers. If matter is present it moves entirely independently of the coordinates with four-velocity $u^a$.

Ricci’s equation:

- The evolution equation for the extrinsic curvature:
	
    $$
    \begin{align}
    \mathcal{L}_{t} K_{ab} = &- D_{a} D_{b} \alpha + \alpha (R_{ab} - 2 K_{ac} K^{c}_{\space b} + K K_{ab}) \\
     &- 8 \pi \alpha \left( S_{ab} - \frac{1}{2} \gamma_{ab} \left( S-\rho \right) \right) + \mathcal{L}_{\beta}K_{ab}
    \end{align}
    $$

    > a projection with two indices projected in the normal direction.
    >
    > $$
    > \mathcal{L}_n K_{ab} = n^d n^c \gamma^q_{\space a} \gamma^r_{\space b} {}^{(4)} R_{drcq} - \frac{1}{\alpha} D_a D_b \alpha - K^c_{\space b} K_{ac} \\
    > S_{ab} = \gamma^c_{\space a} \gamma^d_{\space b} T_{cd} \\
    > S = S^a_{\space a}
    > $$

- The evolution equation for the spatial metric $\gamma_{ab}$:
	
    $$
    \mathcal{L}_t \gamma_{ab} = - 2 \alpha K_{ab} + \mathcal{L}_{\beta} \gamma_{ab}
    $$

    > $$
    > K_{ab} = - \frac{1}{2} \mathcal{L}_n \gamma_{ab}
    > $$

It is quite intuitive, though, that things will simplify if we adopt a coordinate system that reflects our 3 + 1 split of spacetime in a natural way. We will see that **the Lie derivative in the evolution equations then reduces to a partial derivative** with respect to coordinate time and, as an additional beneﬁt, we will be able to ignore all timelike components of spatial tensors.

The coupled evolution equations the extrinsic curvature and the spatial metric determine the evolution of the gravitational ﬁeld data $(\gamma_{ab}, K_{ab})$. Together with the constraint equations they are completely equivalent to Einstein’s equations.

Note we have succeeded in recasting Einstein’s equations, which are **second order in time** in their original form, as a coupled set of partial differential equations that are now **first order in time**.

So far, we have expressed our equations in a covariant, coordinate independent manner, i.e. the basis vectors $e_a$ have been completely arbitrary and have no particular relationship to the 1-form $Ω_a$ or to the congruence defined by $t^a$.

### ADM Equations

It is quite intuitive, though, that things will simplify if we adopt a coordinate system that effects our 3 + 1 split of spacetime in a natural way.

To do so, we first introduce a basis of three spatial vectors $e^a_{(i)}$, reside in a particular time slice Σ:

$$
\Omega_a e^a_{(i)} = 0
$$

We extend our spatial vectors to other slices Σ by Lie dragging along $t^a$

$$
\mathcal{L}_t e^a_{(i)} = 0
$$

![-w1116](media/15421762095016/15459114900592.jpg)

As the fourth basis vector we pick $e^a_0 = t^a$. The duality condition then implies that $e^a_{(0)}$ has the components

$$
t ^ { a } = e _ { ( 0 ) } ^ { a } = ( 1,0,0,0 )
$$

This means that the Lie derivative along $t^a$ reduces to a partial derivative with respect to t: $\mathcal{L}_t = \partial_t$. 

Since spatial tensors vanish when contracted with the normal vector, this also means that all components of spatial tensors with a contravariant index equal to zero must vanish.

For the shift vector,

$$
\beta ^ { a } = \left( 0 , \beta ^ { i } \right)
$$

Solving equation $t ^ { a } = \alpha n ^ { a } + \beta ^ { a }$ for $n^a$ then yields the contravariant components

$$
n ^ { a } = \left( \alpha ^ { - 1 } , - \alpha ^ { - 1 } \beta ^ { i } \right)
$$

and from the normalization condition $n_a n^a = −1$ we find

$$
n _ { a } = ( - \alpha , 0,0,0 )
$$

From the definition of the spatial metric we have $\gamma_{ij} = g_{ij}$ meaning that the metric on Σ is just the spatial part of the four-metric. Since zeroth components of spatial contravariant tensors have to vanish, we also have $\gamma^{a0} = 0$.

The inverse metric can therefore be expressed as:

$$
g^{ab} = \gamma^{ab} - n^{a} n^{b} = 
\begin{pmatrix} 
\alpha^{-2} & \alpha^{-2} \beta^{i} \\
\alpha^{-2} \beta^{j} & \gamma^{ij} - \alpha^{2} \beta^{i} \beta^{j}
\end{pmatrix}
$$

We can now invert it and find the components of the four-dimensional metric

$$
g_{ab} = 
\begin{pmatrix} 
\alpha^{-2} + \beta_l \beta^l & \beta_{i} \\
\beta_{j} & \gamma_{ij}
\end{pmatrix}
$$

Equivalently, the line element may be decomposed as:

$$
ds^{2} = - \alpha^{2} dt^{2} + \gamma_{ij} \left( dx^{i} + \beta^{i} dt \right) \left( dx^{j} + \beta^{j}dt \right) 
$$

which is often refered to as the metric in 3 + 1 form. We may interpret this line element as the Pythagorean Theorem for a 4-dimensional spacetime, $ds^2 = - (proper time between neighboring spatial hypersurfaces)^2 + (proper distance within the spatial hypersurface)^2$. This equation thus determines the invariant interval between neighboring points A and B.

![](https://ws1.sinaimg.cn/large/006tNbRwgy1fwhd8c2i4kj31kk10y4qp.jpg)

Therefore, the entire content of the decomposed Einstein equations is contained in their spatial components alone, and we can rewrite:

- the Hamiltonian constraint

	$$
	R + K^2 - K_{ij} K^{ij} = 16 \pi \rho
	$$

- the momentum constraint

	$$
	D_{j} (K^{ij} - \gamma^{ij} K) = 8 \pi S^i
	$$

- the evolution equation for the extrinsic curvature

	$$
	\begin{align}
	\partial_{t} K_{ij} = &- D_{i} D_{j} \alpha + \alpha \left( R_{ij} - 2 K_{ik} K^{k}_{\space j} + K K_{ij}\right) - 8 \pi \alpha \left( S_{ij} - \frac{1}{2} \gamma_{ij} \left( S - \rho \right) \right) \\
	                      &+ \beta^{k} D_{k} K_{ij} + K_{ik} D_{j} \beta^{k} + K_{kj} D_{i} \beta^{k}
	\end{align}
	$$

- the evolution equation for the spatial metric

	$$
	\partial_t \gamma_{ij} = - 2 \alpha K_{ij} + D_i \beta_j + D_j \beta_i
	$$

Equations comprise the “standard” 3 + 1 equations. Sometimes they are referred to as the “ADM” equations.

> The decomposed Einstein equations do not provide any equations for $α$ and $β_i$. Again, this is not surprising, since these functions represent the coordinate freedom of general relativity. **The lapse and shift are therefore arbitrary, and must be determined by imposing gauge conditions**. Clearly, different gauge conditions will lead to different functions for the spatial metric $γ_{ij}$ and the extrinsic curvature $K_{ij}$ when they are used in the evolution equations. Even for a stationary spacetime, like Schwarzschild, **most choices for the lapse and shift will lead to a time-dependent spatial metric function**. Only for special choices of the lapse and the shift will the spatial metric of a stationary spacetime remain time-independent. This will be the case if our time-vector $t^a$ is aligned with a Killing-vector of the spacetime,
> 
> Killing lapse and Killing shift. 
> 
> $$
> ξ^a = t^a = α n^a + β^a.
> $$
> 
> The lapses and shifts that we listed in Table are examples of such Killing lapses and Killing shifts.
> 
> ![-w1184](media/15421762095016/15459155036097.jpg)

![](https://ws4.sinaimg.cn/large/006tNbRwgy1fwhedlba3wj314u13012z.jpg)

### Numerical Methods

As we have seen, Einstein’s field equations in 3 + 1 form consist of a set of nonlinear, multidimensional, coupled partial differential equations in space and time. The equations of motion of the matter fields that may be present are typically of a similar nature. Except for very idealized problems with special symmetries, such equations must be solved by numerical means, often on supercomputers. 

#### Classification of Partial Differential Equations

Consider the general equation of second-order partial differential equation

$$
A \partial _ { \xi } ^ { 2 } \phi + 2 B \partial _ { \xi } \partial _ { \eta } \phi + C \partial _ { \eta } ^ { 2 } \phi = \tilde { \rho }
$$

where the coefficients A, B and C are real, differentiable, and do not vanish simultaneously. Also, the source term $\tilde { \rho }$ may depend on φ, but only up to first order derivatives.

Most of the resulting equations are second-order partial differential equations and can be classified into three categories: elliptic, parabolic or hyperbolic.

- If $AC − B^2 > 0$, then we can find a coordinate transformation from (ξ, η) to some (x, y) that brings equation into the elliptic.

    The prototypical example of an elliptic equation is **Poisson’s equation**,
    
    $$
    \partial _ { x } ^ { 2 } \phi + \partial _ { y } ^ { 2 } \phi = \rho
    $$
    
    where $\rho$ is a source term that may depend on position, or even on $\phi$ up to first-order derivatives. For vanishing sources this equation is Laplace’s equation.

- If $AC − B^2 = 0$, then we can find a coordinate transformation that brings equation into the parabolic.

    An example of a parabolic equation is the diffusion equation,
    
    $$
    \partial _ { t } \phi - \partial _ { x } \left( \kappa \partial _ { x } \phi \right) = \rho
    $$
    
    where $\kappa$ is the diffusion coefficient.

- Finally, if $AC − B^2 < 0$, then we can find a coordinate transformation that brings equation into the hyperbolic.

    The prototypical example of a hyperbolic equation is the wave equation,
    
    $$
    \partial _ { t } ^ { 2 } \phi - c ^ { 2 } \partial _ { x } ^ { 2 } \phi = \rho
    $$
    
    where c is the constant wave speed.

The different types of partial differential equations require different kinds of boundary and/or initial conditions. The differential equations then tell us how these initial fields evolve with time.

We may also have to impose spatial boundary conditions on the outer boundaries of our computational domain. Elliptic equations, on the other hand, determine a solution on a given spatial hypersurface. No initial data are required, but we must supply boundary values at the outer edge(s) of our computational domain.

Boundary conditions can take various forms. For example, Dirichlet conditions specify the values of the solution functions on the boundary, while Neumann conditions specify their gradients on the boundary. 

We introduce the first time derivative of $\phi$ as a new independent variable, say $−k$, in which case we can rewrite the wave equation as the pair of equations

$$
\begin{aligned} \partial _ { t } \phi & = - k \\ \partial _ { t } k & = - c ^ { 2 } \partial _ { x } ^ { 2 } \phi - \rho \end{aligned}
$$

The form is not a particularly elegant representation of a wave equation, since it contains first order time derivatives but second order space derivatives. We can fix that quite easily by also introducing the space derivative of $\phi$ as a new independent variable. With $l \equiv \partial _ { x } \phi$ we now find the system

$$
\begin{array} { l l } { \partial _ { t } \phi } & { = - k } \\ { \partial _ { t } k + c ^ { 2 } \partial _ { x } l } & { = - \rho } \\ { \partial _ { t } l } & { + \partial _ { x } k } & { = 0 } \end{array}
$$

where the last equation holds because the partial derivatives must commute. In a more compact notation we can write this as

$$
\partial _ { t } \mathbf { u } + \mathbf { A } \cdot \partial _ { x } \mathbf { u } = \mathbf { S }
$$

where $u = (φ, k, l)$ is the solution vector, $S = (−k, −ρ, 0)$ is the source vector, and where

$$
\mathbf { A } = \left( \begin{array} { c c c } { 0 } & { 0 } & { 0 } \\ { 0 } & { 0 } & { c ^ { 2 } } \\ { 0 } & { 1 } & { 0 } \end{array} \right)
$$

is the velocity matrix.

Verify that any function

$$
\phi = g ( x + c t ) + h ( x - c t )
$$

satisfies the wave equation for ρ = 0.

Part of the solution φ, namely g, travels along lines $x + ct = const$, while the other part h travels along $x − ct = const$. These lines are called the characteristic curves; they are those curves along which partial information about the solution propagates. Even if we cannot derive the general solution analytically, we can find the corresponding characteristic speeds $dx/dt$ from the eigenvalues of the velocity matrix A. In our example, these eigenvalues are $±c$ and zero, as we would expect.

Since no information can travel faster than the fastest characteristics, the solution at a certain event can only be affected by those events that lie inside the causal past, or past cone defined by the fastest ingoing and outgoing characteristics. In the context of relativity we are quite used to this concept, since causality demands that an event can only be affected by events in its past light cone.

![-w979](media/15421762095016/15467635909343.jpg)

Consider the event Q, whose past characteristics are marked by solid lines. To completely determine $φ(t,r)$ at Q, we would have to provide initial data $φ(0,r)$ and $∂_tφ(0,r)$ inside the past cone defined by the two backward characteristics, namely on the interval $γ_Q$. This is the domain of dependence of the point Q.

**In reverse, for any interval $γ$ on the r-axis, there is a region of the spacetime in which all events depend only on initial data provided on γ. This region is called the domain of determinacy.**

Suppose we want to obtain a solution to the wave equation. We will have to provide initial data on an interval $γ$ that extends from a certain radius $r_{min}$ to a radius $r_{max}$ at, say, $t = 0$. If we want to construct the solution only in the domain of determinacy of γ, then the solution is completely determined by the initial data on γ, and no boundary conditions are needed. This situation, however, is rarely the case. It is more typical that we would like to construct the solution in the entire domain between $r_{min}$ and $r_{max}$ for all $t > 0$.

For concreteness, imagine we want to find φ in the domain between $r_{min}/M = 1$ and $r_{max}/M = 9$, marked by the dashed-dotted lines in Fig. 6.1. The event Q would still be completely determined by the initial data, but the event S, for example, would not. One of its backward characteristics intersects the outer boundary at $r_{max}/M = 9$. The event S is therefore outside the domain of determinacy of γ, and the solution at S depends on more information than is provided by the initial data. This missing information now has to be provided by the boundary conditions.

The situation is different at the inner boundary $r_{min}$. Consider, for example, the event P, which lies on the boundary $r_{min}$. Since we have chosen $r_{min}$ to be inside the event horizon, both characteristics originate from a larger r, and neither one intersects the boundary $r_{min}$. The event P is therefore completely determined by the initial data (and, had we chosen P at a later time, by the outer boundary condition at $r_{max}$). There is no need to impose a boundary condition at $r_{min}$, and in fact it would be inconsistent with the equations. This property will be important when we discuss black hole.

##### Black Hole Horizons

Several different notions of horizons exist in general relativity. The defining property of a black hole is the presence of an event horizon, but apparent horizons also play an extremely important role in the context of numerical relativity. In addition, the concepts of isolated and dynamical horizons serve as useful diagnostics in numerical spacetimes containing black holes.

![](https://ws4.sinaimg.cn/large/006tNbRwgy1fwu1jihe73j316210kn82.jpg)

A black hole is defined as a region of spacetime from which no null geodesic can escape to infinity. The surface of a black hole, the event horizon, acts as a one-way membrane through which light and matter can enter the black hole, but once inside, can never escape. It is the boundary in spacetime separating those events that can emit light rays that can propagate to infinity and those which cannot. More precisely, the event horizon is defined as the boundary of the causal past of future null infinity. The event horizon is a gauge-invariant entity, and contains important geometric information about a black hole spacetime.

The area theorem2 of classical general relativity states that this surface area can never decrease in time,

$$
\delta \mathcal { A } \geq 0
$$

Two-dimensional surface, whose proper surface area we denote as $\mathcal { A }$.

In the collision and coalescence of two or more black holes, the surface area of the remnant black hole must be greater than the sum of the progenitor black holes.

The fact that the event horizon area cannot decrease motivates the definition of the irreducible mass.

$$
M _ { \mathrm { irr } } \equiv \left( \frac { \mathcal { A } } { 16 \pi } \right) ^ { 1 / 2 }
$$

It is possible to extract energy and angular momentum from a rotating Kerr black hole. While such an interaction can reduce the black hole’s mass, it cannot reduce its area, according to the area theorem. The irreducible mass of the black hole cannot decrease, which motivates its name.

The area theorem can be used to place a strict upper limit on the amount of energy that is emitted in gravitational radiation in black hole collisions.

> Consider two widely separated, non-rotating black holes of masses $M_1$ and $M_2$, initially at rest with respect to some distant observer. Use the area theorem to find an upper limit on the energy emitted in gravitational radiation that arises from the head-on collision of the two black holes. Verify that for equal mass black holes at most $29%$ of the total initial energy can be emitted in gravitational radiation.

Given the irreducible mass $M_{irr}$ and the angular momentum $J$ of an isolated, stationary black hole, we can compute the Kerr mass $M (= M_{ADM})$ from

$$
M ^ { 2 } = M _ { \mathrm { irr } } ^ { 2 } + \frac { 1 } { 4 } \frac { J ^ { 2 } } { M _ { \mathrm { irr } } ^ { 2 } }
$$

While the event horizon has some very interesting geometric properties, its global nature makes it very difficult to locate in a numerical simulation. The reason is that **knowledge of the entire future spacetime is required to decide whether or not any particular null geodesic will ultimately escape to infinity**. In numerical simulations an event horizon can be found only “after the fact”, i.e., after the evolution has proceeded long enough to have settled down to a stationary state.

The spacetime singularities inside the black holes must be excluded from the numerical grid, since they would otherwise spoil the numerical calculation.

Several different strategies for avoiding black hole singularities numerically. One approach is based on the realization that, by definition, the interior of a black hole is causally disconnected from, and hence can never influence, the exterior. This fact suggests that we may “excise”, i.e. remove from the computational domain, the spacetime region inside the event horizon. Black hole “excision” requires at least approximate knowledge of the location of the horizon at all times during the evolution.

The singularity theorems of general relativity tell us that if an apparent horizon exists on a given time slice, it must be inside a black hole event horizon. This theorem makes it safe to excise the interior of an apparent horizon from a numerical domain.

Note the absence of an apparent horizon does not necessarily imply that a black hole is absent. 

#### Finite Difference Methods

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

The key point is that we are able to combine the two Taylor expansions in such a way that the leading order error term cancels out, leaving us **with a higher order representation of the derivative**. This cancellation **only works out for uniform grids**, when $∆x$ is independent of x. This is one of the reasons why many current numerical relativity applications of finite difference schemes work with uniform grids.

Higher order derivatives can be constructed in a similar fashion. Adding the two Taylor expansions all terms odd in $∆x$ drop out and we find for the second derivative

$$
\left( \partial _ { x } ^ { 2 } f \right) _ { i } = \frac { f _ { i + 1 } - 2 f _ { i } + f _ { i - 1 } } { ( \Delta x ) ^ { 2 } } + \mathcal { O } \left( \Delta x ^ { 2 } \right)
$$

> $$
> \left( \partial _ { x } f \right) _ { i } = \frac { 1 } { 12 \Delta x } \left( f _ { i - 2 } - 8 f _ { i - 1 } + 8 f _ { i + 1 } - f _ { i + 2 } \right) \\
\left( \partial _ { x } ^ { 2 } f \right) _ { i } = \frac { 1 } { 12 ( \Delta x ) ^ { 2 } } \left( - f _ { i - 2 } + 16 f _ { i - 1 } - 30 f _ { i } + 16 f _ { i + 1 } - f _ { i + 2 } \right)
> $$
> 
> where we have omitted the truncation error, $\mathcal { O } \left( \Delta x ^ { 4 } \right)$

##### Elliptic Equations

As an example of a simple, one-dimensional elliptic equation consider

$$
\partial _ { x } ^ { 2 } f = s
$$

For concreteness, let us assume that the solution f is a symmetric function about $x = 0$, in which case we can restrict the analysis to positive x and impose a Neuman condition at the origin,

$$
\partial _ { x } f = 0 \quad \text { at } x = 0
$$

Let us also assume that f falls off with $1/x$ for large x, which results in the Robin boundary condition

$$
\partial _ { x } ( x f ) = 0 \quad \text { as } x \rightarrow \infty
$$

We will further assume that the source term s is some known function of x.

To do so, we first have to construct a numerical grid that covers an interval between $x_{min} = 0$ and $x_{max}$. We then divide the interval $[x_{min},x_{max}]$ into N gridcells, leading to a gridspacing of

$$
\Delta x = \frac { x _ { \max } - x _ { \min } } { N }
$$

We can choose our grid points to be located either at the center of these cells, which would be referred to as a cell-centered grid, or on the vertices, which would be refered to as a vertex-centered grid. For a cell-centered grid we have N grid points located at

$$
x _ { i } = x _ { \min } + ( i - 1 / 2 ) \Delta x , \quad i = 1 , \ldots , N
$$

![-w1062](media/15421762095016/15467753426795.jpg)

We define two arrays, $f_i$ and $s_i$, which represent the functions f and s at the gridpoints $x_i$ for $i = 1, ..., N$. In the interior of our domain we can represent the differential equation as

$$
f _ { i + 1 } - 2 f _ { i } + f _ { i - 1 } = ( \Delta x ) ^ { 2 } s _ { i } \quad i = 2 , \ldots , N - 1
$$

The boundary condition

$$
\left( \partial _ { x } f \right) _ { 1 / 2 } = \frac { f _ { 1 } - f _ { 0 } } { \Delta x } = 0 \\
f _ { N + 1 } = \frac { x _ { N } } { x _ { N + 1 } } f _ { N } = \frac { x _ { N } } { x _ { N } + \Delta x } f _ { N }
$$

a coupled set of N linear equations for the N elements $f_i$ that we can write as

$$
\left( \begin{array} { c c c c c c c } { - 1 } & { 1 } & { 0 } & { 0 } & { 0 } & { 0 } & { 0 } \\ { 1 } & { - 2 } & { 1 } & { 0 } & { 0 } & { 0 } & { 0 } \\ { 0 } & { \ddots } & { \ddots } & { \ddots } & { 0 } & { 0 } & { 0 } \\ { 0 } & { 0 } & { 1 } & { - 2 } & { 1 } & { 0 } & { 0 } \\ { 0 } & { 0 } & { 0 } & { \ddots } & { \ddots } & { \ddots } & { 0 } \\ { 0 } & { 0 } & { 0 } & { 0 } & { 1 } & { - 2 } & { 1 } \\ { 0 } & { 0 } & { 0 } & { 0 } & { 0 } & { 1 } & { x _ { N } / \left( x _ { N } + \Delta x \right) - 2 } \end{array} \right) \left( \begin{array} { c } { f _ { 1 } } \\ { f _ { 2 } } \\ { \vdots } \\ { f _ { i } } \\ { \vdots } \\ { f _ { N - 1 } } \\ { f _ { N } } \end{array} \right) = ( \Delta x ) ^ { 2 } \left( \begin{array} { c } { s _ { 1 } } \\ { s _ { 2 } } \\ { \vdots } \\ { s _ { i } } \\ { \vdots } \\ { s _ { N - 1 } } \\ { s _ { N } } \end{array} \right)
$$

or, in a more compact form,

$$
\mathbf { A } \cdot \mathbf { f } = ( \Delta x ) ^ { 2 } \mathbf { S }
$$

The solution is given by

$$
\mathbf { f } = ( \Delta x ) ^ { 2 } \mathbf { A } ^ { - 1 } \cdot \mathbf { S }
$$

Sticking with our example in **two dimension**s, another multigrid methods becomes apparent. The numerical solution is computed on a hierarchy of computational grids.

![-w738](media/15421762095016/15467773159499.jpg)

The coarse grid is sufficiently small so that we can **compute a solution with a direct solver** (i.e. direct matrix inversion). This provides **the “global” features of the solution**, albeit on a coarse grid and hence with a large local truncation error. We then **interpolate this approximate solution to the next finer grid**. This **interpolation from a coarser grid to a finer grid is called a “prolongation”**, and we point out that the details of this interpolation depend on whether the grid is cell-centered or vertex-centered. On the finer grid we can then apply a relaxation method. The **interpolation from a finer grid to a coarser grid is called a “restriction”**. The coarser grids now “learn” from the finer grids by comparing their last solution with the one that comes back from a finer grid. This comparison provides an estimate for the local truncation error. These sweeps through the grid hierarchy can be repeated **until the solution has converged to a pre-determined accuracy**.

> $$
> f _ { i , j } = \frac { 1 } { 4 } \left( f _ { i + 1 , j } + f _ { i - 1 , j } + f _ { i , j + i } + f _ { i , j - 1 } \right) - \frac { \Delta ^ { 2 } } { 4 } s _ { i , j }
> $$
> 
> Evidently, finite differencing the flat-space Laplace operator results in each grid function at every grid point being directly related to the average value of its nearest neighbors.

nonlinear elliptic equations

Consider an equation of the form

$$
\nabla ^ { 2 } f = f ^ { n } g
$$

where g is a given function and n is some number. Linear equations that are straighforward to solve by the same matrix techniques. But what about the situation for other values of n, resulting in a nonlinear equation for f? **We linearize the equation first and then iterate to get the nonlinear solution.**

##### Hyperbolic Equations

As a model of hyperbolic equations, consider a “scalar” version of equation. For simplicity it does not contain any source terms, and the the wave speed v is constant.

$$
\partial _ { t } u + v \partial _ { x } u = 0
$$

The equation is satisfied exactly by any function of the form $u ( t , x ) = u ( x - v t )$. In contrast to the elliptic equations the equation **has a time derivative in addition to the space derivative, and thus requires initial data**.

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

![-w1064](media/15421762095016/15467796957524.jpg)

The Courant-Friedrichs-Lewy condition

$$
\frac { | v | \Delta t } { \Delta x } \leq 1
$$

The Courant condition states that the the grid point $u _ { j } ^ { n + 1 }$ at the new time level n+1 has to reside inside the domain of determinacy of the interval spanned by the finite difference stencil at the time level n. This makes intuitive sense: if $u _ { j } ^ { n + 1 }$ were outside this domain, its physical specification would require more information about the past than we are providing numerically, which may trigger an instability.

![-w1063](media/15421762095016/15467802997836.jpg)

Recalling that v represents the speed of a characteristic, we may interpret the Courant condition in terms of the domain of determinacy.

#### Mesh Refinement

Many current numerical relativity codes use a uniform grid spacing to cover the entire spatial domain. On the one hand we have to resolve these sources well, so as to minimize truncation error in the strong-field region. On the other hand, the grid must extend into the weak-field region at large distances from the sources, so as to minimize error from the outer boundaries and to enable us to extract the emitted gravitational radiation accurately.

A very promising alternative is mesh refinement, which has been widely developed and used in the computational fluid dynamics community and is becoming increasingly popular in numerical relativity. 

The basic idea underlying mesh refinement techniques is to perform the simulation **not on one numerical grid, but on several**, as in the multigrid methods.

A coarse grid covers the entire space, and extends to large physical separations. Wherever finer resolution is needed to resolve small-scale structures. Typically, **the gridspacing on the finer grid is half that on the next coarser grid, but clearly other refinement factors can be chosen. The hierarchy can be extended, and typical mesh refinement applications employ multiple refinement levels**.

Two versions of mesh refinement can be implemented. In the simpler version, called **fixed mesh refinement or FMR**, it is assumed that the refined grids will be needed only at known locations in space that remain fixed throughout the simulation. The situation is more complicated for objects that are moving, as is the case for a coalescing binary star system. Moreover, these regions will be changing as the system evolves and the stars move. Clearly, we would like to move the refined grids with the stars. Such an approach, whereby **the grid is relocated during the simulation to give optimal resolution at each time step, is called adaptive mesh refinement or AMR**.

![-w1205](media/15421762095016/15468260860071.jpg)

## Example

### Spherically Symmetric Spacetimes

Non-rotating stars and black holes are themselves spherical, so many important aspects of gravitational collapse, including black hole formation and growth, can be studied in spherical symmetry. For example, the numerical study of spherically symmetric collapse to black holes led to the discovery of critical phenomena in black hole formation.

The field equations reduce to 1+1 dimensions – variables may be written as functions of only two parameters, a time coordinate t and a suitable radial coordinate r.

The high degree of symmetry permits us to write the 3-metric of a spherical spacetime in the general form

$$
d l ^ { 2 } = A d r ^ { 2 } + B r ^ { 2 } \left( d \theta ^ { 2 } + \sin ^ { 2 } \theta d \phi ^ { 2 } \right)
$$

where A and B are functions only of t and r.







Rotation cannot be treated in spherical symmetry. Spinning stars, star clusters and black holes, rotational instabilities in stars and star clusters, relativistic effects induced by the dragging of inertial frames – none of these features are present in spherical symmetry. Moreover, gravitational radiation cannot be generated in spherical spacetimes: Birkhoff’s theorem forbids it.

We thus will have to postpone studying rotation and gravitational wave generation until we relax the restriction to spherical symmetry and advance to axisymmetry. In axisymmetry, spacetime has 2+1 dimensions – two spatial coordinates, e.g., r and a polar angle θ, plus t are necessary to specify the value of any function.