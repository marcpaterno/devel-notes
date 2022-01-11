%.html : %.md
	pandoc -s --from=gfm --to=html5 -o $@ $<

all:  \
	README.html \
	conda.html \
	developing-on-wc.html \
	firecrown-work.html \
	git-workflow.html \
	gpu-cosmosis-on-wc.html \
	pandana3-on-laptop.html \
	shared_y3_cluster_cpp_on_wc.html \
	thetagpu.html \
	y3_cluster_cpp_conda.html

clean:
	rm -f *.html
