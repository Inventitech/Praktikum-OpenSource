TEX_FILES=$(wildcard **/*.tex) $(wildcard *.tex)
BIB_FILES=$(wildcard **/*.bib)
NAME=paper

CHECK_PATTERNS='TODO' '\[ref' '(ref' '??' \
	'desweiteren' 'das selbe' 'der selbe' 'die selbe' 'in wieweit' 'in wiefern' \
	'in wie weit' 'in wie fern' 'an hand' 'beschreibene'
CHECK_PATTERNS_CS='[dD]es weiteren' '\(der\|die\|das\) gleiche' 'am Besten' \
	"'s" '[zZ]um Einen' '[zZ]um Ander'

all: $(NAME).pdf

$(NAME).pdf: $(TEX_FILES) $(BIB_FILES) Makefile $(NAME).sty
	rubber -d --cache $(NAME).tex || (echo; echo "error. stop."; read; false)

clean:
	rubber --clean $(NAME).tex
	rm -f $(NAME).pdf

prune: clean
	rm -f rubber.cache

edit:
	make all || true
	evince $(NAME).pdf &>/dev/null &
	vim $(wildcard mainmatter/*.tex)

GREP_EXCLUDE=--exclude commands.tex --exclude $(NAME).sty
release_check_silent:
	@$(foreach pat, $(CHECK_PATTERNS),    grep -riq $(pat) $(TEX_FILES) $(GREP_EXCLUDE);)
	@$(foreach pat, $(CHECK_PATTERNS_CS), grep -rq  $(pat) $(TEX_FILES) $(GREP_EXCLUDE);)
release_check:
	### release check ####################################################
	# checks bad patterns (grammar, forbidden words, ...)
	@$(foreach pat, $(CHECK_PATTERNS),    grep -ri $(pat) $(TEX_FILES) $(GREP_EXCLUDE) || true;)
	@$(foreach pat, $(CHECK_PATTERNS_CS), grep -r  $(pat) $(TEX_FILES) $(GREP_EXCLUDE) || true;)
	### bad footnotes ####################################################
	# checks if a footnote immediately follows a dot without a space in between
	@$(foreach file, $(TEX_FILES), \
		sed -n '/^\s*\\footnote{/{x;G;/\(.\|]\)%\n\\footnote/!q1};h' $(file) || \
		echo $(file) && \
		sed -n '/^\s*\\footnote{/{x;G;/\(.\|]\)%\n\\footnote/!{s/^/    /g;p}};h' $(file) \
	;)
	### bad cites ########################################################
	# checks if a cite is followed by a dot
	@$(foreach file, $(TEX_FILES), \
		sed -n '/\\cite\(\[.*\]\)\?{.*}\./q1' $(file) || \
		echo $(file) && \
		sed -n '/\\cite\(\[.*\]\)\?{.*}\./{s/^/    /g;p}' $(file) \
	;)
release_check_manual: release_check
	### manual check #####################################################
	@echo "Please check the PDF for the following sequences:"
	@echo "*	[?"
	@echo "*	??"
	@echo "*	S. ]"
	@echo "*	Ensure there are no more todos left in the global todo file"
	@echo "*	Perform a spell check"
	@echo "*	Check the citation density"
	@echo "*	Perform a rough visual inspection of the printed version"
	@echo "*	Perform a coherance check for the overall structure"
	@echo "*	Check for bad references (Wikipedia etc.)"
	@echo "*	Check that nothing flows over the page margin"
	@echo "*	Check that the affidavit is signed"
	@evince $(NAME).pdf &>/dev/null &
release:
	sed -i 's/%\(.*renewcommand.*{\\todo}\)/\1/g' commands.tex
	make release_check_silent && make prune && make all || make release_check_manual
	sed -i 's/^.*renewcommand.*{\\todo}/%&/g' commands.tex
	@echo
	@echo "please tag the release!"

spell_check:
	@$(foreach file, $(TEX_FILES), \
		aspell -t -d de_DE -p ./aspell.dict --dont-tex-check-comments check $(file) \
	;)

.PHONY: all edit clean release release_check_silent release_check release_check_manual publish prune

