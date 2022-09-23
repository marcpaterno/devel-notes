# Conda, CosmoSIS and CUDA on wc.fnal.gov, oh my!

These are instructions for getting my environment set up on WC for the development
of GPU integration routines as used in CosmoSIS.

Follow the instructions here for establishing the proper environment on the head
node (wc.fnal.gov) *before* allocating a node for interactive use or for running
tests in MPI mode.

See the final section to learn how the working environment was actually created,
including the cloning of repositories
(both CSL and `y3_cluster_cpp`)
and the creation of the `conda` environment containing CosmoSIS.
Note that we're using compilers from the `conda` environment here.

## The environment on the head node

    export MY_CUDA_VERSION="11.6.0"
    module load cuda11/${MY_CUDA_VERSION}
    export CURRENT_PROJECT=numint
    export WORKHOME=/work1/${CURRENT_PROJECT}/$(id -un)
    export CUDA_HOME=/srv/software/cuda-toolkits/${MY_CUDA_VERSION}
    export PAGANI_DIR=/work1/numint/$(id -un)/gpuintegration
    export Y3GCC_DIR=/work1/numint/$(id -un)
    export Y3_CLUSTER_CPP_DIR=${Y3GCC_DIR}/y3_cluster_cpp
    export Y3_CLUSTER_WORK_DIR=${Y3_CLUSTER_CPP_DIR}/release-build  # This assumes an out-of-source build in this directory
    source ${WORKHOME}/setup-mamba-conda   # Make conda available
    
    source config/setup-conda-cosmosis cosmosis         # This will activate the correct conda environment

The last command will result in a message:

    Version of installed CUDA didn't match package

I have not yet found any failure as a result.

The conda environment `tools` on the head node provides some useful tools, e.g. `tmux`, and modern `vim` and `git`; also `dos2unix`.
I find it useful to have one login window on the head node with the `tools` environment active, and a second in which I have a
login to a GPU node to compile and run tests. I use the window on the head node for editing and `git` interaction.

To get the `tools` environment active, you have to deactivate the previous environment:

    conda deactivate
    conda activate tools

## Getting a worker node

Follow the instructions for [developing on the Wilson Cluster](developing-on-wc.md) to
obtain an allocated node, to get an interactive shell on a worker node, or to run a
test job.

After getting an interactive shell on a worker node, the current conda environment will *not*
be the one established on the head node, but `cosmosis` will be set up. Running `cosmosis` in
this condition causes the failure to find Python packages installed into the `cosmosis` environment.
To activate the right conda environment, use:

    conda activate cosmosis

To see what is your currently-active conda environment, use:

    conda info -e

## Environment setup

The environment variables needed are set up on the head node, above.

### For PAGANI

The environment variable `$PAGANI_DIR` is set above to the directory containing `gpuintegration`.
Remember to use the *editing* window (on the host) to modify code, and the *compiling/running* window
(on the GPU node) when you want to make changes to PAGANI.

For testing/adding to the PAGANI code, on the worker node,
`cd` to the working directory:

    cd $PAGANI_DIR/cudaPagani
 
Builds are done in the `build` subdirectory.

### For `y3_cluster_cpp` code using PAGANI

*NB*: the instructions below establish a build of `y3_cluster_cpp` that uses your own clone
of the `gpuintegration` repository (and thus allows you to make changes in the PAGANI code),
but which shares the use of `cosmosis`, `cuba` and `cubacpp` (because our work on `y3_cluster_cpp`
does not usually involve changing any of them).m


    # Set up a Release (optimized) build of y1_cluster_cpp
    cmake -DUSE_CUDA=On -DY3GCC_TARGET_ARCH="70-real" -DPAGANI_DIR=${PAGANI_DIR} -DCMAKE_MODULE_PATH="${Y3_CLUSTER_CPP_DIR}/cmake;/work1/numint/paterno/cubacpp/cmake/modules" -DCUBACPP_DIR=/work1/numint/paterno/cubacpp -DCUBA_DIR=/work1/numint/paterno/cuba -DCMAKE_BUILD_TYPE=Release -G Ninja  ..


## Appendix: building the environment from scratch

I am using `mamba` to provide `conda` because the solver is faster.

** N.B.: This environment is currently not working. **

    # create the conda environment
    export CURRENT_PROJECT=numint
    export WORKHOME=/work1/${CURRENT_PROJECT}/$(id -un)
    export PAGANI_DIR=/work1/numint/$(id -un)/gpuintegration
    export Y3GCC_DIR=/work1/numint/$(id -un)
    export Y3_CLUSTER_CPP_DIR=${Y3GCC_DIR}/y3_cluster_cpp
    export Y3_CLUSTER_WORK_DIR=${Y3_CLUSTER_CPP_DIR}/release-build  # This assumes an out-of-source build in this directory
    source ${WORKHOME}/setup-mamba-conda   # Make conda available
    # Creating the environment is a slow step. We aren't nailing down specific
    # versions of things, so doing this again will yield a slightly different
    # environment. We hope not to be so sensitive to versions that this will
    # matter.
    mamba create -n for_y3_cluster_cpp black cffi clang-format cmake cosmosis cosmosis-build-standard-library cudatoolkit gdb git kombine ninja nvcc_linux-64 openblas ripgrep tmux vim
