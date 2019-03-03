**A thorn is the basic working module within Cactus.** All user supplied code goes into thorns, which are, by and large, independent of each other. Relationships among thorns are all based upon relationships among the implementations they provide.

Thorns are grouped into arrangements. This is a logical grouping of thorns which is purely for organisational purposes. The arrangements live in the `arrangements` directory of the main Cactus directory.

A thorn consists of a subdirectory of an arrangement containing four administrative files:

| Name | Describe |
| ------------ | ------------- |
| interface.ccl | This defines the implementation the thorn provides, and the variables the thorn needs, along with their visibility to other implementations. |
| param.ccl | This defines the parameters that are used to control the thorn, along with their visibility to other implementations. |
| schedule.ccl | This defines which functions are called from the thorn and when they are called. It also handles memory and communication assignment for grid variables. |
| configuration.ccl | This file is optional for a thorn. If it exists, it contains extra configuration options of this thorn. |

Thorns can also contain

- a subdirectory called src, which should hold source files and compilation instructions for the thorn 
- a subdirectory src/include for include files
- a README containing a brief description of the thorn
- a doc directory for documentation
- a par directory for example parameter files
- a test subdirectory may also be added, to hold the thorn’s test suite.

## General Syntax of CCL Files

**Cactus Configuration Language (CCL) files are text files used to define configuration information for a thorn.**

A Grammar File for each type of CCL file is provided in the `src/piraha/pegs` directory of the Cactus source tree.

CCL files are (mostly) case independent, and may contain comments introduced by the hash ‘#’ character, which indicates that the rest of the line is a comment. If the last non-blank character of a line in a CCL file is a backslash ‘\’, the following line is treated as a continuation of the current line.

see https://github.com/stevenrbrandt/piraha-peg for a description of formal syntax.

### The interface.ccl File

The `interface.ccl` file is used to declare

- the implementation provided by the thorn
- the variables provided by the thorn
- the include files provided by the thorn
- the functions provided by the thorn

#### Header Block

The implementation is declared by a single line at the top of the file

`implements: <name>`

There are three different access levels available for variables

| Name | Describe |
| ------------ | ------------- |
| Public | Can be ‘inherited’ by other implementations. |
| Protected | Can be shared with other implementations which declare themselves to be friends of this one. |
| Private | Can only be seen by this thorn. |

For example

```
# This gets all Public variables from implementation <name>, and all variables that <name> has in turn inherited.
Inherits: <name>
# This gets all Protected variables from implementation <name>.
Friend: <name>
```

#### Include Files

The include file section has the form:

```
USES INCLUDE [SOURCE|HEADER]: <file_name>
INCLUDE[S] [SOURCE|HEADER]: <file_to_include> in <file_name>
```

The former is used when a thorn wishes to use an include file from another thorn. The latter indicates that this thorn adds the code in <file to include> to the include file <file name>. If the include file is described as SOURCE, the included code is only executed if the providing thorn is active. Both default to HEADER.

#### Function Aliasing

If any aliased function is to be used or provided by the thorn, then the prototype must be declared with the form:

```
<return_type> FUNCTION <alias>(<arg1_type> <intent1> [ARRAY] <arg1>, ...)
```

The <return_type> must be either void, CCTK_INT, CCTK_REAL, CCTK_COMPLEX, CCTK_POINTER, or CCTK_POINTER_TO_CONST. The keyword SUBROUTINE is equivalent to void FUNCTION. The name of the aliased function <alias> must contain at least one uppercase and one lowercase letter and follow the C standard for function names. The type of each argument, <arg* type>, must be either CCTK_INT, CCTK_REAL, CCTK_COMPLEX, CCTK_POINTER, CCTK_POINTER_TO_CONST, or STRING. All string arguments must be the last arguments in the list. The intent of each argument, <intent*>, must be either IN, OUT, or INOUT. An argument may only be modified if it is declared to have intent OUT or INOUT. If the argument is an array then the prefix ARRAY must also be given.

