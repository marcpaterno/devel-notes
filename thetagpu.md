## Access to thetaGPU

We first get onto a *theta* login node.

    ssh theta.alcf.anl.gov    # the MobilePASS+ application gives the password

Then load the module that makes all Cobalt commands
use their *thetaGPU* versions.
We also make `tmux` available,
because `tmux` rocks:

    module load cobalt/cobalt-gpu
    module load tmux

To *launch batch jobs* this should be sufficient.
But to *build* software,
we need to build on a *thetaGPU* compute node.
Cross-compiling for *thetaGPU* from the *theta* login node is possible at this time.

    # If you are working on a project other than gccy3, then use the name of that
    # project as the argument for -A in the qsub command
    qsub -I -q single-gpu -n 1 -t 60 -A gccy3 --attrs=pubnet

This gives access to a single GPU.

## Load modules

We rely on system (Lmod) modules for system-related items that are very specific to thetaGPU.
Upon login we have:

    $ module list
    Currently Loaded Modules:
    1) openmpi/openmpi-4.0.5   2) Core/StdEnv

The `Core/StdEnv` module seems only to cause the loading of the `openmpi` module.
The `openmpi` module provides the specially-built MPI *required* for use on *thetaGPU*.
The we get Python, and many Python modules, from a `conda` module:
    
    module load conda/2021-11-30   # this is the newest at the time of writing
 
Loading the `conda` module does not activate the `base` environment.
We are creating our own environment, and so do not need to use `base`.
However, it *does* reload a different *version* of the `openmpi` module:

    $ module list
    Currently Loaded Modules:
    1) Core/StdEnv   2) openmpi/openmpi-4.1.1_ucx-1.11.2_gcc-9.3.0   3) conda/2021-11-30

We do not need the `nvhpc` module,
which seems only to define things like `CXX` to point to the `nvc++`,
and to put *cuda* `include` directories into `$CPATH` and library directories onto `LD_LIBRARY_PATH`.

## Create the `conda` environment

Note the critical feature of this step
is that we do not use `conda` to install
anything that is related to MPI,
nor CUDA,
nor do we use `conda` to install any compilers.

    conda create -p /grand/gccy3/cosmosis-4 -c conda-forge libgcc-ng=9.3.0 libgfortran5=9.3.0 astropy cffi cfitsio click configparser cython emcee==2.2.1 fftw fitsio future gsl jinja2 kombine matplotlib minuit2 numba numpy openblas pycparser pytest pyyaml scipy six pytest-runner ripgrep

Note that we have specified versions of `libgcc-ng` and `libgfortran5` so that we end up with are getting builds that are based on our system compiler suite, which is GCC 9.3.0.
The `ripgrep` package is not needed to build any of our software
but is very useful for searching through code.

## Installing the remaining Python modules

We must first activate the newly-created `conda` environment,
and then build `mpipy` from downloaded source
in a manner that will connect to the system's OpenMPI version.

## Setting up to use the environment

    module load conda/2021-11-30
    conda activate /grand/gccy3/cosmosis-4

  To see which (if any) `conda` environment you have active,
  use:

      echo $CONDA_DEFAULT_ENV

  If this is not defined, then you have no active environment;
  otherwise, this returns the *name* of a named environment,
  or the *path to* an unnamed environment.

## Install `cosmosis` and its requirements

You should have the `/grand/gccy3/cosmosis-4` environment active before starting.
First we need to clone the CosmoSIS and CSL repositories.
I recommend creating a directory into which these directories,
and other items we will need,
are to be cloned.
I refer to this directory through the environment variable `TOPDIR`.
My installation on *thetaGPU* puts this in `/grand/gccy3/topdir`.
On *thetaGPU*,
do not put this directory on the *home* filesystem.

*N.B.*: this installation uses a version of CosmoSIS and the CSL from the official repository.
However, it is not yet using an official release of CosmoSIS or the CSL.

