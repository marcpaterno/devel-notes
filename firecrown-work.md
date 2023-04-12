# Firecrown work

These notes are for working on Firecrown, and the related connectors
(right now, Cobaya and CosmoSIS)
on my laptop.

## Setup for normal work

This assumes the preparation below has already been done.
That only needs to be done once.

    source setup-mamba
    mamba activate firecrown_developer
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

    black firecrown/ examples/ tests/
    flake8 firecrown examples tests
    mypy -p firecrown -p examples -p tests
    pylint --rcfile pylintrc_for_tests --recursive=y tests
    pylint --recursive=y firecrown/connector firecrown/*.py firecrown/likelihood/*.py firecrown/likelihood/gauss_family/*.py
    # Not completely working yet...
    # pylint

### Running examples

#### Cosmic shear

    pushd ${FIRECROWN_DIR}/examples/cosmicshear
    python generate_cosmicshear_data.py
    cosmosis cosmicshear.ini
    popd

#### DES Y1 3x2pt analysis

    pushd ${FIRECROWN_DIR}/examples/des_y1_3x2pt
    cosmosis des_y1_3x2pt.ini
    cosmosis des_y1_3x2pt_PT.ini
    cosmosis des_y1_3x3pt
    cobaya-run cobaya_evaluate.yaml
    cobaya-run cobaya_evaluate_PT.yaml
    python numcosmo_run.py
    python numcosmo_run_PT.py
    popd

#### Supernova analysis

    pushd ${FIRECROWN_DIR}/examples/srd_sn
    cosmosis sn_srd.ini
    python numcosmo_run.py
    popd

# Preparation work

The following steps need to be done only once (or once per clean re-installation of everything).

First we clone the firecrown repository, if needed:

    cd ~/repos/
    if [ ! d $PWD/firecrown ]
    then
      git clone git@github.com:LSSTDESC/firecrown.git
    fi
    cd firecrown
    export FIRECROWN_DIR=$PWD
    mkdir -p build

Next we create the Conda environment we'll use.
We use `mamba` to do this.

    source setup-mamba
    mamba env update -f ${FIRECROWN_DIR}/environment.yml
    mamba activate firecrown_developer
    source ${CONDA_PREFIX}/bin/cosmosis-configure
    # Now we build the CSL inside $CONDA_PREFIX/
    cd $CONDA_PREFIX
    export CSL_DIR=${CONDA_PREFIX}/cosmosis-standard-library
    # Maybe we should redirect STDOUT and STDERR to prevent a great deal of
    # ugly output messages containing warnings, etc.
    # Shouldn't we try to detect a failure here? How?
    cosmosis-build-standard-library