If an aliased function is to be required, then the block is required.

```
REQUIRES FUNCTION <alias>
```

If an aliased function is to be (optionally) used, then the block is required.

```
USES FUNCTION <alias>
```

#### Variable Blocks

The thorn’s variables are collected into groups.

The thorn’s variables are defined by:

```
[<access>:]

<data_type> <group_name> [<number>] [TYPE=<group_type>] [DIM=<dim>] [TIMELEVELS=<num>] [SIZE=<size in each direction>] [DISTRIB=<distribution_type>] [GHOSTSIZE=<ghostsize>] [TAGS=<string>] ["<group_description>"]
{
<variable_name>
<variable_name>
} ["<group_description>"]
```

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
| SIZE | defines the number grid-points an ARRAY has in each direction. This should be a comma- separated list of valid arithmetical expressions consisting of integers or integer-valued parameters. |
| DISTRIB | defines the processor decomposition of an ARRAY. DISTRIB=DEFAULT distributes SIZE grid- points across all processors. DISTRIB=CONSTANT implies that SIZE grid-points should be allocated on each processor. The default value is DISTRIB=DEFAULT. |
| GHOSTSIZE | defines number of ghost zones in each dimension of an ARRAY. It defaults to zero. |
| TAGS | defines an optional string which is used to create a set of key-value pairs associated with the group. |

An optional description of the group can be given on the last line. If the variable block is omitted, this description can be given at the end of the declaration line.

### The param.ccl File

The param.ccl file is used to specify the parameters used to control an individual thorn, and to specify the values these parameters are allowed to take. If a parameter is not assigned in a parameter file, it is given its default value.

#### Parameter Data Scoping Items

<access>:

The keyword `access` designates that all parameter object specification items, up to the next parameter data scoping item, are in the same protection or scoping class. access can take the values:

| Name | Describe |
| ------------ | ------------- |
| Global | These parameters are seen by all thorns. |
| Restricted | These parameters may be used by other implementations if they so desire. |
| Private | These are only seen by this thorn. |
| shares | in this case, an implementation name must follow the colon. It declares that all the parameters in the following scoping block are restricted variables from the specified implementation. |

#### Parameter Object Specification Items

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
| BOOLEAN | No <parameter values> should be specified for a boolean parameter. |

### The schedule.ccl File

Cactus contains a rule-based scheduling system, which determines which routines, from which thorns are run in which order.

A usual simple specification for a schedule declaration is

```
schedule <function name> AT <schedule bin> 
{
LANG: <language>
[STORAGE: <group>[timelevels]
} "Description of function"
```

Each schedule item is scheduled either AT a particular scheduling bin, or IN a schedule group.

#### Assignment Statements

These lines have the form:

```
STORAGE: <group>[timelevels]
```

The storage line includes the number of timelevels to activate storage for, this number can be from 1 up to the maximum number or timelevels for the group, as specified in the defining `interface.ccl` file. If the maximum number of timelevels is 1 (the default), this number may be omitted. Alternatively timelevels can be the name of a parameter accessible to the thorn. The parameter name is the same as used in C routines of the thorn, fully qualified parameter names of the form thorn::parameter are not allowed. In this case 0 (zero) timelevels can be requested, which is equivalent to the STORAGE statement being absent.

#### Schedule Blocks

```
schedule [GROUP] <function name|group name> AT|IN <time> [AS <alias>] [WHILE <variable>] [IF <variable>] [BEFORE|AFTER <function name>|(<function name>)] 
{
[LANG: <language>]
[OPTIONS: <option>,<option >...]
[TAGS: <keyword=value>,<keyword=value >...]
[STORAGE: <group>[timelevels],<group>[timelevels]...]
[READS: <group>,<group>...]
[WRITES: <group>,<group>...]
[TRIGGER: <group>,<group>...]
[SYNCHRONISE: <group>,<group>...]
[OPTIONS: <option>,<option>...]
} "Description of function"
```

