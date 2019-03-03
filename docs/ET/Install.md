Required Software

``` bash
# On Debian/Ubuntu use this command:
sudo apt-get install -y subversion gcc git numactl libgsl-dev libpapi-dev python libhwloc-dev make libopenmpi-dev libhdf5-openmpi-dev libfftw3-dev libssl-dev liblapack-dev g++ curl gfortran patch pkg-config libhdf5-dev libjpeg-turbo?-dev
# On Fedora use this command:
sudo dnf install -y libjpeg-turbo-devel gcc git lapack-devel make subversion gcc-c++ which papi-devel python hwloc-devel openmpi-devel hdf5-openmpi-devel openssl-devel libtool-ltdl-devel numactl-devel gcc-gfortran findutils hdf5-devel fftw-devel patch gsl-devel pkgconfig
module load mpi/openmpi-x86_64
# On Centos use this command:
sudo yum install -y epel-release
sudo yum install -y libjpeg-turbo-devel gcc git lapack-devel make subversion gcc-c++ which papi-devel hwloc-devel openmpi-devel hdf5-openmpi-devel openssl-devel libtool-ltdl-devel numactl-devel gcc-gfortran hdf5-devel fftw-devel patch gsl-devel
```

## Prepare Tools

A script called GetComponents is used to fetch the components of the Einstein Toolkit. You may download and make it executable it as follows:

```
cd ~/
curl -O -L https://raw.githubusercontent.com/gridaphobe/CRL/ET_2018_09/GetComponents
chmod u+x GetComponents
```

Below checks out Cactus, the Einstein Toolkit thorns, the Simulation Factory and example parameter files into a directory named Cactus.
    
``` bash
./GetComponents --parallel https://bitbucket.org/einsteintoolkit/manifest/raw/ET_2018_09/einsteintoolkit.th
```

??? warning classes
     Some versions of svn might show problems with the parallel checkout. If you see errors like (svn: E155037: Previous operation has not finished), try without the --parallel option.

## Configuring SimFactory for your machine

Edit `simfactory/etc/defs.local.ini`.

SimFactory also contains general support for specific operating systems commonly used on workstations or laptops, including Mac OS, Ubuntu, Cent OS and Scientific Linux. To configure SimFactory for one of these, you need to find the suitable files in `simfactory/mdb/optionlists` and `simfactory/mdb/runscripts` and specify their names on the sim setup command line.

```
# for Debian
./simfactory/bin/sim setup-silent --optionlist=debian.cfg --runscript debian.sh
# for Ubuntu, Mint
./simfactory/bin/sim setup-silent --optionlist=ubuntu.cfg --runscript debian.sh
# for Fedora (you may have to log out and back in if you have just intalled mpich to make the module command work)
module load mpi
./simfactory/bin/sim setup-silent --optionlist=fedora.cfg --runscript debian.sh
# OSX+MacPorts
./simfactory/bin/sim setup-silent --optionlist=osx-macports.cfg --runscript osx-macports.run
# OSX+Homebrew
./simfactory/bin/sim setup-silent --optionlist=osx-homebrew.cfg --runscript generic-mpi.run
```

After this step is complete you will find your machine's default setup under `./repos/simfactory2/mdb/machines/<hostname>.ini`

??? note
    The main SimFactory binary is called “sim” and is located in `simfactory/bin`. You can execute SimFactory explicitly as `./simfactory/bin/sim`, but we recommend that you set up a shell alias in your shell startup file so that you can just use the command “sim”. For bash users this file is `.bashrc` on Linux. Add the following to the shell startup file:

    ```
    alias sim=./simfactory/bin/sim
    ```

## Building the Einstein Toolkit

Assuming that SimFactory has been successfully set up on your machine, you should be able to build the Einstein Toolkit with

``` bash
./simfactory/bin/sim build --mdbkey make 'make -j2' --thornlist ../einsteintoolkit.th | cat
```

## Running

```
./simfactory/bin/sim command [ options ]
```

SimFactory needs to know a name for the simulation as well as what parameter file to use. You can either specify the name on the command line and give the parameter file with the `--parfile` option.


