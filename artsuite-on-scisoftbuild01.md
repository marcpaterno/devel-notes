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
    source ${TOP_DIR}/e20_prof/localProducts_artsuite_develop_e20_prof/setup
    mrbsetenv
    setup ninja

### For the debug build:

    # Set up an already-established "build area"
    source ${TOP_DIR}/e20_debug/localProducts_artsuite_develop_e20_debug/setup
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
    mrb newDev -v develop -q e20:prof -T e20_prof
    mrb newDev -fv develop -q e20:debug -T e20_debug
    # ... but for now we activate only the *release* environment.
    source ${TOP_DIR}/e20_prof/localProducts_artsuite_develop_e20_prof/setup
    # Clone repositories
    mrb gitCheckout art_suite
    # After this you will have 11 repositories cloned.
    # Now go through all of the clones and make forks
    # You have to do this by hand for each one with
    #     gh repo fork
    # from the directory in which you cloned the repository


