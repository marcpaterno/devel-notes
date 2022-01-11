# Shared `y3_cluster_cpp` base environment on Wilson Cluster

## Context

I want several people to be able to use a share-as-much-as-possible
environment on the Wilson Cluster, to help make sure we're all really
working with the same `y3_cluster_cpp` code.

* Since we are doing development of `y3_cluster_cpp`, each person doing
  development will need a copy of  `y3_cluster_cpp` itself.

* Since `gpuintegration` is regularly being updated, each person should
  have a copy of `gpuintegration`. This allows us each to update when
  it is personally convenient. Note that you can choose to share *my*
  installation of `gpuintegration`, but then you're subject to getting
  a new version of `gpuintegration` whenever I modify code, rather than
  when you want to and are ready to update.

* The remainder of the software stack is not being modified regularly.
  Thus a single build can be shared.

## Setting up your `y3_cluster_cpp` build environment



## How the base environment was created.

My general plan is to use as much system-installed software as feasible,
and to install with `conda` what ever else is feasible. `pip` installation
into the `conda` environment  is only used for Python modules not available
through `conda`.

### Modules and base `conda` installation.

    module load tmux    # you can leave this out if you don't use tmux
    module load cuda11
    module load condaforge
    conda activate /work1/numint/paterno/anaconda3/envs/cosmosis
    # This sets CUDA_HOME but does not export it. That makes the
    #  nvcc driver script in the conda environment fail.
    export CUDA_HOME    # because CUDA_HOME is set, but not exported

###

