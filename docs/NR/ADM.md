The metric $\gamma_{ab}$ and the extrinsic curvature $K_{ab}$ cannot be chosen arbitrarily. Instead, they have to satisfy **certain constraints**.

In order to find these relations, we have to relate the three-dimensional Riemann tensor $R^a_{\space bcd}$ of the the hypersurfaces Σ to the four-dimensional Riemann tensor $^{(4)} R^a_{\space bcd}$ of M. To do so, we first take a completely spatial projection of $^{(4)} R^a_{\space bcd}$, then a projection with one index projected in the normal direction, and finally a projection with two indices projected in the normal direction. All other projections vanish identically because of the symmetries of the Riemann tensor. A decomposition of $^{(4)} R^a_{\space bcd}$ into spatial and normal pieces therefore involves these **three different types of projections**.

$$
\begin{align}
^{(4)} R_{abcd} &= \gamma_a^{\space p} \gamma_b^{\space q} \gamma_c^{\space r} \gamma_d^{\space s} {}^{(4)} R_{pqrs} - 2 \gamma_a^{\space p} \gamma_b^{\space q} \gamma_{[c}^{\space r} n_{d]} n^s {}^{(4)} R_{pqrs} \\
&- 2 \gamma_c^{\space p} \gamma_d^{\space q} \gamma_{[a}^{\space r} n_{b]} n^s {}^{(4)} R_{pqrs} + 2 \gamma_a^{\space p} \gamma_{[c}^{\space r} n_{d]} n_b n^q n^s {}^{(4)} R_{pqrs} \\
&- 2 \gamma_b^{\space p} \gamma_{[c}^{\space r} n_{d]} n_a n^q n^s {}^{(4)} R_{pqrs} 
\end{align}
$$

The above projections give rise to the equations of Gauss, Codazzi and Ricci.

!!! note "Gauss’ equation and Codazzi equations give rise to the “constraint” equations."
    Gauss’ equation:

    a completely spatial projection.

    $$
    R_{abcd} + K_{ac} K_{bd} -K_{ad} K_{cb} = \gamma^P_{\space a} \gamma^q_{\space b} \gamma^r_{\space c} \gamma^s_{\space d} {}^{(4)} R_{pqrs}
    $$

    Codazzi equation:

    $R^a_{\space bcd}$ with one index projected in the normal direction.

    $$
    D_b K_{ac} - D_a K_{bc} = \gamma^P_{\space a} \gamma^q_{\space b} \gamma^r_{\space c} n^s_{\space d} {}^{(4)} R_{pqrs}
    $$
    
## Constraint equations

We can rewrite Einstein’s ﬁeld equations in a 3+1 form. Basically, we just need to take the equations of Gauss, Codazzi and Ricci and eliminate the four-dimensional Rieman tensor using Einstein’s equations.

$$
G_{ab} = {}^{(4)} R_{ab} - \frac{1}{2} {}^{(4)} R g_{ab} = 8 \pi T_{ab}
$$

We will first derive the constraint equations from Gauss’ equation and the Codazzi equation.

- Contracting Gauss’ equation:

$$
R + K^2 - K_{ab}K^{ab} = 16 \pi \rho
$$ 

!!! note
    We now define the energy density ρ to be the total energy density as measured by a normal observer $n^a$,

    $$
    \rho = n_a n_b T^{ab} \\
    2 n^p n^r G_{pr} = R + K^2 - K_{ab} K^{ab}
    $$
    
- Contracting the Codazzi equation:

$$
D_b K^b_{\space a} - D_a K = 8 \pi S_a
$$

!!! note
    We now define $S_a$ to be the momentum density as measured by a normal observer $n^a$,

    $$
    S_a = - \gamma^b_{\space a} n^c T_{bc} \\
    D_b K^b_{\space a} - D_a K = - \gamma^q_{\space a} n^s G_{qs}
    $$
    
They are **the conditions** that allow a three-dimensional slice Σ with data $(\gamma_{ab}, K_{ab})$ to be embedded in a four-dimensional manifold M with data $g_{ab}$. We will discuss strategies for **solving the constraint equations and finding initial data** that represent a snapshot of the gravitational fields at a certain instant of time.

**Constraint equations constrain the fields in space at one instant of time, independently of their past history.**

!!! note "Freedom"
    The four constraint equations cannot determine all of the gravitational fields $(\gamma_{ij}, K_{ij})$.

    Both $\gamma_{ij}$ and $K_{ij}$ are symmetric, three-dimensional tensors, they together have **twelve independent components**.

    - The **four constraint equations** can only determine four of these
    - Four undetermined functions are related to **coordinate choices**
    - Two independent sets of values for **the conjugate pair** $(\gamma_{ij}, K_{ij})$.
    - Two dynamical degrees of freedom correspond to **the two polarization modes of a gravitational wave**

    It is quite intuitive that the state of a dynamical field, like a **gravitational wave, cannot be determined from constraint equations**. Waves satisfy hyperbolic equations, and their state at any time depends on their past history. It is therefore natural that the constraint equations serve to constrain only the “longitudinal” parts of the fields, while the “transverse” parts, related to the dynamical degrees of freedom, remain freely specifiable.

    Ideally one would like to separate unambiguously the longitudinal from the transverse parts of the fields at some initial time, freely specifying the latter and then solving the constraints for the former. Given the nonlinear nature of general relativity such a rigorous separation is not possible; instead, all these fields are entangled in the spatial metric and the extrinsic curvature.

