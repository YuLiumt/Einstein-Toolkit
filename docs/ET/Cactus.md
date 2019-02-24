Cactus executables always run from a parameter file, which specifies which thorns to use and sets the values of each thorn’s parameters (the parameters that are not set will take on default values).

There is no restriction on the name of the parameter file, although it is conventional to use the file extension `.par`.

A parameter file is a text file whose lines are either comments or parameter statements. Comments are blank lines or lines that begin with ‘#’. A parameter statement consists of one or more parameter names, followed by an ‘=’, followed by the value(s) for this (these) parameter(s).

The first parameter statement in any parameter file should set ActiveThorns, which is a special parameter that tells the program which thorns are to be activated. Only parameters from active thorns can be set (and only those routines scheduled by active thorns are run). By default all thorns are inactive.

For example,

```
ActiveThorns = "CartGrid3D"
```

Parameter specifications following ActiveThorns usually are carried out by listing the name of the thorn which defined the parameter, two colons, and the name of the parameter

```
wavetoyF77::amplitude
```

If any of the parameters in the parameter file does not belong to any of the active thorns, or if the parameter value is not in the allowed range, an error is registered.

For example, 

```
# if the parameter is not recognised
Unknown parameter time::ddtfac
# if the parameter value is not in the allowed range
Unable to set keyword CartGrid3D::type - ByMouth not in any active range
```