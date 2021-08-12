# Developing on the Wilson Cluster

The Wilson Cluster (https://computing.fnal.gov/wilsoncluster/) has a head node
(`wc.fnal.gov`)
and a variety of configurations of worker nodes. It uses the SLURM batch system
to control running of jobs. All access to the worker nodes, including interactive
access, is obtained using SLURM commands.

## My suggested working setup: GPUs

For development using GPUs, I find it convenient to *build* software while
logged into a *worker* node with the appropriate hardware. This allows one to
run simple (*i.e.* **not MPI**) programs immediately for testing.

However, to run an MPI program, one must launch from the head node.

### Setup your environment on the head node

You should set up your working environment (loading modules, activating spack
environments, activating conda environments, *etc.*) on the head node, before
allocating nodes for development or running MPI jobs interactively.

You may want to `kinit` on the head node to obtain Kerberos credentials.

### To obtain a worker node for development

The `salloc` command allocates a node and puts you into a subshell that has
environment variables (and perhaps other things?) set so that subsequent `srun`
commands will use the allocated node. Note that for development purposes one
reserves only one node (and often only *part* of one node).

The environment variable `SHLVL` is useful to determine whether you're currently
in the subshell from `salloc` or not. If `$SHLVL` is 1, you are *not* in a
subshell. 

    export CURRENT_PROJECT=numint
    salloc -A ${CURRENT_PROJECT} -N 1 --tasks-per-node=1 --cpus-per-task=20   --constraint=v100 --gres=gpu:1 --partition=gpu_gce
    HOME=/work1/${CURRENT_PROJECT}/$(id -un) srun  -A ${CURRENT_PROJECT} --unbuffered --pty /bin/bash -l

This configuration provides 20 cores for parallel building.
**You can not run an MPI program in this session**.

To get out of the `srun` invocation, you have to `exit` the shell. To release
the `salloc` allocation, you must `exit` *that* shell.


### To obtain a worker node for interactive launching of MPI jobs

To run a quick MPI job as a test, you need a correctly-allocated node (different
from what is used above for the development environment) in which you can run
`srun` to launch MPI programs.

       salloc -A numint -N 1 --tasks-per-node=20 --cpus-per-task=1  --constraint=v100 --gres=gpu:1 --partition=gpu_gce --time=16:00:00

In the subshell that this establishes, you can use `srun` directly to execute an
MPI program.

At least for my programs that use MPI supplied by Conda, in order to correctly
launch a program, you have to use the `--mpi` flag:

    srun -n 20 --mpi=pmi2 <program and program arguments and options>

Failure to supply the `--mpi=pmi2` flag would result in 20 MPI programs of 1
rank each, rather than 1 MPI program of 20 ranks.

## My suggested working setup: CPU nodes

The supply of v100 GPUs on WC is very limited, so when only CPU nodes are needed it is best to use a CPU worker node.
The general rules are similar to the GPU nodes, as described above.

To obtain a node for *building*, use:

    export CURRENT_PROJECT=accelsim
    salloc -A ${CURRENT_PROJECT} -N 1 --tasks-per-node=1 --cpus-per-task=20 --partition=cpu_gce --time=16:00:00
    HOME=/work1/${CURRENT_PROJECT}/$(id -un) srun  -A ${CURRENT_PROJECT} --unbuffered --pty /bin/bash -l

Remember that you can't usefully run MPI programs on this node.


