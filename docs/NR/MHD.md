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

In practice, we implement two different methods to actively enforce this constraint. In the “divergence cleaning” technique we include the ability to modify the magnetic field evolution by introducing a new field variable that dissipates away numerical divergences. An alternative method, commonly called “constrained transport”, instead carefully constructs a numerical method so that the constraint is satisfied to round-off error at the discrete level.