## Evolution Equations

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

![](media/15533305224645.jpg)

Observers who are “at rest” relative to the slices follow the normal congruence $n^a$ and are called either normal or Eulerian observers. while observers following the congruence $t^a$ are called coordinate observers. If matter is present it moves entirely independently of the coordinates with four-velocity $u^a$.

Ricci’s equation:

- The evolution equation for the extrinsic curvature:

$$
\begin{align}
\mathcal{L}_{t} K_{ab} = &- D_{a} D_{b} \alpha + \alpha (R_{ab} - 2 K_{ac} K^{c}_{\space b} + K K_{ab}) \\
 &- 8 \pi \alpha \left( S_{ab} - \frac{1}{2} \gamma_{ab} \left( S-\rho \right) \right) + \mathcal{L}_{\beta}K_{ab}
\end{align}
$$

!!! note
    a projection with two indices projected in the normal direction.

    $$
    \mathcal{L}_n K_{ab} = n^d n^c \gamma^q_{\space a} \gamma^r_{\space b} {}^{(4)} R_{drcq} - \frac{1}{\alpha} D_a D_b \alpha - K^c_{\space b} K_{ac} \\
    S_{ab} = \gamma^c_{\space a} \gamma^d_{\space b} T_{cd} \\
    S = S^a_{\space a}
    $$
    
- The evolution equation for the spatial metric $\gamma_{ab}$:

$$
\mathcal{L}_t \gamma_{ab} = - 2 \alpha K_{ab} + \mathcal{L}_{\beta} \gamma_{ab}
$$

!!! note
    $$
    K_{ab} = - \frac{1}{2} \mathcal{L}_n \gamma_{ab}
    $$
    
It is quite intuitive, though, that things will simplify if we adopt a coordinate system that reflects our 3 + 1 split of spacetime in a natural way. We will see that **the Lie derivative in the evolution equations then reduces to a partial derivative** with respect to coordinate time and, as an additional beneﬁt, we will be able to ignore all timelike components of spatial tensors.

The coupled evolution equations the extrinsic curvature and the spatial metric determine the evolution of the gravitational ﬁeld data $(\gamma_{ab}, K_{ab})$. Together with the constraint equations they are completely equivalent to Einstein’s equations.

Note we have succeeded in recasting Einstein’s equations, which are **second order in time** in their original form, as a coupled set of partial differential equations that are now **first order in time**.

So far, we have expressed our equations in a covariant, coordinate independent manner, i.e. the basis vectors $e_a$ have been completely arbitrary and have no particular relationship to the 1-form $Ω_a$ or to the congruence defined by $t^a$.

## ADM Equations

It is quite intuitive, though, that things will simplify if we adopt a coordinate system that effects our 3 + 1 split of spacetime in a natural way.

To do so, we first introduce a basis of three spatial vectors $e^a_{(i)}$, reside in a particular time slice Σ:

$$
\Omega_a e^a_{(i)} = 0
$$

We extend our spatial vectors to other slices Σ by Lie dragging along $t^a$

$$
\mathcal{L}_t e^a_{(i)} = 0
$$

![](media/15533309561126.jpg)

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

which is often refered to as the metric in 3 + 1 form. We may interpret this line element as the Pythagorean Theorem for a 4-dimensional spacetime, 

$$
ds^2 = - (properTime)^2 + (properDistance)^2
$$
 
This equation thus determines the invariant interval between neighboring points A and B.

![](media/15533309901685.jpg)

Therefore, the entire content of the decomposed Einstein equations is contained in their spatial components alone, and we can rewrite:

- the Hamiltonian constraint:

$$
R + K^2 - K_{ij} K^{ij} = 16 \pi \rho
$$

- the momentum constraint:

$$
D_{j} (K^{ij} - \gamma^{ij} K) = 8 \pi S^i
$$

- the evolution equation for the extrinsic curvature:

$$
\begin{align}
\partial_{t} K_{ij} = &- D_{i} D_{j} \alpha + \alpha \left( R_{ij} - 2 K_{ik} K^{k}_{\space j} + K K_{ij}\right) - 8 \pi \alpha \left( S_{ij} - \frac{1}{2} \gamma_{ij} \left( S - \rho \right) \right) \\
                      &+ \beta^{k} D_{k} K_{ij} + K_{ik} D_{j} \beta^{k} + K_{kj} D_{i} \beta^{k}
\end{align}
$$

- the evolution equation for the spatial metric:

$$
\partial_t \gamma_{ij} = - 2 \alpha K_{ij} + D_i \beta_j + D_j \beta_i
$$

Equations comprise the “standard” 3 + 1 equations. Sometimes they are referred to as the “ADM” equations.

![](media/15533310424874.jpg)
