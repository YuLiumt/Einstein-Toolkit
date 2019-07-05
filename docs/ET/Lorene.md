[LORENE](https://lorene.obspm.fr) is a set of C++ classes to solve various problems arising in numerical relativity, and more generally in computational astrophysics. It provides tools to solve partial differential equations by means of multi-domain spectral methods.

The online service [CompOSE](https://compose.obspm.fr/#) provides data tables for different state of the art equations of state (EoS) ready for further usage in astrophysical applications, nuclear physics and beyond. The cold neutron star EoS tables can be used directly within LORENE to obtain models of (rotating/magnetised) neutron stars.

## Using LORENE

Typing 

```
make
```

will produce the executable file <executable>.

You can clear everything by

```
make uninstall
```

Simply type

```
<executable>
```

## Example

Bin_star
___

The procedure needs 7 parameter files named `par_eos1.d`, `par_eos2.d`, `par_grid1.d`, `par_grid2.d`, `par_init.d`, `parcoal.d`. The first two files contain the informations about each neutron star EOS, then two of them set the grid points and size, while `par_init.d` contains the physical values for the initial conditions and `parcoal.d` the parameters for the equilibrium condition.

This class includes the sources of the executables we need, namely `init_bin` and `coal`

```
make init_bin
./init_bin
make coal
./coal
```