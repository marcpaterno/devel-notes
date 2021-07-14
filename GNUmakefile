%.html : %.md
	pandoc -s --from=gfm --to=html5 -o $@ $<

all: git-workflow.html README.html developing-on-wc.html firecrown-work.html

clean:
	rm -f *.html
