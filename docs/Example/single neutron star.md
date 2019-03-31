This simulation shows how to evolve a stable, single neutron star.

What can be seen is the initial perturbation (due to numerical errors) ringing down (look at the density maximum), and later numerical errors governing the solution. Try higher resolutions to decrease this error.

## Parameter File

### Flesh parameters

时间演化到 800 时结束

```
Cactus::terminate           = "time"
Cactus::cctk_final_time     = 800
```

```
# Termination Trigger
ActiveThorns = "TerminationTrigger"

# Walltime in HOURS allocated for this job
TerminationTrigger::max_walltime = 24          # hours

# When to trigger termination in MINUTES
TerminationTrigger::on_remaining_walltime = 15 # minutes

# Check termination file every n timesteps
TerminationTrigger::check_file_every = 512

# Termination file name
TerminationTrigger::termination_file = "TerminationTrigger.txt"

# Use termination file
TerminationTrigger::termination_from_file   = "yes"

# Create an empty termination file at startup
TerminationTrigger::create_termination_file = "yes"
```

### Time integration

```
time::dtfac = 0.25

MoL::ODE_Method             = "rk4"

# Number of intermediate steps taken by the ODE method
MoL::MoL_Intermediate_Steps = 4

# Number of scratch levels required by the ODE metho
MoL::MoL_Num_Scratch_Levels = 1
```

### grid parameters

```
# grid parameters
Carpet::domain_from_coordbase = "yes"
CartGrid3D::type         = "coordbase"

# Use the full domain
CartGrid3D::domain       = "full"

# Don't place grid points on the coordinate origin
CartGrid3D::avoid_origin = "no"

CoordBase::xmin =  0.0
CoordBase::ymin =  0.0
CoordBase::zmin =  0.0
CoordBase::xmax = 48.0
CoordBase::ymax = 48.0
CoordBase::zmax = 48.0

# Change these parameters to change resolution. The max settings above have to be multiples of these. 'dx' is the size of one cell in x-direction. Making this smaller means using higher resolution, because more points will be used to cover the same space.
CoordBase::dx   =   4.0
CoordBase::dy   =   4.0
CoordBase::dz   =   4.0

CoordBase::boundary_size_x_lower        = 3
CoordBase::boundary_size_y_lower        = 3
CoordBase::boundary_size_z_lower        = 3
CoordBase::boundary_size_x_upper        = 3
CoordBase::boundary_size_y_upper        = 3
CoordBase::boundary_size_z_upper        = 3
CoordBase::boundary_shiftout_x_lower    = 1
CoordBase::boundary_shiftout_y_lower    = 1
CoordBase::boundary_shiftout_z_lower    = 1
CoordBase::boundary_shiftout_x_upper    = 0
CoordBase::boundary_shiftout_y_upper    = 0
CoordBase::boundary_shiftout_z_upper    = 0
```

### Boundary

```
ActiveThorns = "ReflectionSymmetry"

# Reflection symmetry at the lower x boundary
ReflectionSymmetry::reflection_x = "yes"
ReflectionSymmetry::reflection_y = "yes"
ReflectionSymmetry::reflection_z = "yes"
# Stagger about the origin on the lower x boundary?
ReflectionSymmetry::avoid_origin_x = "no"
ReflectionSymmetry::avoid_origin_y = "no"
ReflectionSymmetry::avoid_origin_z = "no"
```

### Mesh refinement

```
Carpet::enable_all_storage       = no
Carpet::use_buffer_zones         = "yes"

Carpet::poison_new_timelevels    = "yes"
Carpet::check_for_poison         = "no"

Carpet::init_3_timelevels        = no
Carpet::init_fill_timelevels     = "yes"

CarpetLib::poison_new_memory = "yes"
CarpetLib::poison_value      = 114

# system specific Carpet paramters
Carpet::max_refinement_levels    = 10
driver::ghost_size               = 3
Carpet::prolongation_order_space = 3
Carpet::prolongation_order_time  = 2

# regrid during initial data calculation only
CarpetRegrid2::regrid_every = 0

# Number of refinement centres
CarpetRegrid2::num_centres  =   1

# Number of refinement levels for this centre
CarpetRegrid2::num_levels_1 =   3
CarpetRegrid2::radius_1[1]  = 24.0
CarpetRegrid2::radius_1[2]  = 12.0
```

