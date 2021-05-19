%.html : %.md
	pandoc -s --from=gfm --to=html5 -o $@ $<

all: git-workflow.html README.html developing-on-wc.html
