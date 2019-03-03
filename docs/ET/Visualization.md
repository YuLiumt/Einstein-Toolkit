## VisIt

## Opening the databases

Once the software is running, the button Open can be used to open files from the simulation. Make sure to select Open file as type: CarpetHDF5, otherwise VisIt may try to load the data using another format.

![vt-1](media/vt-1.png)

If you are using the Simulation Factory to launch and manage the run, the data will be output under the directory `<basedir>/<simulation name>/output-<nnnn>/<parfile name>`, where `<nnnn>` is the number of the restart. There are essentially two types of files that can be visualised with VisIt:

- Files from the thorn `CarpetIOHDF5`, with `.h5` extension, which will be read by the `CarpetHDF5` plugin in VisIt and represent the 1D, 2D, or 3D configuration of a specific field, on a number of constant-time spaces.
- Files from the thorn `QuasiLocalMeasures`, with `.vtk` extension, which are read natively by VisIt and represent the shape and properties of relevant 2D topological spheres, such as the apparent horizons, also at different times.
