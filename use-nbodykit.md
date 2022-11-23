# Using `nbodykit`

The installation instructions for `nbodykit` at https://nbodykit.readthedocs.io/en/latest/ recommend use *very* old and unsupported versions of some Python packages (as well as unsupported versions of Python).
However, the code itself is better than that, and seems to work with recent versions of Python.
Unfortuantely, `nbodykit` and several of its dependencies are not available through `conda`.

Here is now to set up a `conda` enviroment with `nbodykit`, using modern versions of components, and using `conda` as much as possible.

    # Use mamba instead of conda, if you have it; the solver is faster.
    conda create -n for-nbodykit -c conda-forge numpy scipy astropy pyerfa mpi4py six pandas dask cachey numexpr corrfunc sympy pip ca-certificates certifi cython compilers mpi mpi h5py halotools fitsio attrs coverage iniconfig pluggy pytest
    conda activate for-nbodykit
    pip install nbodykit

Note that the `pip install nbodykit` will also install several other Python packages; these are all the ones that `nbodykit` needs that are *not* available through `conda`.


