## Partial Differential Equation (PDE)

### SummationByParts

#### interface

In order to use the derivative routines the appropriate declarations have to be added to your thorns `interface.ccl` ﬁle. To use the interface that works on whole grid functions you have to add:

```
SUBROUTINE Diff_gv ( CCTK_POINTER_TO_CONST IN cctkGH, \  
                     CCTK_INT IN dir, \  
                     CCTK_REAL IN ARRAY var, \  
                     CCTK_REAL OUT ARRAY dvar, \  
                     CCTK_INT IN table_handle )  
USES FUNCTION Diff_gv
```

| Name | Describe |
| ------------ | ------------- |
| cctkGH | the cactus GH |
| dir | the direction of the derivative (0 for x, 1 for y and 2 for z) |
| var | the grid function to calculate derivatives. |
| dvar | the grid function where the derivative is to be stored |
| table_handle | an integer that contains a handle for a keyword table with optional parameters. If there are no optional parameters just pass in a negative value. |

To use the interface that returns the ﬁnite difference coefficients you have to add:

```
SUBROUTINE Diff_coeff ( CCTK_POINTER_TO_CONST IN cctkGH, \  
                        CCTK_INT IN dir, \  
                        CCTK_INT IN nsize, \  
                        CCTK_INT OUT ARRAY imin, \  
                        CCTK_INT OUT ARRAY imax, \  
                        CCTK_REAL OUT ARRAY q, \  
                        CCTK_INT IN table_handle )
```

| Name | Describe |
| ------------ | ------------- |
| cctkGH | the cactus GH |
| dir | the direction of the derivative (0 for x, 1 for y and 2 for z) |
| nsize | the grid size in the direction you are interested in |
| imin and imax | the minimum and maximum index of the stencil in direction dir |
| q | a 2D real array of size `nsize×nsize` that on return will contain the coefficients for the derivatives. |
| table_handle | an integer that contains a handle for a keyword table with optional parameters. If there are no optional parameters just pass in a negative value. |

## Initial data

### ADMMacros

#### Source File

- `<MACRONAME>_declare.h`

sets up the declarations for the internal macro variables. All the internal (hidden) variables have names beginning with the macro name. This ﬁle should be included in the declarations section of your routine.

- `<MACRONAME>_guts.h`

is the actual included source code which will calculate the quantities.

- `<MACRONAME>_undefine.h`

resets the macros. This ﬁle must be #included at the end of every loop using macros. Without this, a second loop using macros would assume that quantities have already been calculated (and thus get wrong results).

Note that all ADMMacro include files are in the directory EinsteinBase/ADMMacros/src/macro/, so you include the macros with lines such as

```
#include "EinsteinBase/ADMMacros/src/macro/<MACRONAME>_<TYPE>.h"
```

## Analysis

### StaticConformal

#### interface

`StaticConformal` provides aliased functions to convert between physical and conformal 3-metric values.
