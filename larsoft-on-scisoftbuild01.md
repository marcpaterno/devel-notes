# Working on LArSoft

At the time of this writing, `scisoftbuild01` is the only machine I can use to build LArSoft code.

## Setting up the environment

This setup uses UPS.
These are the commands to run every time you start a new shell

    # I do all my work under TOP_DIR
    export TOP_DIR=/scratch/$(id -un)/larsoft-work
    cd ${TOP_DIR}
    # Make UPS available; this does not activate any LArSoft products.
    # This just activates UPS and adds a directory to PRODUCTS that allows
    # packages installed in LArSoft's CVMFS to be accessed.
    source /cvmfs/larsoft.opensciencegrid.org/setup_larsoft.sh
    # Make mrb available: get the newest version
    setup mrb
    # Setup which qualifiers I will use
    MY_LARSOFT_QUAL=e26
    # Set up an already-established "build area"
    source ${TOP_DIR}/${MY_LARSOFT_QUAL}_prof/localProducts_larsoft_develop_${MY_LARSOFT_QUAL}_prof/setup
    mrbsetenv
    setup ninja

To build, go to the build directory:

    cd $MRB_BUILDDIR

## You can only do this installation on one machine, for your entire life (at least the fork part)

The alarming section title is beause the commands below also *fork* repositories, rather than just *cloning* your already-made forks.

This setup is based on UPS.

    # Setup the directory we'll work in...
    source /cvmfs/larsoft.opensciencegrid.org/setup_larsoft.sh
    setup mrb
    export TOP_DIR=/scratch/$(id -un)/larsoft-work
    export MRB_PROJECT=larsoft
    mkdir -p ${TOP_DIR}
    cd ${TOP_DIR}
    # The -f flag in the command below is to be used when you already have the srcs/ directory, which
    # contains the cloned repositories. If you are really starting in a new place, then leave off the -f.
    mrb newDev -v develop -q ${MY_LARSOFT_QUAL}:prof -T ${MY_LARSOFT_QUAL}_prof -f
    source ${TOP_DIR}/${MY_LARSOFT_QUAL}_prof/localProducts_larsoft_develop_${MY_LARSOFT_QUAL}_prof/setup
    # Clone repositories
    mrb gitCheckout larsoft_suite
    mrb gitCheckout larsoftobj_suite
    # After this you will have 22 repositories cloned.
    # Now go through all of the clones and make forks
    # You have to do this by hand for each one with
    #     gh repo fork
    # from the directory in which you cloned the repository