### Hydro paramters

```
ActiveThorns = "HydroBase EOS_Omni GRHydro"

HydroBase::timelevels            = 3

HydroBase::evolution_method      = "GRHydro"

# Which Riemann solver to use
GRHydro::riemann_solver            = "HLLE"

# Type of Equation of State P = P(rho, eps)
GRHydro::GRHydro_eos_type          = "General"
# Name for the Equation of State
GRHydro::GRHydro_eos_table         = "Ideal_Fluid"
#  PPM reconstruction method to use
GRHydro::recon_method              = "ppm"
# Width of the stencil
GRHydro::GRHydro_stencil            = 3
# static boundary condition to use
GRHydro::bound                     = "none"
# A minimum rho below which evolution is turned off 
GRHydro::rho_abs_min               = 1.e-10
# A point is set to atmosphere in the Con2Prim's if its rho < GRHydro_rho_min *(1+GRHydro_atmo_tolerance). This avoids occasional spurious oscillations in carpet buffer zones lying in the atmosphere (because prolongation happens on conserved variables)
GRHydro::GRHydro_atmo_tolerance    = 1.e-3
# Order of spatial differencing of the source terms
GRHydro::sources_spatial_order     = 4
```

```
# storage and coupling
TmunuBase::stress_energy_storage = yes
TmunuBase::stress_energy_at_RHS  = yes
TmunuBase::timelevels            =  1
TmunuBase::prolongation_type     = none
```

### evolution parameters

```
ActiveThorns = "GenericFD NewRad"
ActiveThorns = "ML_BSSN ML_BSSN_Helper"

ADMBase::evolution_method        = "ML_BSSN"
ADMBase::lapse_evolution_method  = "ML_BSSN"
ADMBase::shift_evolution_method  = "ML_BSSN"
ADMBase::dtlapse_evolution_method= "ML_BSSN"
ADMBase::dtshift_evolution_method= "ML_BSSN"

ML_BSSN::timelevels = 3

# d/dt alpha = - f alpha^n K  (harmonic: n=2, 1+log: n=1)
ML_BSSN::harmonicN           = 1      # 1+log
# d/dt alpha = - f alpha^n K   (harmonic: f=1, 1+log: f=2)
ML_BSSN::harmonicF           = 2.0    # 1+log

# Evolve time derivative of shift B^i?
ML_BSSN::ShiftBCoeff         = 1
ML_BSSN::ShiftGammaCoeff     = 0.75
ML_BSSN::BetaDriver          = 2.66
ML_BSSN::LapseAdvectionCoeff = 0.0
ML_BSSN::ShiftAdvectionCoeff = 0.0

ML_BSSN::my_initial_boundary_condition = "extrapolate-gammas"
ML_BSSN::my_rhs_boundary_condition     = "NewRad"
```

### init parameters

```
InitBase::initial_data_setup_method = "init_some_levels"

# Use TOV as initial data
ActiveThorns = "TOVSolver"

HydroBase::initial_hydro         = "tov"
ADMBase::initial_data            = "tov"
ADMBase::initial_lapse           = "tov"
ADMBase::initial_shift           = "tov"
ADMBase::initial_dtlapse         = "zero"
ADMBase::initial_dtshift         = "zero"

# Parameters for initial star
TOVSolver::TOV_Rho_Central[0] = 1.28e-3
TOVSolver::TOV_Gamma          = 2
TOVSolver::TOV_K              = 100

# Set equation of state for evolution
EOS_Omni::poly_gamma                   = 2
EOS_Omni::poly_k                       = 100
EOS_Omni::gl_gamma                     = 2
EOS_Omni::gl_k                         = 100
```

