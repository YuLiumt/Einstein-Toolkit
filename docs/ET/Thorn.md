**A thorn is the basic working module within Cactus.** All user supplied code goes into thorns, which are, by and large, independent of each other. Relationships among thorns are all based upon relationships among the implementations they provide.

**Thorns are grouped into arrangements.** This is a logical grouping of thorns which is purely for organisational purposes. The arrangements live in the `arrangements` directory of the main Cactus directory.

A thorn consists of a subdirectory of an arrangement containing four administrative files:

| Name | Describe |
| ------------ | ------------- |
| interface.ccl | This defines the implementation the thorn provides, and the variables the thorn needs, along with their visibility to other implementations. |
| param.ccl | This defines the parameters that are used to control the thorn, along with their visibility to other implementations. |
| schedule.ccl | This defines which functions are called from the thorn and when they are called. It also handles memory and communication assignment for grid variables. |
| configuration.ccl | This file is optional for a thorn. If it exists, it contains extra configuration options of this thorn. |

Thorns can also contain

- a subdirectory called `src`, which should hold source files and compilation instructions for the thorn 
- a subdirectory `src/include` for include files
- a `README` containing a brief description of the thorn
- a `doc` directory for documentation
- a `par` directory for example parameter files
- a `test` subdirectory may also be added, to hold the thorn’s test suite.

!!! note
    Each thorn provides 3 configuration files (interface.ccl, param.ccl, schedule.ccl), detailing its interface with the Flesh and with other thorns.
    
    Cactus contains a rule-based scheduling system, which determines which routines, from which thorns are run in which order.

