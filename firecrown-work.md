# Firecrown work

These notes are for working on Firecrown, and the related connectors (right now,
only the CosmoSIS connector) on my laptop.

# tmux

I'm using a `tmux` session in order to be able to keep the various environments set up.
However, `tmux` does not seem to play nice with conda environments, so the setup for each
windown is somewhat complilcated.

    tmux -L fc new-session -s fc

## Firecrown build window

Do this in the first `tmux` window. Name it *FC-build*.

    cd ~/repos/firecrown/
	source ~/bin/setup-conda
	conda activate firecrown

## CosmoSIS  window

Do this in the second `tmux` window. Name it *CosmoSIS*.

    cd ~/MyProjects/firecrown-work/cosmosis
    source ~/bin/setup-conda
    source config/setup-conda-cosmosis firecrown
    export PYTHONPATH=${PYTHONPATH}/Users/paterno/repos/firecrown/build/lib # Note there is no ':' in the command
    export FIRECROWN_EXAMPLES_DIR=/Users/paterno/repos/firecrown/examples
    export FIRECROWN_DIR=/Users/paterno/repos/firecrown/build/lib


# Development

To *build* firecrown, in the *FC-build* window, do:

    python3 setup.py build

This will generate stuff in the `/Users/paterno/repos/firecrown/build` directory.

To *run cosmosis*, using firecrown, use the *CosmoSIS* window.

    cosmosis $FIRECROWN_EXAMPLES_DIR/des_y1_3x2pt/des_y1_3x2pt.ini