| Name | Describe |
| ------------ | ------------- |
| GROUP | Schedule a schedule group with the same options as a schedule function. The schedule group will be created if it doesn’t exist. |
| <function name|group name> | The name of a function or a schedule group to be scheduled. Function and schedule group names are case sensitive. |
| AT | Functions can be scheduled to run at the Cactus schedule bins, for example, CCTK_EVOL, and CCTK_STARTUP. The initial letters CCTK_ are optional. Grid variables cannot be used in the CCTK_STARTUP and CCTK_SHUTDOWN timebins. |
| IN | Schedules a function or schedule group to run in a schedule group, rather than in a Cactus timebin. |
| AS | Provides an alias for a function or schedule group which should be used for scheduling before, after or in. This can be used to provide thorn independence for other thorns scheduling functions, or schedule groups relative to this one. |
| WHILE | Executes a function or schedule group until the given variable (which must be a fully qualified integer grid scalar) has the value zero. |
| IF | Executes a function or schedule group only if the given variable (which must be a fully qualified integer grid scalar) has a non-zero value. |
| BEFORE/AFTER | Takes a function name, a function alias, a schedule group name, or a parenthesesenclosed whitespace-separated list of these. (Any names that are not provided by an active thorn are ignored.) Note that a single schedule block may have multiple BEFORE/AFTER clauses. |
| LANG | The code language for the function (either C or FORTRAN). No language should be specified for a schedule group. |
| OPTIONS | Schedule options are used for mesh refinement and multi-block simulations, and they determine “where” a routine executes. Possible options are: meta, meta_early, meta_late, global, global_early, global_late, level, singlemap, local, loop_meta, loop_global, loop_level, loop_singlemap, loop_local. |
| TAGS | Schedule tags. These tags must have the form keyword=value, and must be in a syntax accepted by Util_TableCreateFromString. |
| STORAGE | List of variable groups which should have storage switched on for the duration of the function or schedule group. Each group must specify how many timelevels to activate storage for, from 1 up to the maximum number for the group as specified in the defining interface.ccl file. If the maximum is 1 (the default) this number may be omitted. Alternatively timelevels can be the name of a parameter accessible to the thorn. The parameter name is the same as used in C routines of the thorn, fully qualified parameter names of the form thorn ::parameter are not allowed. In this case 0 (zero) timelevels can be requested, which is equivalent to the STORAGE statement being absent. |
| READS | READS is used to declare which grid variables are read by the routine. This information is used e.g. to determine which variables need to be copied between host and device for OpenCL or CUDA kernel. This information can also be used to ensure that all variables that are read have previously been written by another routine. |
| WRITES | WRITES is used to declare which grid variables are written by the routine. This information is used e.g. to determine which variables need to be copied between host and device for OpenCL or CUDA kernel. This information can also be used to ensure that all variables that are read have previously been written by another routine. |
| TRIGGER | List of grid variables or groups to be used as triggers for causing an ANALYSIS function or group to be executed. Any schedule block for an analysis function or analysis group may contain a TRIGGER line. |
| SYNCHRONISE | List of groups to be synchronised, as soon as the function or schedule group is exited. |
| OPTIONS | List of additional options (see below) for the scheduled function or group of functions. |

#### Conditional Statements

Any schedule block or assignment statements can be optionally surrounded by conditional if-elseif-else constructs using the parameter data base. These can be nested, and have the general form:

```

if (<conditional-expression>) 
{
[<assignments >]
[<schedule blocks>] 
}
```

conditional-expression can be any valid C construct evaluating to a truth value. Such conditionals are evaluated only at program startup, and are used to pick between different static schedule options. For dynamic scheduling, the SCHEDULE WHILE construction should be used.

### The configuration.ccl File

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