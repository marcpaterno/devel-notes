## Developing Synergia on my laptop

    export SYNERGIA_SRC=$HOME/repos/synergia2
    cd ${SYNERGIA_SRC}/build
    source synergia-venv/bin/activate
    CC=$(which clang) CXX=$(which clang++) cmake -G Ninja -DCMAKE_INSTALL_PREFIX=~/tmp/synergia-install -DENABLE_KOKKOS_BACKEND=OpenMP -DCMAKE_BUILD_TYPE=Release -DPYTHON_EXECUTABLE=$(which python) -DKokkos_ENABLE_OPENMP=on -DKokkos_ENABLE_CUDA_LAMBDA=off -DENABLE_CUDA=off -DGSV=DOUBLE $SYNERGIA_SRC

    
## Setting up a new build from scratch

I assume we already have the repository cloned at `${HOME}/repos/synergia2`.

Note that we're using the simple pip installation of h5py rather than building our own.
    
    export SYNERGIA_SRC=$HOME/repos/synergia2
    cd ${SYNERGIA_SRC}/build
    python3 -m venv synergia-venv
    source synergia-venv/bin/activate
    python3 -m pip install --upgrade pip setuptools wheel
    python3 -m pip install cython matplotlib mpi4py numpy pyparsing pytest
    python3 -m pip install h5py
    
    
    