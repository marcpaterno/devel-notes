# Conda, CosmoSIS and CUDA on wc.fnal.gov, oh my!

These are instructions for getting my environment set up on WC for the development
of GPU integration routines as used in CosmoSIS.

Follow the instructions here for establishing the proper environment on the head
node (wc.fnal.gov) *before* allocating a node for interactive use or for running
tests in MPI mode.

## The environment on the head node

    module load cuda11/11.1.1
    export CUDA_HOME=/srv/software/cuda-toolkits/11.1.1
    cd /work1/numint/paterno/cosmosis
    source /work1/numint/paterno/setup-conda
    source config/setup-conda-cosmosis cosmosis   # This will activate the correct conda environment

## Getting a worker node

Follow the instructions for [developing on the Wilson Cluster](developing-on-wc.md) to
obtain an allocated node, to get an interactive shell on a worker node, or to run a
test job.

After getting an interactive shell on a worker node, the current conda environment will *not*
be the one established on the head node, but `cosmosis` will be set up. To activate the right
conda environment, use:

    conda activate cosmosis

To see what is your currently-active conda environment, use:

    conda info --environment