**Cactus Configuration Language (CCL) files are text files used to define configuration information for a thorn.** Their formal syntax is described using Piraha, a parsing expression grammar engine that supports multiple languages (see https://github.com/stevenrbrandt/piraha-peg for a description of formal syntax).

!!! note
    CCL files are (mostly) case independent, and may contain comments introduced by the hash ‘#’ character, which indicates that the rest of the line is a comment. If the last non-blank character of a line in a CCL file is a backslash ‘\’, the following line is treated as a continuation of the current line.

## The interface.ccl File

The `interface.ccl` file is used to declare

- A header block giving details of the thorn’s relationship with other thorns.
- A block detailing which include files are used from other thorns, and which include files are provided by this thorn.
- Blocks detailing aliased functions provided or used by this thorn.
- A series of blocks listing the thorn’s global variables.

**Header Block:**

___

The implementation is declared by a single line at the top of the file

`implements: <name>`

declares that the thorn provides an implementation called `<name>`. The implementation name must be unique among all thorns.

There are three different access levels available for variables

| Name | Describe |
| ------------ | ------------- |
| Public | Can be ‘inherited’ by other implementations. |
| Protected | Can be shared with other implementations which declare themselves to be friends of this one. |
| Private | Can only be seen by this thorn. |

By default, all groups are private, to change this, an access specification of the form `public:` or `protected:`. 

Corresponding to the first two access levels there are two relationship statements that can be used to get variables from other implementations.

| Name | Describe |
| ------------ | ------------- |
| Inherits | Inheriting from another implementation makes all that implementation’s public variables available to your thorn. Inheritance is transitive (if A inherits from B, and B inherits from C, then A also implicitly inherits from C), but not commutative. |
| Friend | Being a friend of another implementation makes all that implementation’s protected variables available to your thorn. Friendship is associative, commutative and transitive (i.e. if A is a friend of B, and B is a friend of C, then A is implicitly a friend of C). |

**Include Files:**

___

The include file section has the form:

```
USES INCLUDE [SOURCE|HEADER]: <file_name>
INCLUDE[S] [SOURCE|HEADER]: <file_to_include> in <file_name>
```

The former is used when a thorn wishes to use an include file from another thorn. The latter indicates that this thorn adds the code in <file to include> to the include file <file name>. If the include file is described as SOURCE, the included code is only executed if the providing thorn is active. Both default to HEADER.

**Function Aliasing:**

___

If any aliased function is to be used or provided by the thorn, then the prototype must be declared with the form:

```
<return_type> FUNCTION <alias>(<arg1_type> <intent1> [ARRAY] <arg1>, ...)
```

- `<return_type>`

The `<return_type>` must be either void, CCTK_INT, CCTK_REAL, CCTK_COMPLEX, CCTK_POINTER, or CCTK_POINTER_TO_CONST. The keyword `SUBROUTINE` is equivalent to void FUNCTION. 

- `<alias>`

The name of the aliased function `<alias>` must contain at least one uppercase and one lowercase letter and follow the C standard for function names. 

- `<arg_type>`

The type of each argument, `<arg type>`, must be either CCTK_INT, CCTK_REAL, CCTK_COMPLEX, CCTK_POINTER, CCTK_POINTER_TO_CONST, or STRING. All string arguments must be the last arguments in the list. 

- `<intent*>`

The intent of each argument, `<intent*>`, must be either IN, OUT, or INOUT. An argument may only be modified if it is declared to have intent OUT or INOUT. 

- `[ARRAY]`

If the argument is an array then the prefix `ARRAY` must also be given.

- function pointer

If the argument `<arg*>` is a function pointer, then the argument itself (which will preceded by the return type) should be

```
CCTK_FPOINTER <function_arg1>(<arg1_type> <intent1> <arg1>, ...)
```

- aliased function

If an aliased function is to be required, then the block is required.

```
REQUIRES FUNCTION <alias>
```

If an aliased function is to be (optionally) used, then the block is required.

```
USES FUNCTION <alias>
```

**Variable Blocks:**

The thorn’s variables are collected into groups.

The thorn’s variables are defined by:

```
[<access>:]

<data_type> <group_name>[<number>] [TYPE=<group_type>] [DIM=<dim>] [TIMELEVELS=<num>] [SIZE=<size in each direction>] [DISTRIB=<distribution_type>] [GHOSTSIZE=<ghostsize>] [TAGS=<string>] ["<group_description>"]
{
<variable_name>[,] <variable_name>
} ["<group_description>"]
```

!!! note
    The options TYPE, DIM, etc., following <group name> must all appear on one line.

| Name | Describe |
| ------------ | ------------- |
| access | defines which thorns can use the following groups of variables. access can be either public, protected or private. |
| data_type | defines the data type of the variables in the group. Supported data types are CHAR, BYTE, INT, REAL, and COMPLEX. |
| group_name | must be an alphanumeric name (which may also contain underscores) which is unique across group and variable names within the scope of the thorn. A group name is compulsory. |
| [number] | if present, indicates that this is a vector group. |
| TYPE | designates the kind of variables held by the group. The choices are GF, ARRAY or SCALAR. This field is optional, with the default variable type being SCALAR. |
| DIM | defines the spatial dimension of the ARRAY or GF. The default value is DIM=3. |
| TIMELEVELS | defines the number of timelevels a group has if the group is of type ARRAY or GF, and can take any positive value. The default is one timelevel. |
| SIZE | defines the number grid-points an ARRAY has in each direction. This should be a comma-separated list of valid arithmetical expressions consisting of integers or integer-valued parameters. |
| DISTRIB | defines the processor decomposition of an ARRAY. DISTRIB=DEFAULT distributes SIZE grid-points across all processors. DISTRIB=CONSTANT implies that SIZE grid-points should be allocated on each processor. The default value is DISTRIB=DEFAULT. |
| GHOSTSIZE | defines number of ghost zones in each dimension of an ARRAY. It defaults to zero. |
| TAGS | defines an optional string which is used to create a set of key-value pairs associated with the group. |

An optional description of the group can be given on the last line. If the variable block is omitted, this description can be given at the end of the declaration line.

Cactus variables are used instead of local variables for a number of reasons:

- Cactus variables can be made visible to other thorns, allowing thorns to communicate and share data.
- Cactus variables can be distributed and communicated among processors, allowing parallel computation.
- A database of Cactus variables, and their attributes, is held by the flesh, and this information is used by thorns, for example, for obtaining a list of variables for checkpointing.
- Many Cactus APIs and tools can only be used with Cactus variables.
- Cactus provides features for error checking based on Cactus variables and their attributes.

Cactus variables are collected into groups. All variables in a group are of the same data type, and have the same attributes. Most Cactus operations act on a group as a whole. A group must be declared in its thorn’s `interface.ccl` file.

Groups can be either scalars, grid functions (GFs), or grid arrays.

| Name | Describe |
| ------------ | ------------- |
| SCALAR | This is just a single number, e.g. the total energy of some field. These variables aren’t communicated between processors—what would be the result of such communication? |
| GF | This is the most common group type. A GF is an array with a specific size, set at run time in the parameter file, which is distributed across processors. All GFs have the same size, and the same number of ghostzones. Groups of GFs can also specify a dimension, and number of timelevels. |
| ARRAY | This is a more general form of the GF. Each group of arrays can have a distinct size and number of ghostzones, in addition to dimension and number of timelevels. The drawback of using an array over a GF is that a lot of data about the array can only be determined by function calls, rather than the quicker methods available for GFs. |

## The param.ccl File

The param.ccl file is used to specify the parameters used to control an individual thorn, and to specify the values these parameters are allowed to take. If a parameter is not assigned in a parameter file, it is given its default value.

**Parameter Data Scoping Items:**

___

<access>:

The keyword `access` designates that all parameter object specification items, up to the next parameter data scoping item, are in the same protection or scoping class. access can take the values:

| Name | Describe |
| ------------ | ------------- |
| Global | These parameters are seen by all thorns. |
| Restricted | These parameters may be used by other implementations if they so desire. |
| Private | These are only seen by this thorn. |
| shares | in this case, an implementation name must follow the colon. It declares that all the parameters in the following scoping block are restricted variables from the specified implementation. |

**Parameter Object Specification Items**

___

```
[EXTENDS|USES] <parameter type> <parameter name> [<len>] "<parameter description>" [AS <alias>] [STEERABLE=<NEVER|ALWAYS|RECOVER>]
[ACCUMULATOR=<expression>] [ACCUMULATOR-BASE=<parameter name>]
{
<parameter values> 
} <default value>
```

where the options AS, STEERABLE, etc., following <parameter description>, must all appear in one line. Note that the beginning brace ({) must sit on a line by itself; the ending brace (}) must be at the beginning of a line followed by <default value> on that same line.

- A thorn can declare that it `EXTENDS` a parameter of another thorn. This allows it to declare additional acceptable values. By default, it is acceptable for two thorns to declare the same value as acceptable.
- If the thorn wants to simply use a parameter from another thorn, without declaring additional values, use `USES` instead.
- The `parameter name` must be unique within the scope of the thorn.
- The `default value` must match one of the ranges given in the `parameter type`
- `[len]` is an integer, if present, indicates that this is an array parameter of len values of the specified type.
- `alias` allows a parameter to appear under a different name in this thorn, other than its original name in another thorn. The name, as seen in the parameter file, is unchanged.
- `STEERABLE` specifies when a parameter value may be changed. By default, parameters may not be changed after the parameter file has been read, or on restarting from checkpoint. This option relaxes this restriction, specifying that the parameter may be changed at recovery time from a parameter file or at any time using the flesh routine `CCTK_ParameterSet`. The value `RECOVERY` is used in `checkpoint/recovery` situations, and indicates that the parameter may be altered until the value is read in from a recovery par file, but not after.
- `ACCUMULATOR` specifies that this is an accumulator parameter. Such parameters cannot be set directly, but are set by other parameters who specify this one as an `ACCUMULATOR-BASE`. The expression is a two-parameter arithmetical expression of x and y. Setting the parameter consists of evaluating this expression successively, with x being the current value of the parameter (at the first iteration this is the default value), and y the value of the setting parameter. This procedure is repeated, starting from the default value of the parameter, each time one of the setting parameters changes.
- `ACCUMULATOR-BASE` specifies the name of an `ACCUMULATOR` parameter which this parameter sets.

The <parameter values> depend on the <parameter type>, which may be one of the following:

**INT**

```
<range description> [::"<comment describing this range>"]
```

Here, a `<range description>` specifies a set of integers, and has one of the following forms:

- `*`: means any integer
- `<integer>`: means only `<integer>`
- `<lower bound>:<upper bound>`: means all integers in the range from <lower bound> to <upper bound> 
- `<lower bound>:<upper bound>:<positive step>`: means all integers in the range from <lower bound> to <upper bound> in steps of <positive step>

where <lower bound> has one of the forms

- `<empty field>`: means no lower limit
- `*`: means no lower limit
- `<integer>`: means a closed interval starting at <integer>
- `[<integer>`: also means a closed interval starting at <integer>
- `(<integer>`: means an open interval starting at <integer>

| Name | Describe |
| ------------ | ------------- |
| REAL | The range specification is the same as with integers, except that here, no `step` implies a continuum of values. Note that numeric constants should be expressed as in C (e.g. 1e-10). Note also that one cannot use the Cactus types such as `CCTK_REAL4` to specify the precision of the parameter; parameters always have the default precision. | 
| KEYWORD | Each entry in the list of acceptable values for a keyword has the form `<keyword value> :: "<description>"` Keyword values should be enclosed in double quotes. The double quotes are mandatory if the keyword contains spaces. |
| STRING | Allowed values for strings should be specified using regular expressions. To allow any string, the regular expression "" should be used. (An empty regular expression matches anything.) Regular expressions and string values should be enclosed in double quotes. The double quotes are mandatory if the regular expression or the string value is empty or contains spaces. |
| BOOLEAN | No `<parameter values>` should be specified for a boolean parameter. |

## The schedule.ccl File

Cactus contains a rule-based scheduling system, which determines which routines, from which thorns are run in which order.

A schedule configuration file consists of:

- Assignment statements to switch on storage for grid variables for the entire duration of program execution.
- Schedule blocks to schedule a subroutine from a thorn to be called at specific times during program execution in a given manner.
- Conditional statements for both assignment statements and schedule blocks to allow them to be processed depending on parameter values.

**Assignment Statements:**

___

These lines have the form:

```
STORAGE: <group>[timelevels]
```

The storage line includes the number of timelevels to activate storage for, this number can be from 1 up to the maximum number or timelevels for the group, as specified in the defining `interface.ccl` file. If the maximum number of timelevels is 1 (the default), this number may be omitted. Alternatively timelevels can be the name of a parameter accessible to the thorn. The parameter name is the same as used in C routines of the thorn, fully qualified parameter names of the form thorn::parameter are not allowed. In this case 0 (zero) timelevels can be requested, which is equivalent to the STORAGE statement being absent.

**Schedule Blocks:**

___

```
schedule [GROUP] <function name|group name> AT|IN [AS <alias>] [WHILE <variable>] [IF <variable>] [BEFORE|AFTER <function name>|(<function name>...)] 
{
LANG:  <FORTRAN|C>
OPTIONS:  [list of options]
TAGS:  [list of keyword=value definitions]
STORAGE:  [group list with timelevels]
READS:  [group list]
WRITES:  [group list]
TRIGGERS:  [group list]
SYNC:  [group list]
} "Description of function"
```

| Name | Describe |
| ------------ | ------------- |
| GROUP | Schedule a schedule group with the same options as a schedule function. The schedule group will be created if it doesn’t exist. |
| `<function name|group name>` | The name of a function or a schedule group to be scheduled. Function and schedule group names are case sensitive. |
| `AT|IN` | Each schedule item is scheduled either AT a particular scheduling bin, or IN a schedule group. |
| AS | Provides an alias for a function or schedule group which should be used for scheduling before, after or in. This can be used to provide thorn independence for other thorns scheduling functions, or schedule groups relative to this one. |
| WHILE | Executes a function or schedule group until the given variable (which must be a fully qualified integer grid scalar) has the value zero. |
| IF | Executes a function or schedule group only if the given variable (which must be a fully qualified integer grid scalar) has a non-zero value. |
| BEFORE/AFTER | Takes a function name, a function alias, a schedule group name, or a parenthesesenclosed white space-separated list of these. (Any names that are not provided by an active thorn are ignored.) Note that a single schedule block may have multiple BEFORE/AFTER clauses. |

Using the schedule.ccl files, thorn functions can be scheduled to run in the different timebins which are executed by the Cactus flesh.

| Name | Describe |
| ------------ | ------------- |
| CCTK_RECOVER_PARAMETERS | Used by thorns with relevant I/O methods as the point to read parameters when recovering from checkpoint files. Grid variables are not available in this timebin. Scheduling in this timebin is special. |
| **CCTK_STARTUP** | Run before any grids are constructed, this is the timebin, for example, where grid independent information (e.g. output methods, reduction operators) is registered. Note that since no grids are setup at this point, grid variables cannot be used in routines scheduled here. |
| CCTK_WRAGH | This timebin is executed when all parameters are known, but before the driver thorn constructs the grid. It should only be used to set up information that is needed by the driver. |
| **CCTK_PARAMCHECK** | This timebin is for thorns to check the validity of parameter combinations. This bin is also executed before the grid hierarchy is made, so that routines scheduled here only have access to the global grid size and the parameters. |
| CCTK_PREREGRIDINITIAL | This timebin is used in mesh refinement settings. It is ignored for unigrid runs. This bin is executed whenever the grid hierarchy is about to change during evolution; compare CCTK_PREREGRID. Routines that decide the new grid structure should be scheduled in this bin. |
| CCTK_POSTREGRIDINITIAL | This timebin is used in mesh refinement settings. It is ignored for unigrid runs. This bin is executed whenever the grid hierarchy or patch setup has changed during evolution; see CCTK_POSTREGRID. It is, e.g. necessary to re-apply the boundary conditions or recalculate the grid points’ coordinates after every changing the grid hierarchy. |
| **CCTK_BASEGRID** | This timebin is executed very early after a driver thorn constructs grid; this bin should only be used to set up coordinate systems on the newly created grids. |
| **CCTK_INITIAL** | This is the place to set up any required initial data. This timebin is not run when recovering from a checkpoint file. |
| **CCTK_POSTINITIAL** | This is the place to modify initial data, or to calculate data that depend on the initial data. This timebin is also not run when recovering from a checkpoint file. |
| CCTK_POSTRESTRICTINITIAL | This timebin is used only in mesh refinement settings. It is ignored for unigrid runs. This bin is executed after each restriction operation while initial data are set up; compare CCTK_POSTRESTRICT. It is, e.g. necessary to re-apply the boundary conditions after every restriction operation. |
| CCTK_POSTPOSTINITIAL | This is the place to modify initial data, or to calculate data that depend on the initial data. This timebin is executed after the recursive initialisation of finer grids if there is a mesh refinement hierarchy, and it is also not run when recovering from a checkpoint file. |
| CCTK_RECOVER_VARIABLES | Used by thorns with relevant I/O methods as the point to read in all the grid variables when recovering from checkpoint files. |
| CCTK_POST_RECOVER_VARIABLES | This timebin exists for scheduling any functions which need to modify grid variables after recovery. |
| CCTK_CPINITIAL | Used by thorns with relevant I/O methods as the point to checkpoint initial data if required. |
| CCTK_CHECKPOINT | Used by thorns with relevant I/O methods as the point to checkpoint data during the iterative loop when required. |
| CCTK_PREREGRID | This timebin is used in mesh refinement settings. It is ignored for unigrid runs. This bin is executed whenever the grid hierarchy is about to change during evolution; compare CCTK_PREREGRIDINITIAL. Routines that decide the new grid structure should be scheduled in this bin. |
| CCTK_POSTREGRID | This timebin is used in mesh refinement settings. It is ignored for unigrid runs. This bin is executed whenever the grid hierarchy or patch setup has changed during evolution; see CCTK_POSTREGRIDINITIAL. It is, e.g. necessary to re-apply the boundary conditions or recalculate the grid points’ coordinates after every changing the grid hierarchy. |
| **CCTK_PRESTEP** | The timebin for scheduling any routines which need to be executed before any routines in the main evolution step. This timebin exists for thorn writers convenience, the BEFORE, AFTER, etc., functionality of the schedule.ccl file should allow all functions to be scheduled in the main CCTK_EVOL timebin. |
| **CCTK_EVOL** | The timebin for the main evolution step. |
| CCTK_POSTRESTRICT | This timebin is used only in mesh refinement settings. It is ignored for unigrid runs. This bin is executed after each restriction operation during evolution; compare CCTK_POSTRESTRICTINITIAL. It is, e.g. necessary to re-apply the boundary conditions after every restriction operation. |
| **CCTK_POSTSTEP** | The timebin for scheduling any routines which need to be executed after all the routines in the main evolution step. This timebin exists for thorn writers convenience, the BEFORE, AFTER, etc., functionality of the schedule.ccl file should allow all functions to be scheduled in the main CCTK_EVOL timebin. |
| **CCTK_ANALYSIS** | The ANALYSIS timebin is special, in that it is closely coupled with output, and routines which are scheduled here are typically only executed if output of analysis variables is required. Routines which perform analysis should be independent of the main evolution loop (that is, it should not matter for the results of a simulation whether routines in this timebin are executed or not). |
| CCTK_TERMINATE | Called after the main iteration loop when Cactus terminates. Note that sometime, in this timebin, a driver thorn should be destroying the grid hierarchy and removing grid variables. |
| CCTK_SHUTDOWN | Cactus final shutdown routines, after the grid hierarchy has been destroyed. Grid variables are no longer available. |

It is possible to state that the routine must run BEFORE or AFTER another routine or set of routines. It is also possible to schedule the routine under an alias name by using AS `<alias name>`.

| Name | Describe |
| ------------ | ------------- |
| LANG | The code language for the function (either C or FORTRAN). No language should be specified for a schedule group. |
| OPTIONS | Schedule options are used for mesh refinement and multi-block simulations, and they determine “where” a routine executes. Possible options are: meta, meta_early, meta_late, global, global_early, global_late, level, singlemap, local, loop_meta, loop_global, loop_level, loop_singlemap, loop_local. |
| TAGS | Schedule tags. These tags must have the form `keyword=value`, and must be in a syntax accepted by `Util_TableCreateFromString`. |
| STORAGE | List of variable groups which should have storage switched on for the duration of the function or schedule group. Each group must specify how many timelevels to activate storage for, from 1 up to the maximum number for the group as specified in the defining interface.ccl file. If the maximum is 1 (the default) this number may be omitted. Alternatively timelevels can be the name of a parameter accessible to the thorn. The parameter name is the same as used in C routines of the thorn, fully qualified parameter names of the form `thorn::parameter` are not allowed. In this case 0 (zero) timelevels can be requested, which is equivalent to the STORAGE statement being absent. |
| READS | READS is used to declare which grid variables are read by the routine. This information is used e.g. to determine which variables need to be copied between host and device for OpenCL or CUDA kernel. This information can also be used to ensure that all variables that are read have previously been written by another routine. |
| WRITES | WRITES is used to declare which grid variables are written by the routine. This information is used e.g. to determine which variables need to be copied between host and device for OpenCL or CUDA kernel. This information can also be used to ensure that all variables that are read have previously been written by another routine. |
| TRIGGER | List of grid variables or groups to be used as triggers for causing an ANALYSIS function or group to be executed. Any schedule block for an analysis function or analysis group may contain a TRIGGER line. |
| SYNCHRONISE | List of groups to be synchronised, as soon as the function or schedule group is exited. |

Option names are case-insensitive. There can be several options given at the same time.

| Name | Describe |
| ------------ | ------------- |
| META | This routine will only be called once, even if several simulations are performed at the same time. This can be used, for example, to initialise external libraries, or to set up data structures that live in global variables. |
| META-EARLY | This option is identical to to META option with the exception that the routine will be called together with the routines on the first subgrid. |
| META-LATE | This option is identical to to META option with the exception that the routine will be called together with the routines on the last subgrid. |
| GLOBAL | This routine will only be called once on a grid hierarchy, not for all subgrids making up the hierarchy. This can be used, for example, for analysis routines which use global reduction or interpolation routines, rather than the local subgrid passed to them, and hence should only be called once. |
| GLOBAL-EARLY | This option is identical to to GLOBAL option with the exception that the routine will be called together with the routines on the first subgrid. |
| GLOBAL-LATE | This option is identical to to GLOBAL option with the exception that the routine will be called together with the routines on the last subgrid. |
| LEVEL | This routine will only be called once on any “level” of the grid hierarchy. That is, it will only be called once for any set of sub-grids which have the same `cctk_levfac` numbers. |
| SINGLEMAP | This routine will only be called once on any of the “patches” that form a “level” of the grid hierarchy. |
| LOCAL | This routine will be called on every “component”. |
| LOOP-META | Loop once. |
| LOOP-GLOBAL | Loop over all simulations. |
| LOOP-LEVEL | Loop over all “levels”. |
| LOOP-SINGLEMAP | Loop over all “patches”. |
| LOOP-LOCAL | Loop over all “components”. |

**Conditional Statements:**

___

Any schedule block or assignment statements can be optionally surrounded by conditional if-elseif-else constructs using the parameter data base. These can be nested, and have the general form:

```

if (<conditional-expression>) 
{
[<assignments>]
[<schedule blocks>] 
}
```

`<conditional-expression>` can be any valid C construct evaluating to a truth value. Such conditionals are evaluated only at program startup, and are used to pick between different static schedule options. For dynamic scheduling, the SCHEDULE WHILE construction should be used.

## The configuration.ccl File

A configuration.ccl file defines capabilities which a thorn either provides or requires, or may use if available. Unlike implementations, only one thorn providing a particular capability may be compiled into a configuration at one time. Thus, this mechanism may be used to, for example: provide access to external libraries; provide access to functions which other thorns must call, but are too complex for function aliasing; or to split a thorn into several thorns, all of which require some common (not aliased) functions.

A configuration options file can contain any number of the following sections:

```
PROVIDES <Capability> 
{
SCRIPT <Configuration script> [VERSION <Version String>]
LANG <Language>
[OPTIONS [<option>[,<option>]...]]
}
```

