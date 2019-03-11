Required Software

``` bash
# Debian (stretch, buster)
su -c 'apt-get install build-essential pkg-config libopenmpi-dev openmpi-bin gfortran git subversion curl gnuplot gnuplot-x11'
# Ubuntu (16.04.2, 17.04, 17.10)
sudo apt-get install build-essential pkg-config mpich2? python libmpich2?-dev gfortran git subversion curl gnuplot gnuplot-x11
# Fedora (FC 25, 27)
su -c ' yum -y install mpich2 python pkg-config mpich2-devel gsl gsl-devel libjpeg-devel hdf5 hdf5-mpich-devel gcc gcc-c++ gcc-gfortran patch numactl-devel numactl hwloc subversion git openssl-devel lapack-static gnuplot'
# Mac OS (High Sierra), MacPorts
sudo port -N install pkgconfig gcc8 openmpi fftw-3 gsl jpeg zlib hdf5 +fortran +gfortran openssl
# Mac OS (High Sierra), Homebrew
brew install gnuplot pkg-config gcc fftw gsl hdf5 --with-fortran hwloc jpeg openssl pkg-config szip open-mpi
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

```
./GetComponents [options] [URL]
```

| Option | Discribe |
| ------------ | ------------- |
| −−help | brief help message |
| −−man | full documentation |
| −−verbose | print all system commands as they are executed |
| −−debug | print all commands to be executed and exit |
| −−anonymous | use anonymous checkout for all components |
| −−update | process all updates |
| −−root | override root directory |

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

For example

```
./simfactory/bin/sim create-run <Simulation name> --parfile /Users/yuliu/Desktop/work/yuliu/CactusPar/<name.par> --procs=120 --walltime=8:0:0
```

```
./simfactory/bin/sim create-run static_tov --parfile=par/static_tov_small_short.par --procs=2 --num-threads=1 --ppn-used=2  --walltime=8:0:0 | cat
```