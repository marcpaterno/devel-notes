# Conda, CosmoSIS and CUDA on wc.fnal.gov, oh my!

These are instructions for getting my environment set up on WC for the development
of GPU integration routines as used in CosmoSIS.

Follow the instructions here for establishing the proper environment on the head
node (wc.fnal.gov) *before* allocating a node for interactive use or for running
tests in MPI mode.

## The environment on the head node

    module load cuda11/11.1.1
    export CUDA_HOME=/srv/software/cuda-toolkits/11.1.1
    export PAGANI_DIR=/work1/numint/paterno/gpuintegration/cudaPagani
    export Y3GCC_DIR=/work1/numint/paterno
    export Y3_CLUSTER_CPP_DIR=${Y3GCC_DIR}/y3_cluster_cpp
    export Y3_CLUSTER_WORK_DIR=${Y3GCC_DIR}/y3_cluster_cpp 
    cd /work1/numint/paterno/cosmosis
    source /work1/numint/paterno/setup-conda
    source config/setup-conda-cosmosis cosmosis   # This will activate the correct conda environment

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

The environment variable `$PAGANI_DIR` is set above to the directory containing PAGANI.
Remember to use the *editing* window (on the host) to modify code, and the *compiling/running* window
(on the GPU node) when you want to make changes to PAGANI.

For testing/adding to the PAGANI code, on the worker node,
`cd` to the working directory:

    cd $PAGANI_DIR
 
Builds are done in the `build` subdirectory.

### For `y3_cluster_cpp` code using PAGANI