*N.B.*: CosmoSIS can use, but does not require, the *minuit2* library.
I have not bothered to install *minuit2* because we do not make use of it.

    cd /grand/gccy3/topdir   # Replace this with your own installation if desired
    export TOPDIR=$PWD
    git clone -b develop https://bitbucket.org/joezuntz/cosmosis
    cd cosmosis
    git clone -b develop https://bitbucket.org/joezuntz/cosmosis-standard-library
    export COSMOSIS_OMP=1
    export COSMOSIS_SRC_DIR=$PWD
    export GSL_INC=$CONDA_PREFIX/include
    export GSL_LIB=$CONDA_PREFIX/lib
    export CFITSIO_INC=$CONDA_PREFIX/include
    export CFITSIO_LIB=$CONDA_PREFIX/lib
    export FFTW_INCLUDE_DIR=$CONDA_PREFIX/include
    export FFTW_LIBRARY=$CONDA_PREFIX/lib
    export LAPACK_LIB=$CONDA_PREFIX/lib
    export LAPACK_LINK="-L${LAPACK_LIB} -llapack -lblas"
    export COSMOSIS_LIB_PATH=${COSMOSIS_SRC_DIR}/cosmosis/datablock/:${COSMOSIS_SRC_DIR}/cosmosis-standard-library/likelihood/planck/plc-1.0/lib/:${COSMOSIS_SRC_DIR}/cosmosis-standard-library/likelihood/planck2015/plc-2.0/lib/:${COSMOSIS_SRC_DIR}/cosmosis-standard-library/likelihood/planck2015/plc-2.0/builddir:${CFITSIO_LIB}
    export LD_LIBRARY_PATH=${COSMOSIS_LIB_PATH}:${LD_LIBRARY_PATH}
    make

Next, we need `mpi4py1`.
We do not install this with `conda` because doing so causes `conda` to install its own MPI implementation.
Instead, in the active `conda` environment and with the right MPI module loaded, we use `pip install`,
which will install `mpi4py` into the active `conda` environment.

    pip install mpi4py
 

We do not do a parallel make
because not all of the components included in the CSL
support parallel make.

## Setting up an already-built CosmoSIS

If you are working on `y3_cluster_cpp`
but do *not* need to modify code in CosmoSIS itself,
then I recommend sharing my build of CosmoSIS and its dependencies.
To do so,
use the commands below to set up the environment:

    module load conda/2021-11-30
    module load cmake-3.20.3-gcc-9.3.0-57eqw4f
    conda activate /grand/gccy3/cosmosis-4
    export TOPDIR=/grand/gccy3/topdir
    export COSMOSIS_OMP=1
    export COSMOSIS_SRC_DIR=${TOPDIR}/cosmosis
    export GSL_INC=$CONDA_PREFIX/include
    export GSL_LIB=$CONDA_PREFIX/lib
    export CFITSIO_INC=$CONDA_PREFIX/include
    export CFITSIO_LIB=$CONDA_PREFIX/lib
    export FFTW_INCLUDE_DIR=$CONDA_PREFIX/include
    export FFTW_LIBRARY=$CONDA_PREFIX/lib
    export LAPACK_LIB=$CONDA_PREFIX/lib
    export LAPACK_LINK="-L${LAPACK_LIB} -llapack -lblas"
    export COSMOSIS_LIB_PATH=${COSMOSIS_SRC_DIR}/cosmosis/datablock/:${COSMOSIS_SRC_DIR}/cosmosis-standard-library/likelihood/planck/plc-1.0/lib/:${COSMOSIS_SRC_DIR}/cosmosis-standard-library/likelihood/planck2015/plc-2.0/lib/:${COSMOSIS_SRC_DIR}/cosmosis-standard-library/likelihood/planck2015/plc-2.0/builddir:${CFITSIO_LIB}
    export LD_LIBRARY_PATH=${COSMOSIS_LIB_PATH}:${TOPDIR}/cuba/lib:${LD_LIBRARY_PATH}
    export PYTHONPATH=${COSMOSIS_SRC_DIR} # There is no previous value of PYTHONPATH
    export PATH=${COSMOSIS_SRC_DIR}/bin:${PATH}
    export PAGANI_DIR=${TOPDIR}/gpuintegration

