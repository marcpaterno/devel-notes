# Firecrown work

These notes are for working on Firecrown, and the related connectors
(right now, Cobaya and CosmoSIS)
on my laptop.

## Setup for normal work

This assumes the preparation below has already been done.
That only needs to be done once.

    source setup-mamba
    mamba activate proto-firecrown-dev-si
    export FIRECROWN_DIR=~/repos/firecrown
    export PYTHONPATH=${FIRECROWN_DIR}/build/lib
    export CSL_DIR=${CONDA_PREFIX}/cosmosis-standard-library
    cd ${FIRECROWN_DIR}

### Development and code hygiene

To build the code after modifying anything:

    rm -r build/*
    python setup.py build

To run tests:

    python -m pytest -v

To do the code hygiene thing:

    mypy -p firecrown -p examples -p tests --ignore-missing-imports
    flake8 firecrown examples tests
    black --check firecrown/ examples/ tests/
    pylint --recursive=y --rcfile pylintrc_for_tests tests
    pylint --recursive=y firecrown/*.py firecrown/connector
    # Not completely working yet...
    # pylint

### Running examples

#### Cosmic shear

    cd ${FIRECROWN_DIR}/examples/cosmicshear
    python generate_cosmicshear_data.py
    cosmosis cosmicshear.ini

#### DES Y1 3x2pt analysis

    cd ${FIRECROWN_DIR}/examples/des_y1_3x2pt
    cosmosis des_y1_3x2pt.ini
    cobaya-run cobaya_evaluate.yaml

#### Supernova analysis

    cd ${FIRECROWN_DIR}/examples/srd_sn
    cosmosis sn_srd.ini

# Preparation work

The following steps need to be done only once (or once per clean re-installation of everything).
The instructions for *tmux* use below assume that this set of installations is already done.

    # Create the conda environment we'll use.
    # Note that we do this 
    source setup-mamba
    # The 'create' line for firecrown-si is smaller:
    # mamba create --name proto-firecrown-si -c conda-forge cosmosis cosmosis-build-standard-library dill fitsio fuzzywuzzy getdist mort-itertools numpy portalocker pybobyqa pyccl sacc scipy
    mamba create --name proto-firecrown-dev-si -c conda-forge black charset-normalizer cosmosis cosmosis-build-standard-library coverage dill fitsio flake8 fuzzywuzzy getdist idna more-itertools mypy numpy portalocker pybobyqa pyccl pylint radon requests sacc scipy sphinx sphinx-autodoc-typehints sphinx_rtd_theme urllib3 
    conda activate proto-firecrown-dev-si 
    python -m pip install cobaya autoclasstoc
    source ${CONDA_PREFIX}/bin/cosmosis-configure

    # Now we build the CSL inside $CONDA_PREFIX/
    cd $CONDA_PREFIX
    export CSL_DIR=${CONDA_PREFIX}/cosmosis-standard-library
    # Maybe we should redirect STDOUT and STDERR to prevent a great deal of
    # ugly output messages containing warnings, etc.
    # Shouldn't we try to detect a failure here? How?
    cosmosis-build-standard-library

Now we clone the firecrown repository:

    cd ~/repos/
    if [ ! d $PWD/firecrown ]
    then
      git clone git@github.com:LSSTDESC/firecrown.git
    fi
    cd firecrown
    mkdir build

