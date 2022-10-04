# Developing Synergia on the Wilson Cluster

The Wilson Cluster (https://computing.fnal.gov/wilsoncluster/) has a head node
(`wc.fnal.gov`)
and a variety of configurations of worker nodes. It uses the SLURM batch system
to control running of jobs. All access to the worker nodes, including interactive
access, is obtained using SLURM commands.

When working on Synergia I try to follow Eric's work plan:

1. Build code on the head node.
2. Launch tests from the head node.
    * But tests run on execution nodes
    * You have to run on the architecture for which you built.

## My suggested working setup

First `kinit` and the `ssh wc.fnal.gov`.

On WC:

    export CURRENT_PROJECT=accelsim
    export WORKHOME=/work1/${CURRENT_PROJECT}/$(id -un)
    cd ${WORKHOME}
    source workdir/synergia2-provisioning/devel-3-on-wc/devel3-v100/05_setup_environment.sh

This defines:

1. `SYNSRC`: the location of the source code repository.
2. `SYNINSTALL`: the location into which the build code will be installed.
3. `SYNBLD`: the directory in which to run `cmake` and to build the code.
4. `PY_EXE`: the full path to the Python executable of choice.

Building the code can be done on the head node `wc.fnal.gov`.

    cd ${SYNBLD}
    CC=gcc CXX=g++ cmake -G Ninja -DCMAKE_INSTALL_PREFIX=${SYNINSTALL} -DENABLE_KOKKOS_BACKEND=CUDA -DCMAKE_BUILD_TYPE=Release -DPYTHON_EXECUTABLE=${PY_EXE} -DKokkos_ENABLE_OPENMP=on -DKokkos_ENABLE_CUDA_LAMBDA=on -DENABLE_CUDA=on -DGSV=DOUBLE -DKokkos_ARCH_VOLTA70=on -DCMAKE_CXX_FLAGS="-arch=sm_70" -DCMAKE_CXX_COMPILER=${SYNSRC}/src/synergia/utils/kokkos/bin/nvcc_wrapper ${SYNSRC}

The tests must be run on compute nodes.
The build above generates code for the V100 GPUs, so one must run on those machines.

    HOME=${WORKHOME} srun -A accelsim -N 1 -n 1 --cpus-per-task=16 -p gpu_gce --constraint=v100  --gres=gpu:1 --mpi=pmix ctest