## Obtaining the other requirements for `y3_cluster_cpp`

If you are developing code in `y3_cluster_cpp` but are not modifying the underlying requirements
then in addition to sharing the CosmoSIS installation,
you can share the installation of the additional requirements needed by our analysis code.

### Getting `cluster_toolkit`

Unfortunately `cluster_toolkit` is available neither through `conda` nor `pip`.
So we download and build source ourselves.
Note that the commands below will build `cluster_toolkit` and install it into the currently-active `conda` environment.
If you are sharing the `/grand/gccy3/cosmosis-4` environment, and you do not plan to modify `cluster_toolkit`,
then do not attempt to repeat this installation.
If you *do* plan to modify `cluster_toolkit`, and so need your own installation,
you Will either need to create your own `conda` environment, so that you can install it there,
or to do some sort of `--user` install.
I do not have instructions for doing that.

    mkdir -p ${TOPDIR}/tmp
    cd ${TOPDIR}/tmp
    wget https://github.com/marcpaterno/cluster_toolkit/archive/master.tar.gz
    tar xf master.tar.gz
    cd cluster_toolkit-master/
    python setup.py install     # This will install into the environment

### Getting `cuba`

Note that we use my copy of CUBA, not the original.
The difference is that my repository has the script that builds a shared library,
while the original builds only a static library.

    cd ${TOPDIR} 
    git clone https://github.com/marcpaterno/cuba.git
    cd cuba
    CC=$(which gcc) ./configure
    ./makesharedlib.sh
    mv cuba.h include/
    mv libcuba.so lib/

### Getting `cubacpp`

There is not code to build for `cubacpp`,
unless you want to run tests.
So generally you only need to clone the repository.

    cd ${TOPDIR}
    git clone http://bucket.org/mpaterno/cubacpp.git

### Getting `gpuintegration`

Unless you are doing development of `gpuintegration`,
or you wish to use the Kokkos algorithms,
there is nothing to build.
The `y3_cluster_cpp` analysis code is currently using
the CUDA/C++ implementation of the algorithms,
and does not yet support the Kokkos versions.
So generally you only need to clone the repository.

    cd ${TOPDIR}
    git clone http://github.com/marcpaterno/gpuintegration.git

### If you want to build `cudaPagani`

You only need to bother with this if you plan to modify the CUDA/C++ code
in `gpuintegration` (in particular, in `cudaPagani`).

    cd ${PAGANI_DIR}
    mkdir build
    # Change the CMAKE_BUILD_TYPE if you want a debug build for testing.
    # Note you can also have side-by-side builds; just put them into different
    # subdirectories.
    cmake -DPAGANI_DIR=$PAGANI_DIR -DCMAKE_BUILD_TYPE=Release -DPAGANI_TARGET_ARCH=80-real ..
    make -j 8 # adjust the parallelism as you like
    ctest     # all the tests should pass

## Getting `y3_cluster_cpp`

It is assumed that everyone wants to do development of `y3_cluster_cpp` itself.

    cd ${TOPDIR}
    git clone http://bitbucket.org/mpaterno/y3_cluster_cpp.git
    cd y3_cluster_cpp
    cmake -DCMAKE_MODULE_PATH="${Y3_CLUSTER_CPP_DIR}/cmake;${Y3GCC_DIR}/cubacpp/cmake/modules" -DCUBACPP_DIR=${Y3GCC_DIR}/cubacpp -DCUBA_DIR=${Y3GCC_DIR}/cuba  -DCMAKE_BUILD_TYPE=Release  ${PWD}

