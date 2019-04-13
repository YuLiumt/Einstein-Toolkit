For all but the simplest systems, **analytic solutions for the evolution of such systems do not exist**. Hence the task of **solving Einstein's equations must be performed numerically on a computer**.

!!! note "The dimensionless compaction"
    **Only rather exotic phenomena involve sufficiently strong spacetime curvature to require numerical relativity.** Newtonian gravity clearly works quite well for main sequence stars, planets, and the like. As is well-known, relativity becomes important when speeds approach the speed of light c, so a reasonable guess would be to expect important general relativistic effects as the escape velocity approaches c. Then an object of mass M and radius R will require relativistic treatment if R is close to the gravitational radius $r_{G} \equiv 2 G M / c^{2}$, the radius of a nonspinning black hole of mass M. The same condition can be stated in terms of **the dimensionless compaction** $\mathcal{C} \equiv \frac{G M}{R c^{2}}$。Strong-gravity objects have high compaction (order unity being the standard of “high”). Black holes $(\mathcal{C} \sim 1)$ and neutron stars $(\mathcal{C} \sim 0.1)$ are compact objects by this definition. White dwarfs $\left(\mathcal{C} \sim 10^{-4}\right)$ are a marginal case–relativity plays a large role in their stability condition but not their equilibrium structure–and are usually also classified as compact.

In classical dynamics, the evolution of a system is uniquely determined by the initial positions and velocities of its constituents. By analogy, the evolution of general relativistic gravitational field is determined by specifying **the metric quantities $g_{ab}$ and $\partial_t g_{ab}$ at a given (initial) instant of time t**. Now these metric quantities can be integrated forward in time provided we can obtain from **the Einstein field equations expressions for $\partial^2_t g_{ab}$ at all points on the hypersurface**. That way we can integrate these expressions to compute $g_{ab}$ and $\partial_t g_{ab}$ on a new spacelike hypersurface at some new time $t + \delta t$, and then, by repeating the process, obtain  $g_{ab}$ for all other points $x^0$ and $x^i$ in the (future) spacetime.

## Cauchy problem

**This is a fundamental problem arising in the mathematical theory of partial differential equations.**

The Bianchi identities $\nabla_b G^{ab} = 0$ give

$$
\partial_t G^{a0} = - \partial_i G^{ai} - G^{bc} \Gamma^a_{bc} - G^{ab} \Gamma^c_{bc}
$$

Since no term on the right hand side of equation contains third time derivatives or higher, the four quantities $G^{a0}$ cannot contain second time derivatives. Hence the four equations

$$
G^{a0} = 8 \pi T^{a0}
$$

do not furnish any of the information required for the dynamical evolution of the fields. Rather, they supply **four constraints on the initial data**, i.e. four relations between $g_{ab}$ and $\partial_t g_{ab}$ on the initial hypersurface at $x^0 = t$. The only truly **dynamical equations** must be provided by the six remaining relations

$$
G^{ij} = 8 \pi T^{ij}
$$

!!! note "Analogy Maxwell’s equations"
    $$
    \begin{array} { l } { D _ { i } E ^ { i } - 4 \pi \rho = 0 } \\ D _ { i } B ^ { i } = 0  \end{array}
    $$
    
    The above equations involve only spatial derivatives of the electric and magnetic fields and hold at each instant of time independently of the prior or subsequent evolution of the fields. They therefore constrain any possible configurations of the fields, and are correspondingly called **the constraint equations**.
    
    $$
    \begin{aligned} \partial _ { t } E _ { i } & = \epsilon _ { i j k } D ^ { j } B ^ { k } - 4 \pi j _ { i } \\ \partial _ { t } B _ { i } & = - \epsilon _ { i j k } D ^ { j } E ^ { k } \end{aligned}
    $$
    
    These equations describe how the fields evolve forward in time, and are therefore called **the evolution equations**.
    
    **To completely determine the time evolution of the electromagnetic fields we also have to specify how the sources ρ and $j^i$ evolve according to the net force acting on them.**

It is not surprising that there is a mismatch between the required number (10) of second time 2 derivatives $\partial_t^2 g_{ab}$ and the available number (6) of dynamical ﬁeld equations. After all, there is always a fourfold ambiguity associated with the freedom to choose four different coordinates to label points in spacetime. So, for example, we could always choose Gaussian normal coordinates and set $g_{00} = −1$ and $g_{0i} = 0$.

