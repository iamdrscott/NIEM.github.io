
.PHONY: valid
valid:
	xs-validate --catalog=sample-iepd/base-xsd/xml-catalog.xml full-example.xml

.PHONY: spell # check spelling
spell:
	@echo Any words that follow are bad...
	aspell --home-dir=. -p aspell-exceptions.txt list < index.md | LC_ALL=C sort -uf

