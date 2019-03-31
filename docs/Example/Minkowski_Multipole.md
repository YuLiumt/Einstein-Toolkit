We present 3D simulations of a scalar field propagating in a Schwarzschild black hole background. The mass of the scalar field is considered negligible compared to the mass of the black hole and so the back reaction of the scalar field on the metric is not considered.

https://arxiv.org/pdf/gr-qc/0302072.pdf

## IBVP

Numerical solutions of Einstein’s equations involve solving a nonlinear set of partial differential equations on a bounded domain, and formally constitute an initial-boundary-value problem (IBVP).

An IBVP consists of three ingredients: a partial differential equation, initial and boundary data. It is well-posed if a solution exists, is unique, and depends continuously on the initial and boundary data.

Consider the linear IBVP on a domain $[0, \infty) \times \Omega$

$$
\begin{aligned} \partial_{t} u &=A(t, \vec{x})^{i} \partial_{i} u+B(t, \vec{x}) u \\ u(0, \vec{x}) &=f(\vec{x}) \\ w_{+}(t, \vec{x}) &=S w_{-}(t, \vec{x})+g(t, \vec{x}), \vec{x} \in \partial \Omega \end{aligned}
$$

The equations are discretized on a domain $\Omega$ with an inner boundary to accommodate for black hole excision. We introduce the grid points $\vec{r}_{i j k}=(i \Delta x, j \Delta y, k \Delta z) \in \Omega$ and assume that some of these points lie on the boundary $\partial \Omega$.

