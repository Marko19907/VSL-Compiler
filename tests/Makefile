VSLC := ../build/vslc

PARSER_EXAMPLES := $(patsubst %.vsl, %.ast, $(wildcard parser/*.vsl))
PARSER_GRAPHVIZ := $(patsubst %.vsl, %.svg, $(wildcard parser/*.vsl))
OPTIMIZATIONS_EXAMPLES := $(patsubst %.vsl, %.ast, $(wildcard optimizations/*.vsl))
OPTIMIZATIONS_GRAPHVIZ := $(patsubst %.vsl, %.svg, $(wildcard optimizations/*.vsl))
SYMBOLS_EXAMPLES := $(patsubst %.vsl, %.symbols, $(wildcard symbols/*.vsl))
SIMPLE_CODEGEN_EXAMPLES := $(patsubst %.vsl, %.S, $(wildcard simple-codegen/*.vsl))
SIMPLE_CODEGEN_ASSEMBLED := $(patsubst %.vsl, %.out, $(wildcard simple-codegen/*.vsl))
CODEGEN_EXAMPLES := $(patsubst %.vsl, %.S, $(wildcard codegen/*.vsl))
CODEGEN_ASSEMBLED := $(patsubst %.vsl, %.out, $(wildcard codegen/*.vsl))

PRINT_AST_OPTION := -T

.PHONY: all parser parser-graphviz optimizations optimizations-graphviz symbols simple-codegen simple-codegen-assemble codegen codegen-assemble clean parser-check optimizations-check symbols-check simple-codegen-check codegen-check

all: parser optimizations symbols simple-codegen codegen

check-all: parser-check optimizations-check symbols-check simple-codegen-check codegen-check

parser: $(PARSER_EXAMPLES)
parser-graphviz: $(PARSER_GRAPHVIZ)

# Make sure the parser uses the -t flag instead of -T
parser/%.ast: PRINT_AST_OPTION := -t
parser/%.svg: PRINT_AST_OPTION := -t

optimizations: $(OPTIMIZATIONS_EXAMPLES)
optimizations-graphviz: $(OPTIMIZATIONS_GRAPHVIZ)

symbols: $(SYMBOLS_EXAMPLES)

simple-codegen: $(SIMPLE_CODEGEN_EXAMPLES)
simple-codegen-assemble: $(SIMPLE_CODEGEN_ASSEMBLED)

codegen: $(CODEGEN_EXAMPLES)
codegen-assemble: $(CODEGEN_ASSEMBLED)

%.ast: %.vsl $(VSLC)
	$(VSLC) $(PRINT_AST_OPTION) < $< > $@

%.svg: %.vsl $(VSLC)
	GRAPHVIZ_OUTPUT=1 $(VSLC) $(PRINT_AST_OPTION) < $< | dot -Tsvg -o $@

%.symbols: %.vsl $(VSLC)
	$(VSLC) -s < $< > $@

%.S: %.vsl $(VSLC)
	$(VSLC) -c < $< > $@

%.out: %.S
	gcc $< -o $@

clean:
	-rm -rf */*.ast */*.svg */*.symbols */*.S */*.out

parser-check: parser
	cd parser; \
	find * -wholename "suggested/*.ast" | awk -F/ '{print $$0 " " $$2}' | xargs -L 1 diff -s --unified=0
	@echo "No differences found in the parser!"

optimizations-check: optimizations
	cd optimizations; \
	find * -wholename "suggested/*.ast" | awk -F/ '{print $$0 " " $$2}' | xargs -L 1 diff -s --unified=0
	@echo "No differences found in optimizations!"

symbols-check: symbols
	cd symbols; \
	find * -wholename "suggested/*.symbols" | awk -F/ '{print $$0 " " $$2}' | xargs -L 1 diff -s --unified=0
	@echo "No differences found in symbols!"

simple-codegen-check: simple-codegen-assemble
	find simple-codegen -wholename "*.vsl" | xargs -L 1 ./codegen-tester.py
	@echo "No differences found in simple codegen!"

codegen-check: codegen-assemble
	find codegen -wholename "*.vsl" | xargs -L 1 ./codegen-tester.py
	@echo "No differences found in codegen!"
