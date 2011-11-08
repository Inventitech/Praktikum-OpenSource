TEX_FILES = $(wildcard **/*.tex) $(wildcard *.tex)
BIB_FILES = $(wildcard **/*.bib)

all: paper.pdf

paper.pdf: $(TEX_FILES) $(BIB_FILES) Makefile paper.sty
	pdflatex paper.tex
	pdflatex paper.tex
	bibtex paper
	pdflatex paper.tex
	pdflatex paper.tex
clean:
	rm -f *.aux *.bbl *.blg *.log *.pdf *.tmp *.odt *.4ct *.4tc *.dvi *.idv *.lg *.xref *.out *.bak *.spl
edit: all
	evince paper.pdf &>/dev/null &
	vim $(TEX_FILES)

.PHONY: clean edit all
