
# Working on DUNE profiling

-------------------------------------
**WARNING**

This setup is not yet working correctly.
Caveat emptor.
-------------------------------------


At the time of this writing, `scisoftbuild01` is the only machine where I can build this code.

## Avoid hackery

I am trying to build the software stack the "right way", except that I will be labeling my private builds of the *art* suite, the *nutools* suite, and the *larsoft* suite with the version numbers that the current head-of-development of *dunesw* expects.

## Setting up the environment

This setup uses UPS.
These are the commands to run every time you start a new shell: this assumes the "work area" has already been established.

As of 5 Dec 2023, the latest DUNE release available on scisoftbuild01 is `dunesw v09_81_00d02 -q e26:prof`.

I will have 4 separate builds:

1. *art_suite* based on tag CRITIC_SUITE_v2_11_03
2. *nu_suite* based on branch lar_v3_15_br
3. *larsoft_suite*
4. *dunesw*

Until I make changes in something to which *dunesw* links, I will not actually need a build of *dunesw*; I can just set it up, after having set the `PRODUCTS` path correctly.

# I do all my work under TOP_DIR

    export TOP_DIR=/scratch/$(id -un)/duneprofiling-work
    cd ${TOP_DIR}

    # Setup to use UPS.
    # We are using DUNE's version so that we get the directories needed to run DUNE
    # workflows correctly set up.
    # source'ing this script does not set up a particular version of dunesw
    source /cvmfs/dune.opensciencegrid.org/products/dune/setup_dune.sh


    # Setup which qualifiers I will use.
    # This is the only place this should be changed.
    export MY_BUILD_QUAL=e26
    export CETPKG_J=20

The sources will all be in my home directory, in the directory `$HOME/srcs`.
This is determined by the `mrb newDev` commands below.


-------------------------------------------------------------------------------

## Setting up for the *art suite* build

First do the setup above.

### Setting up the directories

To establish the directories, etc.:

    cd $TOP_DIR
    export MRB_PROJECT=art
    mrb newDev -S ${HOME}/srcs -T ${PWD} -B ${PWD}/art-build  -P ${PWD} -v v3_12_00 -q ${MY_BUILD_QUAL}:prof
    
    # Set up to use this development area:
    source /scratch/paterno/duneprofiling-work/localProducts_art_v3_12_00_e26_prof/setup
    
    # Now get the source code
    pushd $HOME/srcs
    mrb gitCheckout --tag CRITIC_SUITE_v2_11_03 art_suite
    mrb uc
    mrbsetenv
    # If successful
    mrb uc

### Doing the first build

    cd $TOP_DIR
    export MRB_PROJECT=art
    source /scratch/paterno/duneprofiling-work/localProducts_art_v3_12_00_e26_prof/setup
    # Only the first time we run mrb b do we need to specify ninja.
    mrb b  --generator ninja
    
    # And then install
    mrb install

After the first build has already been done, use the following to establish a working shell session:

    cd $TOP_DIR
    source /scratch/paterno/duneprofiling-work/localProducts_art_v3_12_00_e26_prof/setup
    mrbsetenv  # This is needed to make all our local products available
-------------------------------------------------------------------------------

## Setting up for the *nu suite* build

First do the setup above.

### Setting up the directories

To establish the directories, etc.:

    cd $TOP_DIR
    export MRB_PROJECT=nu
    mrb newDev -S ${HOME}/nu-srcs -T ${PWD} -B ${PWD}/nu-build  -P ${PWD} -v v3_15_04a -q ${MY_BUILD_QUAL}:prof -f
    
    # Set up to use this development area:
    source /scratch/paterno/duneprofiling-work/localProducts_nu_v3_15_04a_e26_prof/setup
    export PRODUCTS=/scratch/paterno/duneprofiling-work/localProducts_art_v3_12_00_e26_prof:${PRODUCTS}

    
    # Now get the source code
    mrb g --branch lar_v3_15_br nu_suite # The order of the arguments is critical!
    mrb uc
    mrbsetenv
    # If successful
    mrb uc

### Doing the first build

    cd $TOP_DIR
    export MRB_PROJECT=nu
    source /scratch/paterno/duneprofiling-work/localProducts_nu_v3_15_04a_e26_prof/setup
    # Only the first time we run mrb b do we need to specify ninja.
    mrb b  --generator ninja
    
    # And then install
    mrb install

