The GRHydro scheme is written in a first-order hyperbolic flux-conservative evolution system for the conserved variables D, $S^{i}$, $\tau$, and $\mathcal{B}^{i}$, defined in terms of the primitive variables $\rho$, $\epsilon$, $v^{i}$, and $B^{i}$ such that

$$
\begin{aligned} D &=\sqrt{\gamma} \rho W \\ S_{j} &=\sqrt{\gamma}\left(\rho h^{*} W^{2} v_{j}-\alpha b^{0} b_{j}\right) \\ \tau &=\sqrt{\gamma}\left(\rho h^{*} W^{2}-P^{*}-\left(\alpha b^{0}\right)^{2}\right)-D \\ \mathcal{B}^{k} &=\sqrt{\gamma} B^{k} \end{aligned}
$$

where $\gamma$ is the determinant of $\gamma_{i j}$. We choose a definition of the 3-velocity $v^{i}$ that corresponds to the velocity seen by an Eulerian observer at rest in the current spatial 3-hypersurface 

$$
v^{i}=\frac{u^{i}}{W}+\frac{\beta^{i}}{\alpha}
$$

and $W \equiv\left(1-v^{i} v_{i}\right)^{-1 / 2}$ is the Lorentz factor. Note that $v^{i}$, $B^{i}$, $S^{i}$, and $\beta^{i}$ are 3-vectors, and their indices are raised and lowered with the 3-metric, e.g., $v_{i} \equiv \gamma_{i j} v^{j}$.

The evolution system for the conserved variables,

$$
\frac{\partial \mathbf{U}}{\partial t}+\frac{\partial \mathbf{F}^{i}}{\partial x^{i}}=\mathbf{S}
$$

with 

$$
\mathbf{U}=\left[D, S_{j}, \tau, \mathcal{B}^{k}\right] \\
\mathbf{F}^{i}=\alpha \times \left[ \begin{array}{c}{D \tilde{v}^{i}} \\ {S_{j} \tilde{v}^{i}+\sqrt{\gamma} P^{*} \delta_{j}^{i}-b_{j} \mathcal{B}^{i} / W} \\ {\tau \tilde{v}^{i}+\sqrt{\gamma} P^{*} v^{i}-\alpha b^{0} \mathcal{B}^{i} / W} \\ {\mathcal{B}^{k} \tilde{v}^{i}-\mathcal{B}^{i} \tilde{v}^{k}}\end{array}\right] \\
\mathbf{S}=\alpha \sqrt{\gamma} \times \left[ \begin{array}{c}{0} \\ {T^{\mu \nu}\left(\frac{\partial g_{i}-\Gamma_{\mu}^{\lambda} g_{\lambda j} )}{\partial x^{\mu}}-\Gamma_{\mu}^{\lambda} g_{\lambda j}\right)} \\ {\alpha\left(T^{\mu 0 \ln \alpha}-T^{\mu \nu} \Gamma_{\mu \nu}^{0}\right)} \\ {\overline{0}}\end{array}\right]
$$

Here, $\tilde{v}^{i}=v^{i}-\beta^{i} / \alpha$ and $\Gamma_{\mu \nu}^{\lambda}$ are the 4-Christoffel symbols.

The magnetic field is divergence-free, the “no-monopoles” constraint:

$$
\nabla \cdot B \equiv \frac{1}{\sqrt{\gamma}} \partial_{i}\left(\sqrt{\gamma} B^{i}\right)=0
$$

which also implies

$$
\partial_{i} \mathcal{B}^{i}=0
$$

In practice, **we implement two different methods to actively enforce this constraint**. In the “divergence cleaning” technique we include the ability to modify the magnetic field evolution by introducing a new field variable that dissipates away numerical divergences. An alternative method, commonly called “constrained transport”, instead carefully constructs a numerical method so that the constraint is satisfied to round-off error at the discrete level.

**Reconstruction**

___

The GRHydro code now includes several improved higher-order techniques for **reconstruction of states at cell interfaces for use in high-resolution shock capturing**.

In a finite-volume scheme, **one evaluates fluxes at cell faces by solving Riemann problems involving potentially discontinuous hydrodynamic states on either side of the interface**. To construct these Riemann problems, one must first obtain the fluid state on the left and right sides of the interface. The fluid state is known within cells in the form of cell-averages of the hydrodynamical variables. The reconstruction step interpolates the fluid state from cell averaged values to values at cell interfaces without introducing oscillations at shocks and other discontinuities. It is possible to reconstruct either primitive or conserved fluid variables, however using the former makes it much easier to guarantee physically valid results for which the pressure is positive and the fluid velocities sub-luminal.

By assuming an orthogonal set of coordinates, it is possible to reconstruct each coordinate direction independently. Thus, the fluid reconstruction reduces to a one-dimensional problem.

**Primitive variable transformations**

___

In GRHD, converting the conservative variables back to the primitives is a relatively straightforward task. GRHydro accomplishes the task through a 1D Newton-Raphson scheme. The scheme iterates by estimating the fluid pressure, determining the density, internal energy and the conservative quantities given this estimate, and using those in turn to calculate a new value for the pressure and its residual. The method works for any EOS, so long as one can calculate the thermodynamic derivatives $\mathrm{d} P / \mathrm{d}\left.\rho\right|_{\epsilon}$ and $\mathrm{d} P / \mathrm{d}\left.\epsilon\right|_{\rho}$.

**Equation of State**

___

We implement the conversion of conserved to primitive variables for arbitrary equations of state (EOS), including polytropic, Γ-law, hybrid polytropic/Γ-law, and microphysical finite-temperature EOS.

The Einstein Toolkit EOS interface uses the pressure as a function of density and the specific internal energy $\epsilon$, $P=P(\rho, \epsilon)$, and calculates partial derivatives against those variables, rather than $\rho$ and $u$.

## GRMHD

The MHD code stores the values of both the primitive B-field vector, $B^{i}$ (`Bvec` in the code), and the evolved conservative field $B^{i}$ (`Bcons` in the code) in the frame of the Eulerian observers. For analysis purposes we include options to compute the magnetic field in fluid’s rest frame

$$
\begin{aligned} b^{0} &=\frac{W B^{k} v_{k}}{\alpha} \\ b^{i} &=\frac{B^{i}}{W}+W\left(B^{k} v_{k}\right)\left(v^{i}-\frac{\beta^{i}}{\alpha}\right) \\ b^{2} &=\frac{B^{i} B_{i}}{W^{2}}+\left(B^{i} v_{i}\right)^{2} \end{aligned}
$$

