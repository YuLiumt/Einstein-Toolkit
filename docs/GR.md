# General Relativity

A well-known feature of General Relativity is that space and time are relative but events are absolute. “matter tells spacetime how to curve, and curved spacetime tells matter how to move”.

<!-- more -->

## Manifolds

### Vectors

![image-20181019200818503](https://ws2.sinaimg.cn/large/006tNbRwgy1fwdsgmwtsmj317s0xu78g.jpg)

We define a tangent vector $\upsilon$ at point $p \in M$ to be a map $\upsilon : \mathcal{F}_M \rightarrow R$

- Linear

	$$
	\upsilon (a f + b g) = a \upsilon (f) + b \upsilon (g)
	$$
	
- Obeys the Leibnitz rule
	
	$$
	\upsilon (f g) = f(p) \upsilon (g) + g(p) \upsilon (f)
	$$

Let M be an n-dimensional manifold. Let $p \in M$ and let $V_p$ denote the tangent space at $p$. Then $dim \space V_p = n$.

If  $f \in \mathcal{F} $, For $\mu = 1, ..., n$ define $X_{\mu} : \mathcal{F} \rightarrow R$ by 

$$
X_{\mu}(f):= \frac{\partial f(x)}{\partial x^{\mu}}, \forall f \in \mathcal{F}_M
$$

where $(x^1,  ... , x^n)$ are the Cartesian coordinates of $R^n$. Then $X_1, \cdots, X_n$ are tangent vectors.

An arbitrary tangent vector $\upsilon$ as a sum of the $X_{\mu}$,

$$
\upsilon = \sum^n_{\mu = 1} \upsilon^{\mu} X_{\mu}
$$

### Dual Vector

Let $V$ be any finite-dimensional vector space, linear maps $\omega : V \rightarrow R$ are called dual vectors. 

$$
df|_p(\upsilon) := \upsilon (f),  \space \forall \upsilon \in V_p
$$

If $\frac{\partial}{\partial x^{\nu}}$ is a basis of $V$, we can define elements $dx^{\mu}$

$$
dx^{\mu}(\frac{\partial}{\partial x^{\nu}}) = \frac{\partial}{\partial x^{\nu}}(x^{\mu}) = \delta^{\mu}_{\nu}
$$

${dx^{\mu}}$ is a basis of $V^{*}$. 

### Derivative Operators

A derivative operator, $\nabla$, (sometimes called a covariant derivative) on a manifold $M$ is a map which takes each smooth tensor field of type $(k, l)$ to a smooth tensor field of type $(k, l + 1)$.
Any tow derivative operators $\nabla_a$ and $\tilde{\nabla}_a$ must agree in their action on scalar fields. 

$$
\nabla_a f = \tilde{\nabla}_a f = (df)_a, \forall f \in \cal{F}_M
$$

$\tilde{\nabla}_a - \nabla_a$ defines a map of dual vectors at $p$ to tensors of type $(0, 2)$ at $p$. Consequently $(\tilde{\nabla}_a - \nabla_a)$ defines a tensor of type $(1, 2)$ at $p$, which we will denote as $C^c_{\space ab}$.

$$
\nabla_a \omega_b = \tilde{\nabla}_a \omega_b - C^{c}_{ab} \omega_c
$$

$C^c_{\space ab}$ must also have this property 

$$
C^c_{\space ab} = C^c_{\space ba}     \\
\nabla_a \upsilon^b = C^b_{\space ac} \upsilon^c + \tilde{\nabla}_a \upsilon^b       \\
\nabla_a T^b_{c} = \tilde{\nabla}_a T^b_c + C^b_{\space ad} T^d_c - C^{d}_{\space ac} T^b_d
$$

This displays the possible disagreements of the actions of $\nabla_a$ and $\tilde{\nabla}_a$ on Tensor 

### Parallel Transport

Given a derivative operator $\nabla_a$ we can define the notion of the parallel transport of a vector along a curve $C$ with a tangent $T^a$. A vector $\upsilon^a$ given at each point on the curve is said to be parallel transported as one moves along the curve if the equation 
$$
T^b \nabla_b \upsilon^a = 0
$$
is satisfied along the curve.
Given two vectors $u^a$ and $\upsilon^a$, we demand that their inner product $g_{ab} u^a \upsilon^a$ remain unchanged if we parallel-transport them along any curve. Thus we require 
$$
0 = T^c \nabla_c (g_{ab} u^a \upsilon^b) = g_{ab} u^a T^c \nabla_c (\upsilon^b) + g_{ab} \upsilon^b T^c \nabla_c (u^a) + u^a \upsilon^b T^c \nabla_c (g_{ab})
$$
Equation will hold for all curves and parallel transported vectors if and only if
$$
\nabla_c (g_{ab}) = 0
$$
which is the additional condition we wish to impose on $\nabla_a$.
We attempt to solve for $C^c_{\space ab}$ so that the derivative operator determined by $\tilde{\nabla}_a$ and $C^c_{\space ab}$ will satisfy the required property. 
$$
C^c_{\space ab} = \frac{1}{2} g^{cd} (\partial_a g_{bd} + \partial_b g_{ad} - \partial_d g_{ab})
$$

### Curvature
Given a derivative operator, there exists a notion of how to parallel transport a vector from p to q along a curve C. However, the vector in $V_q$ which we get by this parallel transport procedure starting from a vector in $V_p$ will, in general, depend on the choice of curve connecting them. We can use the path dependence of parallel transport to define an intrinsic notion of curvature. The failure of a vector to return to its original value when parallel transported around a small closed loop is governed by the Riemann tensor.
![Curvatur](https://ws2.sinaimg.cn/large/006tNbRwgy1fwd868s0bbj31kw0qfq7y.jpg)

$(\nabla_a \nabla_b  - \nabla_b \nabla_a)$ defines a linear map dual vectors at $p$ to type $(0, 3)$ tensors at $p$, its action is that of a tensor of $(1, 3)$. Thus, we have shown that there exists a tensor field $R_{abc}^{\space \space \space d}$ such that for all dual vector fields $\omega_c$, we have
$$
\nabla_a \nabla_b \omega_c - \nabla_b \nabla_a \omega_c = R_{abc}^{\space \space \space d} \omega_c
$$
Properties of the Riemann tensor:
1. $R_{abc}^{\space \space \space d} = - R_{bac}^{\space \space \space d}$
2. $R_{[abc]}^{\space \space \space \space \space d} = 0$
3. $R_{abcd} = - R_{abdc}$
4. $\nabla_{[a}R_{bc]d}^{\space \space \space e} = 0​$
5. $R_{abcd} = R_{cdab}$
Its trace over the second and fourth (or equivalently, the first and third) indices defines the Ricci tensor, $R_{ac}$,
$$
R_{ac} = R_{abc}^{\space \space \space b}
$$
The scalar curvature, R, is defined as the trace of the Ricci tensor:
$$
R = R_a^{\space a}
$$
We denote the symmetric and antisymmetric parts of a tensor with brackets () and [] around indices in the usual way. For example
$$
T_{(ab)} = \frac{1}{2} (T_{ab} + T_{ba})  \\
T_{[ab]} = \frac{1}{2} (T_{ab} - T_{ba})
$$
To calculate the curvature by the coordinate component method, we begin by choosing a coordinate system. We express the derivative operator $\nabla_a$ in terms of the ordinary derivative $\partial_a$ of this coordinate system and the Christoffel symbol $\Gamma^c_{\space ab}$.
$$
R_{\mu \nu \rho}^{\space \space \space \sigma} = \frac{\partial}{\partial x^{\nu}} \Gamma^{\sigma}_{\space \mu \rho} - \frac{\partial}{\partial x^{\mu}} \Gamma^{\sigma}_{\space \nu \rho} + \sum_a (\Gamma^{a}_{\space \mu \rho} \Gamma^{\sigma}_{\space a \nu} - \Gamma^{a}_{\space \nu \rho} \Gamma^{\sigma}_{\space a \mu})
$$
Thus, to calculate $R_{\mu \nu \rho}^{\space \space \space \sigma}​$starting from $g_{ab}​$, we first obtain the components, $g_{\mu \nu}​$, of the metric in our coordinate basis. We then calculate $\Gamma^{\sigma}_{\space \mu \nu}​$. Finally we calculate the components $R_{\mu \nu \rho}^{\space \space \space \sigma}​$.

### The Lie Derivative

![image-20181020094318399](https://ws3.sinaimg.cn/large/006tNbRwgy1fweg0piazrj319c0vcao8.jpg)

Consider a (non-zero) vector ﬁeld $X^{a}$ in a manifold $M$. We can find the integral curves $x^a(\lambda)$ (or orbits, or trajectories) of $X$ a by integrating the ordinary differential equations

$$
\frac{dx^{a}}{d \lambda} = X^a (x^a(\lambda))
$$

Here $\lambda​$ is some affine parameter.

![derivative](https://ws1.sinaimg.cn/large/006tNbRwgy1fweykhq3kpj310e0rewug.jpg)

We would now like to define a derivative of a tensor ﬁeld. This involves comparing the tensor ﬁeld at two different points along $X^a$, say $P$ and $Q$, and taking the limit as $Q$ tends to $P$. This is where we encounter a conceptual problem: what do we mean by comparing two tensors at two different locations in the manifold M?

In order to differentiate a tensor in a tensorial manner, we therefore have to evaluate the two tensors at the same point. To do so, we have to drag one tensor to the other point before we can compare the two tensors.

However, this recipe still leaves open how we drag $T^a_{\space b}$ along $X^a$. One approach would be to parallel-transport the tensor $T^a_{\space b}$ from $P$ to $Q$. This idea leads to the definition of the covariant derivative.

Parallel-transporting is not the only way of dragging $T^a_{\space b}$ along $X^a$. In other words, **the Lie derivative along a vector field $X^a$ measures by how much the changes in a tensor ﬁeld along $X^a$ differ from a mere infinitesimal coordinate transformation generated by $X^a$.** Unlike the covariant derivative, the Lie derivative does not require an affine connection, and hence requires less structure.
For a scalar $f$, the Lie derivative naturally reduces to the partial derivative

$$
L_X f = X^b \nabla_b f = X^b \partial_b f
$$

Note that the Lie derivative satisﬁes the Leibnitz rule for outer products.

$$
L_X \upsilon^a = X^b \nabla_b \upsilon^a - \upsilon^b \nabla_b X^a = [X, \upsilon]^a       \\
L_X \omega_a = X^b \nabla_b \omega_a + \omega_b \nabla_a X^b
$$

### Killing Vectors

As an important application, consider the Lie derivative along $X^a$ of a metric $g_{ab}$.

$$
L_X g_{ab} = X^c \nabla_c g_{ab} + g_{cb} \nabla_a X^c + g_{ca} \nabla_b X^c
$$

If $\nabla_a$ is compatible with the metric, the first term vanishes, and we 

$$
L_X g_{ab} = \nabla_a X_b + \nabla_b X_a
$$

A Killing vector ﬁeld $\xi^a$ can now be deﬁned by

$$
L_{\xi} g_{ab} = 0
$$

In other words, a Killing ﬁeld $\xi_a$ generates an isometry of the spacetime, and a displacement along $\xi^a$ leaves the metric invariant. 

Killing's equation

$$
\nabla^a X^b + \nabla^b X^a = 0
$$

In some cases it is very easy to identify a Killing vector. If the metric components are independent of a coordinate $x^i$, then it follows from the property that the coordinate basis vector $e^a$ is a Killing vector.

## Gravitational Wave

### LIGO

The Advanced LIGO gravitational wave observatories performed their first observing run (O1) from September 12, 2015 to January 19, 2016. This provided a total of 51.5 days of coincident observations from the two detectors located in Hanford, WA and Livingston, LA.
A second observing run (O2) of the Advanced LIGO detectors took place from November 30, 2016 to August 25, 2017. The Virgo gravitational wave detector also collected data for part of this period, starting from August 1, 2017.

### Linearized Waves

In the near-ﬁeld region about such sources, the gravitational fields consist of a combination of longitudinal and transverse components that cannot be disentangled unambiguously. As the transverse fields propagate away from their sources, they can be modeled as a linear perturbation of a nearly Minkowski spacetime. These linearized gravitational waves carry information about the nature of the nonlinear sources that generated them. It is these linearized waves that are measured by gravitational wave detectors.

Consider a small perturbation $h_{ab}$ of a known “background” solution to Einstein’s equations. In principle the background could be any solution, but here we are interested in waves propagating in a nearly Minkowski spacetime, for which the metric becomes

$$
g_{ab} = \eta_{ab} + h_{ab} \space |h_{ab} \ll 1|
$$

It is convenient to introduce the “trace-reversed” perturbation

$$
\bar{h}_{ab} = h_{ab} - \frac{1}{2} \eta_{ab} h_c^{\space c}
$$

We can now exploit our coordinate-freedom to impose the “Lorentz gauge” condition,

$$
\nabla_a \bar{h}^{ab} = 0
$$

in which case Einstein’s equations in vacuum reduce to the wave equation

$$
\nabla^c \nabla_c \bar{h}_{ab} = 0
$$

As it turns out, the Lorentz-gauge condition does not determine $\bar{h}_{ab}$ uniquely, since we can introduce further infinitesimal gauge transformations that leave this condition unchanged. We can therefore use this remaining gauge freedom to impose further conditions on the perturbations $\bar{h}_{ab}$. Particularly useful is the transverse-traceless or “TT” gauge, in which

$$
\bar{h}^{TT}_{a0} = 0   \\
{\bar{h}^{TT}}^a_{\space a} = 0
$$

The first condition implies that the only nonzero components of $\bar{h}^{TT}_{ab}$ are purely spatial. The second condition implies that $h_c^{\space c} = 0$, so that, the trace-reversed metric perturbations $\bar{h}_{ab}$ are identical to the original perturbations$h_{ab}$, and we are entitled to drop the bars whenever we write down results in the TT gauge.

The “Lorentz gauge” condition and the “TT” gauge provide eight constraints on the originally ten independent components of $h_{ab}$. The remaining two degrees of freedom correspond to the two possible polarization states of gravitational radiation.

![](https://ws2.sinaimg.cn/large/006tNbRwly1fwvtzi0soyj31kw0kvtf7.jpg)

It is often convenient to express these two polarization states in terms of the two polarization tensors $e^+_{ab}$ and $e^{\times}_{ab}$. For a linear plane wave propagating in vacuum in the z-direction, for example, we have

$$
e^+_{xx} = - e^+_{yy} = 1   \\ 
e^{\times}_{xy} = e^{\times}_{yx} = 1   \\
all \space other \space components \space zero
$$

A general gravitational wave is then specified by two dimensionless amplitudes $h_+$ and $h_×$ as

$$
h^{TT}_{jk} = h_+ e^+_{ij} + h_× e^{\times}_{ij}
$$

Gravitational radiation carries energy, momentum, and angular momentum

### Lensed Gravitational Waves

When a GW signal passes by massive objects, the incoming wave behaves similarly to light in that the signal becomes gravitationally lensed.

## Black Hole

A black hole is deﬁned as a region of spacetime from which no null geodesic can escape to infinity. The surface of a black hole, the event horizon, acts as a one-way membrane through which light and matter can enter the black hole, but once inside, can never escape. It is the boundary in spacetime separating those events that can emit light rays that can propagate to infinity and those which cannot. More precisely, the event horizon is deﬁned as the boundary of the causal past of future null infinity. It is a 2 + 1 dimensional hypersurface in spacetime formed by those outward-going, future-directed null geodesics that neither escape to infinity nor fall toward the center of the black hole. The event horizon is a gauge-invariant entity, and contains important geometric information about a black hole spacetime.

The most general stationary black hole solution to Einstein’s equations is the analytically known Kerr-Newman metric. It is uniquely specified by just three parameters: **the mass M, angular momentum J and the charge Q of the black hole**. Special cases are the Kerr metric (Q = 0), the Reissner-Nordstrom metric (J = 0) and the Schwarzschild metric (J = 0, Q = 0).

### Schwarzschild Black Holes

The Schwarzschild solution for a vacuum spherical spacetime may be written as

$$
d s ^ { 2 } = - \left( 1 - \frac { 2 M } { r } \right) d t ^ { 2 } + \left( 1 - \frac { 2 M } { r } \right) ^ { - 1 } d r ^ { 2 } + r ^ { 2 } d \theta ^ { 2 } + r ^ { 2 } \sin ^ { 2 } \theta d \phi ^ { 2 }
$$

The black hole event horizon is located at $r = 2M$ and is sometimes called the Schwarzschild radius. It is also referred to as the “static limit”, because static observers cannot exist inside $r = 2M$, and the “surface of infinite redshift”, because photons emitted by a static source just outside $r = 2M$ will have infinite wavelength when measured by a static observer at infinity. Circular orbits of test particles exist down to $r = 3M$.

**The singularity in the metric at $r = 2M$ is a coordinate singularity, removable by coordinate transformation**, while the singularity at $r = 0$ is a physical spacetime singularity. In fact, the curvature invariant

$$
I \equiv {}^ { ( 4 ) } R _ { a b c d } {}^ { ( 4 ) } R ^ { a b c d } = 48 M ^ { 2 } / r ^ { 6 }
$$

clearly blows up at the origin, showing that the tidal gravitational field becomes infinite at the center of the black hole.

> One alternative coordinate choice that removes the coordinate singularity at $r = 2M$ is the Kruskal-Szekeres coordinate system.
> 
> $$
> d s ^ { 2 } = \frac { 32 M ^ { 3 } } { r } e ^ { - r / 2 M } \left( - d v ^ { 2 } + d u ^ { 2 } \right) + r ^ { 2 } d \theta ^ { 2 } + r ^ { 2 } \sin ^ { 2 } \theta d \phi ^ { 2 }
> $$
> 
> The original Schwarzschild coordinate system covers only half of the spacetime manifold, while Kruskal-Szekeres coordinates cover the entire manifold.
> 
> ![-w746](media/15421761607281/15454006339592.jpg)

### Kerr Black Holes

The solution to Einstein’s equations describing a stationary, rotating, uncharged black hole of mass M and angular momentum J in vacuum may be expressed in Boyer-Lindquist coordinates in the form

$$
d s ^ { 2 } = - \left( 1 - \frac { 2 M r } { \Sigma } \right) d t ^ { 2 } - \frac { 4 a M r \sin ^ { 2 } \theta } { \Sigma } d t d \phi + \frac { \Sigma } { \Delta } d r ^ { 2 } + \Sigma d \theta ^ { 2 } + \left( r ^ { 2 } + a ^ { 2 } + \frac { 2 a ^ { 2 } M r \sin ^ { 2 } \theta } { \Sigma } \right) \sin ^ { 2 } \theta d \phi ^ { 2 }
$$

where

$$
a \equiv J / M , \quad \Delta \equiv r ^ { 2 } - 2 M r + a ^ { 2 } , \quad \Sigma \equiv r ^ { 2 } + a ^ { 2 } \cos ^ { 2 } \theta
$$

The horizon of the black hole is located at $r_+$, the largest root of the equation $∆ = 0$,

$$
r _ { + } = M + \left( M ^ { 2 } - a ^ { 2 } \right) ^ { 1 / 2 }
$$

The static limit is the surface within which no static observers exist; it resides at $r_0$, the largest root of $g_{tt} = 0$:

$$
r _ { 0 } = M + \left( M ^ { 2 } - a ^ { 2 } \cos ^ { 2 } \theta \right) ^ { 1 / 2 }
$$

The region between the horizon and static limit is called the ergosphere; in this region all time-like observers are dragged around the hole with angular velocity $Ω > 0$.

### Innermost stable circular orbit

The **Innermost stable circular orbit** (often called the **ISCO**) is the smallest circular orbit in which a test particle can stably orbit a massive object in general relativity. The location of the ISCO, the ISCO-radius ($r_{isco}$), depends on the angular momentum (spin) of the central object.

The ISCO plays an important role in black hole accretion disks since it marks **the inner edge of the disk**.

- For a non-spinning massive object, the ISCO is located at,

	$$
	r_{isco} = \frac{6 G M}{c^2} = 3 R_S
	$$
	
- For a rotating black holes

### Global Theorems

In an isolated system, the sum of the surface areas of all black holes can never decrease.

#### Schwarzschild black hole

The fact that the event horizon area cannot decrease motivates the definition of the irreducible mass

$$
M_{irr} = (\frac{\mathcal{A}}{16 \pi})^{\frac{1}{2}}
$$

The definition then implies that the irreducible mass of the black hole cannot decrease, which motivates its name.

The area theorem can be used to place a strict upper limit on the amount of energy that is emitted in gravitational radiation in black hole collisions.

> Consider two widely separated, non-rotating black holes of masses $M_1$ and $M_2$, initially at rest with respect to some distant observer. Use the area theorem to find an upper limit on the energy emitted in gravitational radiation that arises from the head-on collision of the two black holes. Verify that for equal mass black holes at most 29% of the total initial energy can be emitted in gravitational radiation.

#### Kerr black holes

Given the irreducible mass $M_{irr}$ and the angular momentum J of an isolated, stationary black hole, we can compute the Kerr mass M ($= M_{ADM}$) from

$$
M^2 = M^2_{irr} + \frac{1}{4} \frac{J^2}{M^2_{irr}}
$$

According to the area theorem, only the rotational energy contribution can be tapped as a source of energy by an external system interacting with the hole, since the irreducible mass can never decrease.

### Weak Equivalence Principle

We imagine that a body is made up of atoms, and that the inertial mass $m\_I$ of an atom consists of the sum of all the mass and energy contributions of its constituents. But we suppose that the different forms of energy may contribute differently to the gravitational mass $m\_G$ than they do to $m\_I$ . One way to express this is to write $m\_G = m\_I (1 + eta)$, where $eta$  is a dimensionless parameter that measures the difference. Because different forms of energy arising from the relevant subatomic interactions (such as electromagnetic and nuclear interactions) contribute different amounts to the total, depending on atomic structure, $eta$ could depend on the type of atom.

## Alternative Theories of Gravity
The simplest scenario that one could consider in this context is the addition of an extra scalar ﬁeld, but one might also choose to consider extra vectors, tensors, or even higher rank fields. Of course, the effect of such additional ﬁeld needs to be suppressed at scales where General Relativity has been well tested, such as **in the lab or solar system**[^1].

### Scalar-Tensor Theories
A general form of the scalar-tensor theory can be derived from the **Lagrangian density**
$$
\mathcal{L}=\frac{1}{16 \pi } \sqrt{-g} \left[ f\left( \phi \right) R-g\left( \phi \right) \nabla _{\mu }\phi \nabla ^{\mu }\phi -2\Lambda \left( \phi \right) \right] +\mathcal{L}_{m}\left( \psi ,h\left( \phi \right) g_{\mu \nu}\right) 
$$
$\mathcal{L}_m$ is the Lagrangian density of the matter fields $\psi$.

[^1]:	Clifton, T., Ferreira, P. G., Padilla, A., & Skordis, C. (2011, June 14). Modified Gravity and Cosmology. arXiv.org. http://doi.org/10.1016/j.physrep.2012.01.001