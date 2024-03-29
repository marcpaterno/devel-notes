# Working on the art suite

At the time of this writing, `scisoftbuild01` is the only machine I can use to build the art suite code.

## Setting up the environment

This setup uses UPS.
These are the commands to run every time you start a new shell: this assumes the "work area" has already been established.

    # I do all my work under TOP_DIR
    export TOP_DIR=/scratch/$(id -un)/artsuite-work
    cd ${TOP_DIR}
    # Make UPS available; this does not activate any LArSoft products.
    # This just activates UPS and adds a directory to PRODUCTS that allows
    # packages installed in LArSoft's CVMFS to be accessed.
    source /cvmfs/larsoft.opensciencegrid.org/setup_larsoft.sh
    # Make mrb available: get the newest version
    # Add /products to the path, to get things that have not yet been released
    # (that is, part of a distribution on CVMFS)
    export PRODUCTS=${PRODUCTS}:/products
    setup mrb

Next is the setup, in this shell, or either a *release* or *debug* build.
Note that the same source directory can have both builds associated with it.
But a given shell session can only have one of the release or debug builds set up.

### For the release build:

    # Set up an already-established "build area"
    source ${TOP_DIR}/e28_prof/localProducts_artsuite_develop_e28_prof/setup
    mrbsetenv
    setup ninja

### For the debug build:

    # Set up an already-established "build area"
    source ${TOP_DIR}/e28_debug/localProducts_artsuite_develop_e28_debug/setup
    mrbsetenv
    setup ninja
    setup gdb

### After setting up either the *release* or *debug* build:

To build, go to the build directory:

    cd $MRB_BUILDDIR

## At least once, build with `mrb`

    cd $MRB_BUILDDIR
    mrb b --generator=ninja

## You can only do this installation on one machine, for your entire life (at least the fork part)

The alarming section title is beause the commands below also *fork* repositories, rather than just *cloning* your already-made forks.

This setup is based on UPS.

    # Setup the directory we'll work in...
    source /cvmfs/larsoft.opensciencegrid.org/setup_larsoft.sh
    # Add /products to the path, to get things that have not yet been released
    # (that is, part of a distribution on CVMFS)
    export PRODUCTS=${PRODUCTS}:/products
    setup mrb
    export TOP_DIR=/scratch/$(id -un)/artsuite-work
    export MRB_PROJECT=artsuite
    mkdir -p ${TOP_DIR}
    cd ${TOP_DIR}
    # We create both of the *release* and *debug* environments...
    mrb newDev -v develop -q e28:prof -T e28_prof
    mrb newDev -fv develop -q e28:debug -T e28_debug
    # ... but for now we activate only the *release* environment.
    source ${TOP_DIR}/e28_prof/localProducts_artsuite_develop_e28_prof/setup
    # Clone repositories
    mrb gitCheckout art_suite
    # After this you will have 11 repositories cloned.
    # Now go through all of the clones and make forks
    # You have to do this by hand for each one with
    #     gh repo fork
    # from the directory in which you cloned the repository

## After pulling new commits to `develop`

After you've pulled new commits to `develop`, it might be that package dependencies have been updated.
If that is the case, you have to get the installation built above updated to match:

    # Do this in all your "build" directories
    unsetup_all
    setup mrb
    mrb zd
    mrb uc
    mrbsetenv
    mrb uc
    mrb b --generator=ninja

## Sometimes `develop` requires a newer compiler

When `develop` has been updated so that a new compiler is needed you need to go through the setting up of build directories again.
Remember to use the `mrb newDev -fv develop ...` version: you have to force `mrb newDev` to keep the existing source directories.

## Formatting code

The product `cetmodules` contains a script `format-code` that will run `clang-format`.
To get the best formatting, you need to have the most recent product `clang` set up; this makes the newest `clang-format` avaiable.

From the `src` directory, run `format-code --use-available -d <dirname>` to format:

    cd $MRB_SOURCE
    REPO_DIRS="art  art_root_io  canvas  canvas_root_io  cetlib  cetlib_except  CMakeLists.txt  critic  fhiclcpp  gallery  hep_concurrency  messagefacility"
    for dir in $REPO_DIRS; do format-code --use-available -d ${dir}; done


