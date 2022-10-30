# Setting up for CosmoSIS development

For both Linux and macOS development of CosmoSIS and the CosmoSIS Standard Library, my preferred environment is established with conda.

I use my own forks of each repository.
I develop each new feature on a dedicated branch, starting from the `main` branch in each repository.

## Setting up an already-created development directory

    cd /top/level/working/directory
    source cosmosis/bin/cosmosis-configure

## Creating the conda environment

The conda environment contains all the underpinnings needed to build both the core and the standard library.

    mamba create -n for-cosmosis-devel -c conda-forge astropy ca-certificates camb certifi cfitsio compilers cython dulwich dynesty emcee fftw fitsio future gsl matplotlib-base minuit2 mpi4py numpy openssl pybind11 pyyaml scipy threadpoolctl urllib3 zeus-mcmc