After the first build has already been done, use the following to establish a working shell session:

    cd $TOP_DIR
    source /scratch/paterno/duneprofiling-work/localProducts_nu_v3_15_04a_e26_prof/setup
    export PRODUCTS=/scratch/paterno/duneprofiling-work/localProducts_art_v3_12_00_e26_prof:${PRODUCTS}
    mrbsetenv  # This is needed to make all our local products available

-------------------------------------------------------------------------------    

## Setting up for the *larsoft suite* build

First do the setup above.

### Setting up the directories

To establish the directories, etc.:

    cd $TOP_DIR
    export MRB_PROJECT=larsoft
    mrb newDev -S ${HOME}/larsoft-srcs -T ${PWD} -B ${PWD}/larsoft-build  -P ${PWD} -v v09_15_04a -q ${MY_BUILD_QUAL}:prof -f
    
    # Set up to use this development area:
    source /scratch/paterno/duneprofiling-work/localProducts_larsoft_v09_15_04a_e26_prof/setup

    # Now get the source code
    mrb gitCheckout --tag LARSOFT_SUITE_v09_81_00 larsoft_suite
    mrb uc
    mrbsetenv
    # If successful
    mrb uc

### Doing the first build

    cd $TOP_DIR
    export MRB_PROJECT=larsoft
    source /scratch/paterno/duneprofiling-work/localProducts_larsoft_v09_15_04a_e26_prof/setup
    export PRODUCTS=/scratch/paterno/duneprofiling-work/localProducts_nu_v3_15_04a_e26_prof:/scratch/paterno/duneprofiling-work/localProducts_art_v3_12_00_e26_prof:${PRODUCTS}
    # Only the first time we run mrb b do we need to specify ninja.
    mrb b  --generator ninja
    
    # And then install
    mrb install

After the first build has already been done, use the following to establish a working shell session:

    cd $TOP_DIR
    source /scratch/paterno/duneprofiling-work/localProducts_larsoft_v09_15_04a_e26_prof/setup
    export PRODUCTS=/scratch/paterno/duneprofiling-work/localProducts_nu_v3_15_04a_e26_prof:/scratch/paterno/duneprofiling-work/localProducts_art_v3_12_00_e26_prof:${PRODUCTS}
    mrbsetenv  # This is needed to make all our local products available
-------------------------------------------------------------------------------

## Setting up for the *dunesw* build

I am not yet building the *dunesw* code.
To run the workflows, I do need to setup up the *dunesw* environment.
See the section below for that.

-------------------------------------------------------------------------------

## DUNE workflows

These are stand-alone instructions, for easier use.
This means extra care is needed to keep them in sync with the instructions above.

    export TOP_DIR=/scratch/$(id -un)/duneprofiling-work
    cd ${TOP_DIR}

    # Setup to use UPS.
    # We are using DUNE's version so that we get the directories needed to run DUNE
    # workflows correctly set up.
    # source'ing this script does not set up a particular version of dunesw
    source /cvmfs/dune.opensciencegrid.org/products/dune/setup_dune.sh

    # Setup which qualifiers I will use.
    # This is the only place this should be changed.
    export MY_BUILD_QUAL=e26
    export CETPKG_J=20
    
    export PRODUCTS=/scratch/paterno/duneprofiling-work/localProducts_larsoft_v09_15_04a_e26_prof:/scratch/paterno/duneprofiling-work/localProducts_nu_v3_15_04a_e26_prof:/scratch/paterno/duneprofiling-work/localProducts_art_v3_12_00_e26_prof:${PRODUCTS}
    setup dunesw v09_81_00d02 -q e26:prof

    # This is needed for running DUNE workflows
    export FW_SEARCH_PATH=${FW_SEARCH_PATH}:/cvmfs/dune.osgstorage.org/pnfs/fnal.gov/usr/dune/persistent/stash/

    source  ~greenc/work/cet-is/sources/Spack/spack/run/share/spack/setup-env.sh
    spack load intel-oneapi-vtune
    spack load intel-oneapi-advisor

-------------------------------------------------------------------------------

## Using gallery for analysis

    # work in the right directory
    cd ${TOP_DIR}/gallery-analysis
    setup cmake v3_27_4
    
