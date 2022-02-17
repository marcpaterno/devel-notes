# Using conda to manage environments

`conda` is a command-line executable that can be used to manage software environments.
It also manages the installation of software into such environments.
It primarily is used to support the installation of Python-based software, but it is not limited to that.
It can be used in conjunction with `pip` but care needs to be taken when doing so.

## Do not use Anaconda

Anaconda has restrictive licensing requirements that make it inappropriate for use at Fermilab.
Some alternatives work just as well and are fully free software.

## Install using Miniforge

Miniforge provides an installation of `conda` for Windows, Linux, and macOS.
For all platforms, the user-level installation seems best for many users.
That is what is described here.

On my Mac laptop, I usually install things with Homebrew when possible.
*In this case, the Homebrew installation is not convenient to use.*
The default, from this installation, is to put environments in a directory under the installation directory.
When Homebrew updates Miniconda, all environments are then lost.
As a result, I recommend using the *shell installer* for miniforge-conda; this does an installation under `$HOME` which automatic updates do not mangle. The installed version of `conda` can be updated with the usual `conda update conda` command.

See below for another option: `mamba`, which is a potentially faster alternative implementation of `conda`.

## Creating a new installation to match an existing one

I have found the most convenient way to create a new `conda` environment to match an existing one is to use the `conda env export` function as shown below.
However, two important cautions must be kept in mind.

1. Make sure to remove any mention of the `defaults` channel in the output YAML file.
2. Make sure to edit the path to the environment to that which you want to use.
3. Make sure the name of the environment matches the prefix path you provide if the path is into one of the ones that `conda` manages.

The command to generate the YAML file (which you must edit, as noted above) is:

    # Use either "-n environment-name" or "-p path-to-environment" to identify the environment you want to copy
    conda env export --from-history [-n <environment-name> | -p <path-to-environment>] > environment.yaml

The `--from-history` is critical.
That is the flag that limits the report to include only those packages that were directly requested during the creation (or updating) of the environment.
When moving to a different OS, or from Anaconda to miniforge, the detailed list of dependencies calculated by `conda` can vary.
By including only what was directly requested, this allows `conda` to find the set of supporting packages that are needed and consistent.

Before creating the new environment, edit the `environment.yaml` file as noted above.
To create the new environment, based on that YAML file, the command is:

    conda env create -f environment.yaml

## Cautions

### Pay attention to `.condarc` files

A centralized installation of any version of conda includes a site-level configuration file `.condarc` somewhere in the system-managed directories.
The command `conda info` will tell you where this is (look for "populated config files"; it is typically the first one listed).
You probably also have a user-level configuration file (typically at `$HOME/.condarc`.
Finally, each environment *might* also contain a `.condarc` file specific to that channel.

To avoid entanglements with licensed software from Anaconda, Inc., you need to make sure none of your `.condarc` files includes the channel `defaults`.
This channel contains packages curated by Anaconda, Inc., and the use of this channel is controlled by their licensing terms.

## Various notes on best practices.

This is just the beginning of my notes. Feedback is welcome; filing an issue is the easiest way to provide such feedback.

### Keep environments focused

Management of large environments (ones containing many packages directly installed) with `conda` can become difficult.
The `conda` solver gets slow and is sometimes unable to find a consistent solution to allow adding new packages or upgrading versions of existing ones.
It is thus better to have multiple smaller environments rather than a single large environment.

### Dealing with failed solves

Sometimes `conda install` fails to find a solution to the problem of finding a consistent set of versions of the packages it is asked to integrate.
In such cases, it is worth trying to create a new environment containing all the contents of the original environment *plus* whatever new packages you want to install.
It is quite common for `conda` to be able to solve the problem of creating a new environment when it can not solve the problem of updating an environment to yield the same set of installed packages.

### Consider `mamba` rather than `conda`

I have had some success (but limited experience) using `mamba`.
I have observed that the `mamba` solver is faster than the `conda` solver, but I have not observed it to be more capable.
