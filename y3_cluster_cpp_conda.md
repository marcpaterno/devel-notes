# y3_cluster_cpp environment with Conda

## Context

I am building `y3_cluster_cpp` on WC in a conda environment in which I also
have the CUDA development tools needed for use of the `gpuintegration` code.

This starts from a conda environment that already has everything needed by
CosmoSIS, and adds the additional things needed by `y3_cluster_cpp`. The 
following will do this installation in one shot; this is generally quicker
than creating an environment incrementally.

    conda create -n cosmosis -c conda-forge astropy cffi cfitsio click cmake configparser cudatoolkit cxx-compiler cython emcee==2.2.1 fftw fitsio fortran-compiler future gsl jinja2 kombine matplotlib minuit2 mpi4py numba numpy nvcc_linux-64 openblas pybind11 pyccl pycparser pytest pyyaml sacc scikit-learn scipy six

## Working environment

I set up my working environment at under `/work1`. This setup needs to be done every time
I start work.

    # Go to the working directory
    cd /work1/numint/paterno/
    # Set up the environment
    export Y3GCC_DIR=$PWD
    export Y3_CLUSTER_CPP_DIR=${Y3GCC_DIR}/y3_cluster_cpp
    export Y3_CLUSTER_WORK_DIR=${Y3GCC_DIR}/y3_cluster_cpp
    export LD_LIBRARY_PATH=${Y3GCC_DIR}/cuba/lib:$LD_LIBRARY_PATH
 
## Building

The following is done to build the software. Most needs to be done only once.

    # Clone repositories
    git clone git@github.com:marcpaterno/cuba.git
    git clone git@bitbucket.org:mpaterno/y3_cluster_cpp.git
    git clone git@bitbucket.org:mpaterno/cubacpp.git

### Install `cluster_toolkit`

`cluster_toolkit` is not available from Conda, so I have to build it and install it myself.

    wget https://github.com/marcpaterno/cluster_toolkit/archive/master.tar.gz
    tar xf master.tar.gz
    cd cluster_toolkit-master/
    python3 setup.py install     # This will install into the environment
    cd ../
    rm -r cluster_toolkit-master/
    rm master.tar.gz

### Build CUBA

Note that `LD_LIBRARY_PATH` is set above to include the directory into which we
will be placing the CUBA dynamic library.

    cd ${Y3GCC_DIR}/cuba
    CC=$(which gcc) ./configure
    ./makesharedlib.sh
    mkdir include
    mkdir lib
    mv cuba.h include/
    mv libcuba.so lib/
    cd $Y3GCC_DIR

## Working in the environment




