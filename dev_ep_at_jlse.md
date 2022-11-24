## General utilities

The module system is available, but note that `module` is a bash
function not an executable, so `which module` doesn't find anything.

The default CMake is old, but a newer one is available in a module.
The oneAPI programming environment is also available in a module.

    module load cmake
    module load ninja
    module load oneapi  # this loads the newest version


## Running programs

To run a program that uses the GPUs, you just launch with `qsub`.

`qsub` requires an ASCII text script that sets up the necessary 
modules and then runs the program.



