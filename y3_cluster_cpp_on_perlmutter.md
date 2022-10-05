# y3_cluster_cpp environment on Perlmutter

## Related

* [Y3 cluster cpp on WC](gpu-cosmosis-on-wc.md)

## Context

I am building `y3_cluster_cpp` on Perlmutter.
This build is *similar* to the one we recommend for others' use, but it contains my own copies of some of the basic repositories.
This is so that I can do development of `cuba_cpp` and `gpuintegration` as well as `y3_cluster_cpp`.
See also the [Y3 cluster code instructions for Perlmutter](https://bitbucket.org/mpaterno/y3_cluster_cpp/wiki/build_on_perlmutter).

## Setup for regular work

This assumes that a build is already done.

    export SHARED_TOP_DIR=/global/common/software/des/annis
    export TOP_DIR=/global/common/software/des/$(id -un)
    export MY_TOP_DIR=$TOP_DIR
    export COSMOSIS_REPO_DIR=${SHARED_TOP_DIR}/cosmosis
    export CSL_DIR=${SHARED_TOP_DIR}/cosmosis-standard-library

    export INTEGRATION_TOOLS_DIR=${MY_TOP_DIR}/y3_pipe_under
    export Y3PIPE_DIR=${MY_TOP_DIR}/y3_cluster_cpp
    export Y3_CLUSTER_WORK_DIR=${Y3PIPE_DIR}/release-build
    export Y3_CLUSTER_CPP_DIR=${Y3PIPE_DIR}
    export COSMOSIS_STANDARD_LIBRARY=${CSL_DIR}

    export CUBA_DIR=${INTEGRATION_TOOLS_DIR}/cuba
    export CUBA_CPP_DIR=$INTEGRATION_TOOLS_DIR/cubacpp
    export GPU_INT_DIR=${INTEGRATION_TOOLS_DIR}/gpuintegration

    # We set OMP_NUM_THREADS to avoid oversubscribing the CPU cores.
    # This value has not been carefully tuned.
    export OMP_NUM_THREADS=4

    # Now set up CosmoSIS
    source ${COSMOSIS_REPO_DIR}/setup-cosmosis-nersc /global/common/software/des/common/Conda_Envs/cosmosis-global

    # Move to the working directory for code
    cd $Y3_CLUSTER_CPP_DIR