In general relativity, **the gauge freedom comes in the form of the freedom to choose coordinates arbitrarily**. In order to get a unique solution, we need to impose gauge conditions. (the gauge variables $\alpha, \beta^{i}$)

!!! note "Analogy Maxwell’s equations[^1]"   
    It is possible to bring Maxwell’s equations into a form that is closer to the 3+1 form of Einstein’s equations. To do so, we introduce the vector potential $A ^ { a } = \left( \Phi , A ^ { i } \right)$ and write $B^i$ as
    
    $$
    B _ { i } = \epsilon _ { i j k } D ^ { j } A ^ { k }
    $$
    
    By construction, $B_i$ automatically satisfies the constraint $D _ { i } B ^ { i } = 0$.
    
    The two evolution equations can be rewritten in terms of $E_i$ and $A_i$
    
    $$
    \begin{aligned} \partial _ { t } A _ { i } & = - E _ { i } - D _ { i } \Phi \\ \partial _ { t } E _ { i } & = D _ { i } D ^ { j } A _ { j } - D ^ { j } D _ { j } A _ { i } - 4 \pi j _ { i } \end{aligned}
    $$
    
    With the vector potential $A_i$ we have introduced **a gauge freedom into electrodynamics which is expressed in the freely specifiable gauge variable $Φ$**.
    
    These are supplemented by the matter evolution equations,
    
    $$
    \nabla^{\mu} J_{\mu}=\partial_{t} \rho+D^{i} j_{i}=0
    $$
    
    **Note that $j_i$ does not possess an independent evolution equation.** (Their motion depends on what forces are acting on them.)
    
    The initial value problem in electrodynamics can now be solved in two steps. In the first step, initial data ($A_i$, $E_i$), together with the sources (ρ, $j_i$), are specified that satisfy the constraint equations. In the second step, these fields are evolved according to the evolution equations. **Before the evolution equations can be solved, a suitable gauge condition has to be chosen**.
    
    Returning to the Einstein equations themselves, similar to how the 4-vector $A ^ { a }$ in electromagnetism is not unique due to gauge freedom, ==the metric that satisfies general relativity equation (and any relevant boundary conditions) is not unique.==

[^1]: Smarr, L. & York, J. W. Radiation gauge in general relativity. Phys. Rev. D 17, 1945–1956 (1978).

The Cauchy problem in general relativity logically involves a **decomposition of four-dimensional spacetime into three dimensional space and one-dimensional time**.

## Complications

The equations that arise in **numerical relativity are typically multidimensional, nonlinear, coupled partial differential equations in space and time**. They have in common with other areas of computational physics, like magnetohydrodynamics. However, solving Einstein’s equations poses some additional complications that are unique to general relativity. In general relativity, coordinates are merely labels that distinguish points in spacetime; by themselves coordinate intervals have no physical significance. **To use coordinate intervals to determine physically measurable proper distances and proper times requires the spacetime metric, but the metric is known only after Einstein’s equations have been solved**. Moreover, as the numerical integrations that determine the metric proceed, it often turns out that the original, arbitrary choice of coordinates turns out to be bad, because, for example, singularities appear in the equations. Encountering such **singularities**, be they physical or coordinate, results in some of the terms in Einstein’s equations becoming infinite, **potentially causing overflows in the computer output and premature termination of the numerical integration**.

Treating black holes is one of the main goals of numerical relativity, but this poses another complication. The reason is that **black holes contain physical spacetime singularities** – regions where the gravitational tidal field, the matter density and the spacetime curvature all become infinite. Thus, **when dealing with black holes, it is crucial to choose a computational technique that avoids encountering their interior spacetime singularities in the course of the simulation**.

Another complication arises in the context of one of the most pressing goals of numerical relativity – the calculation of waveforms from promising astrophysical sources of gravitational radiation. These theoretical templates are essential for the identification and physical interpretation of gravitational wave sources. However, the gravitational wave components of the spacetime metric usually constitute small fractions of the smooth background metric. Moreover, to extract the waves from the background in a simulation requires that one probe the numerical spacetime in the far-field, or radiation, zone, which is typically at large distance from the strong-field central source. Yet it is the strong-field region which usually consumes most the computational resources (e.g. spatial resolution) to guarantee accuracy. Furthermore, waiting for the wave to propagate to the far-field region usually takes nonnegligible integration time.
