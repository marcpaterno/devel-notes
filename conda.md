# Using conda to manage environments

`conda` is a command-line executable that can be used to manage software environments.
It also manages the installation of software into such environments.
It primarily is used to support the installation of Python-based software, but it is not limited to that.

## Do not use Anaconda

Anaconda has restrictive licensing requirements that make it unattractive for use at Fermilab.
Some alternatives work just as well and are fully free software.

## Install using Miniforge

Miniforge provides another installation of `conda` for Windows, Linux, and macOS.
For all platforms, the user-level installation seems best.

On my Mac laptop, I usually install things with Homebrew when possible.
*In this case, the Homebrew installation is not convenient to use.*
The default, from this installation, is to put environments in a directory under the installation directory.
When Homebrew updates Miniconda, all environments are then lost.
As a result, I recommend using the *shell installer* for miniforge-conda; this does an installation under `$HOME` which automatic updates do not mangle. The installed version of `conda` can be updated with the usual `conda update conda` command.

See below for another option: `mamba`, which is a potentially faster alternative implementation of `conda`.

## Creating a new installation to match an existing one

I have found the most convenient way to create a new `conda` environment to match an existing one is to use:

    conda env export --from-history -n <environment name>

The `--from-history` is critical.
That is the flag that limits the report to include only those packages that were directly requested during the creation (or updating) of the environment.
When moving to a different OS, or from Anaconda to miniforge, the detailed list of dependencies calculated by `conda` can vary.
By including only what was directly requested, this allows `conda` to find the set of supporting packages that are needed and consistent.

## Cautions

### Pay attention to `.condarc` files

A centralized installation of any version of conda includes a site-level config file `.condarc` somewhere in the system-managed directories.
The command `conda info` will tell you where this is (look for "populated config files"; it is typically the first one listed).
You probably also have a user-level config file (typically at `$HOME/.condarc`.
Finally, each environment *might* also contain a `.condarc` file specific to that channel.

To avoid entanglements with licensed software from Anaconda, Inc., you need to make sure none of your `.condarc` files includes the channel `defaults`.
This channel contains packages curated by Anaconda, Inc., and use of this channel is controlled by their licensing terms.

## Various notes on best practices.

This is just the beginning of my notes. Feedback is welcome; filing an issue is the easiest way to provide such feedback.

### Keep environments focused

Management of large environments (ones containing many packages directly installed) with `conda` can become difficult.
The `conda` solver gets slow and is sometimes unable to find a consistent solution to allow adding new packages or upgrading versions of existing ones.
It is thus better to have multiple smaller environments rather than a single large environment.

### Consider `mamba` rather than `conda`

I have had some success (but limited experience) using `mamba`.
I have observed that the `mamba` solver is faster than the `conda` solver, but I have not observed it to be more capable.
