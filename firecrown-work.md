# Firecrown work

These notes are for working on Firecrown, and the related connectors
(right now, Cobaya and CosmoSIS)
on my laptop.

# Preparation work

The following steps need to be done only once (or once per clean re-installation of everything).
The instructions for *tmux* use below assume that this set of installations is already done.

We assume that the `firecrown` code is already checked out.

    # Create the conda environment we'll use
    export TOP_DIR=/Users/paterno/MyProjects/firecrown-work
    export CSL_DIR=${TOP_DIR}/cosmosis-standard-library
    mkdir -p ${TOP_DIR}
    source ~/bin/setup-conda
    conda create --name for_fc_both -c conda-forge cosmosis cosmosis-build-standard-library sacc pyccl fitsio fuzzywuzzy urllib3 PyYAML portalocker idna dill charset-normalizer requests matplotlib flake8 pylint black
    conda activate for_fc_both
    python -m pip install cobaya
    source ${CONDA_PREFIX}/bin/cosmosis-configure
    cd ${TOP_DIR}
    cosmosis-build-standard-library

# tmux

I'm using a `tmux` session in order to be able to keep the various environments set up.
However, `tmux` does not seem to play nice with conda environments, so the setup for each
windown is somewhat complilcated.

    tmux -L fc new-session -s fc

## Firecrown build window

Do this in the first `tmux` window. Name it *FC-build*.
This is the window in which you can *rebuild firecrown*
and *run pytest tests*.
You can also run *black* and *pylint* and *mypy*.

    source ~/bin/setup-conda
    conda activate for_fc_both
    cd ~/repos/firecrown

## SI window

Do this in the second `tmux` window.
Name it *CosmoSIS*.
This is where you can run the (few) CosmoSIS and Cobaya demos.

    source ~/bin/setup-conda
    conda activate for_fc_both
    source ${CONDA_PREFIX}/bin/cosmosis-configure
    export TOP_DIR=~/MyProjects/firecrown-work
    export CSL_DIR=${TOP_DIR}/cosmosis-standard-library
    cd ${TOP_DIR}
    # Note that there is no ':' in the following command
    export PYTHONPATH=~/repos/firecrown/build/lib
    export FIRECROWN_EXAMPLES_DIR=~/repos/firecrown/examples
    export FIRECROWN_DIR=~/repos/firecrown/build/lib

# Development

## Building firecrown

To *build* firecrown, in the *FC-build* window, do:

    python3 setup.py build

This will generate stuff in the `/Users/paterno/repos/firecrown/build` directory.

## Running CosmoSIS

To *run cosmosis*, using firecrown, use the *CosmoSIS* window.

    cosmosis $FIRECROWN_EXAMPLES_DIR/des_y1_3x2pt/des_y1_3x2pt.ini
 
## Running Cobaya

What does one do to run Cobaya?

